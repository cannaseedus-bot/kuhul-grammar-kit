# GPU Folds — D3D11 Compute Pipeline

KHANARY splits inference from scoring:

- **CPU (AVX2)** — µ-model token generation via llama-server (LFM2.5-1.2B / gemma-3-1B)
- **GPU (D3D11 CS 5.0)** — gram/fold/semantic scoring via compute shaders

Target hardware: Intel HD 4600 (Haswell GT2), DirectX 11.1, Compute Shader 5.0.

---

## Shaders

### `data/shaders/attention.hlsl`

Semantic attention kernel. Not transformer QKV — scores symbolic gram records.

```
Input  (t0): StructuredBuffer<SemanticRecord>  — stride 48 bytes (12 floats)
Output (u0): RWStructuredBuffer<float>  BaseScore
Output (u1): RWStructuredBuffer<float>  SemanticScore
Output (u2): RWStructuredBuffer<uint>   LegalMask
Output (u3): RWStructuredBuffer<uint>   AdmissionMask
Cbuffer(b0): SemanticAttentionConstants
  uint  record_count
  float admission_threshold
  float relation_mix        // [0,1] blends geometry modifiers into score
  float pad0
```

**Score law:** `S = W · C · R`  
**Geometry layer:** `semantic = S · lerp(1, rel·fold·phase, relation_mix)`  
**Legal gate:** `policy_ok ≥ 0.5 AND capable ≥ 0.5 AND xcfe_ok ≥ 0.5`  
**Admission:** `legal AND semantic ≥ threshold`

`[numthreads(64, 1, 1)]`, entry point `main`.

### `data/shaders/fold_compute.hlsl`

Elementwise weighted sum of two semantic buffers under arc weight.

```
Input  (t0/t1): two float buffers (node_a, node_b)
Output (u0):    result = arc_weight * (a + b)
Cbuffer(b0): FoldConstants { seq_len, d_model, arc_weight, pad }
```

`[numthreads(64, 1, 1)]`, entry point `main`.

---

## GPU Layer (`src/gpu/`)

### `D3D11Device`

Initializes the D3D11 device and context. Selects the adapter with the most dedicated VRAM by default; pass a hint string to select by name. HD 4600 is the default on this rig.

### `TensorBuffer`

`D3D11_RESOURCE_MISC_BUFFER_STRUCTURED` backed buffer. Configured via `TensorDesc { name, format, elem_count, stride_bytes }`. Creates both SRV and UAV; includes a staging buffer for CPU readback. `stride_bytes = 48` matches `SemanticRecord`.

### `ComputeDispatch`

Wraps shader compilation (`set_shader(blob)`), cbuffer update (`set_constants`), and dispatch (`run`, `run_for_elements`). Manages a single `ID3D11ComputeShader` slot — not safe for concurrent callers; each scorer owns its own shader.

### `DMLOps`

Higher-level ops. GPU GEMM (tiled 16×16 via embedded HLSL, falls back to CPU). `static semantic_attention_gate(W, C, R)` — the CPU scalar version of the attention score law.

### `GramGpuScorer`

Bridges `kuhul::GramStore` to `attention.hlsl`.

**`init()`** — compiles the embedded attention shader via `D3DCompile()`.

**`score_batch(store, keys, relevance, threshold, relation_mix)`**:

1. For each candidate key: reads the gram, maps channel → W and authority → C
2. Packs `gramVector(key)` geodesic coordinates into geometry fields:
   - `fold_weight`     = vector magnitude (geodesic reach from manifold origin)
   - `relation_weight` = equatorial component `√(x²+y²) / mag`
   - `phase_weight`    = vertical component `0.5 + 0.5·(z / mag)`
3. Uploads `SemanticRecord[]` to GPU (stride 48), dispatches attention kernel
4. Reads back four output channels, returns `vector<GramScoreResult>` sorted admitted-first

**Channel → W mapping:**

| Channel | W |
|---|---|
| `similarity_kernel` | 0.90 |
| `xcfe_control_program` | 0.85 |
| `predication_sequence` | 0.75 |
| `taxonomy_geometry` | 0.65 |
| `general` | 0.50 |

**Authority → C mapping:**

| Authority | C |
|---|---|
| `xcfe_control_program` | 0.90 |
| `TOOL-µ` | 0.80 |
| `ELIZA` | 0.75 |
| `Xul` | 0.70 |
| _(default)_ | 0.60 |

---

## CPU / GPU Split Rationale

The Haswell ggml-opencl backend requires OpenCL 2.0 at runtime and drops HD 4600 (CL 1.2 only). This is not a bug — it falls back to AVX2 automatically. AVX2 delivers ~1140 ms/reply for LFM2.5-1.2B, which is fast enough.

The D3D11 compute path is viable because HD 4600 has full Compute Shader 5.0. Gram/fold scoring is arithmetic-light (multiply-add per element) and naturally parallel across many candidate records — a good fit for the GPU even without OpenCL.

```
Query
  │
  ├─ GramGpuScorer.score_batch()   ← GPU: attention.hlsl  (D3D11 CS 5.0)
  │    ↓ GramScoreResult[]
  │
  └─ llama-server (port 17889/17890) ← CPU: AVX2 + FMA (llama.cpp static)
       ↓ token stream
```

The two pipelines are serial at the request level (GPU assembles context → CPU generates), which keeps the design simple and avoids synchronization overhead.

---

## Build Notes

- llama-server: `tools/build_mu_server.bat` — Ninja + VS2022 BuildTools, AVX2/FMA/F16C, static, inference-only (`LLAMA_BUILD_UI=OFF`)
- Output: `build/llama-haswell/bin/llama-server.exe` (~18.7 MB static)
- KHANARY binary: CMake + MSVC 2022, links `d3d11.lib d3dcompiler.lib dxguid.lib dxgi.lib`
- Shader compilation: `D3DCompile()` at runtime, `cs_5_0` target, `O3`
