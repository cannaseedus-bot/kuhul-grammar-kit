# K'UHUL π Grammar Kit

Canonical EBNF for the **K'UHUL π** semantic runtime — version **π-1.2.0 + tensor-bundle-grounding/v8 + fold-algebra/v9**.

K'UHUL is a closed-cycle semantic execution language. It operates over semantic fields and a persistent topology B, with XCFE as the control authority and XulGate as the only topology commit boundary.

## What is K'UHUL?

K'UHUL is the semantic authority layer for the KHANARY stack: it defines what a program/state means, which phase it is in, which fold operations are legal, and when a proposed topology change may commit. Backends such as C++, HLSL, WGSL, Python, KLSL, KXC, KHLC, STB, and XSHARD are projections or containers around that meaning layer.

The practical answer is that K'UHUL is all of these, at different layers:

| View | Meaning in this kit |
|------|---------------------|
| Semantic execution language | The grammar defines phases, glyphs, fields, folds, authority boundaries, and valid transitions. |
| Geometric folding theory | Horizontal folds compose work inside a phase; vertical folds move across the Pop/Wo/Yax/Sek/Ch'en/Xul cycle; topology B only changes through Xul authority. |
| Runtime contract | XCFE supplies control/move legality, XulGate supplies commit authority, and WCR/B_tau admission scores determine whether a semantic field can execute. |
| File/serialization surface | `.kuhul`, EBNF, KXML/XCFE blocks, KAST/KSON, and related JSON/XML artifacts are ways to carry or validate K'UHUL meaning. |
| Style | The syntax has a recognizable authoring style, but style is only the visible surface of the semantic model. |

A short definition: **K'UHUL is a phase-based semantic geometry language for executable cognition.** It models meaning as phase-gated folds over a topology, then lowers that meaning into compiler, shader, tensor, and runtime artifacts.

More precisely, a K'UHUL fold is an **algebraic interpretation of a semantic neighborhood**, not just a container. `Book(k) ≡ N(k)` declares the neighborhood; the fold algebra `α : F(B) -> B` determines what that neighborhood means; phase `θ` locates that interpretation in the C6 cycle; Xul collapses the verified interpretation into canonical output. See `docs/FOLD_ALGEBRA.md`.

The semantic core is frozen as:

```text
K = (G_semantic, Θ, 𝒜, Π, X)
Θ = (P, W, Y, S, C, X)
𝒜 = (α_P, α_W, α_Y, α_S, α_C)
```

Xul is the sixth phase, **not** a sixth fold. Pop through Ch'en are the five semantic transformation algebras; Xul is the policy-governed canonicalization/commit boundary. Executors are heterogeneous realizations behind a common envelope: `E_θ(contract, input, context) -> (result, evidence, status)`. The contract never depends on a specific CPU, GPU, model, or SCXQ2 lowering path.

## Closed phase cycle

```
Pop → Wo → Yax → Sek → Ch'en → Xul → Pop'
```

| Phase | Role |
|-------|------|
| Pop | Semantic entry — gram store read |
| Wo | Declaration — vertical fold, tensor allocation |
| Yax | Admission gate — B_τ neighborhood, WCR attention |
| Sek | Execution — glyph dispatch, ΔB proposal |
| Ch'en | Verification — evidence check, miss classification |
| Xul | Commit — XCFE-authorized topology write |


## Tensor bundle grounding

K'UHUL now carries the same tensor-bundle grounding used by KHANARY.CPP:

- **Phase θ = Connection ∇** — the C6 phase cycle is the discrete, path-ordered connection rule.
- **Geodesic γ ≠ Connection ∇** — the geodesic propagator finds paths under the connection.
- **TensorSubstrate = Fiber bundle** — base coordinates remain separate from fiber/tensor payloads.
- **Gram G = tensor product rank** — multi-token grams are symbolic `V^⊗p` addresses.
- **Projection Π ≈ interior product ι** — projection observes/contracts; it does not manufacture semantic authority.

TENSOR-µ is projection-only. It may learn tensor-bundle geometry over verified FoldGraph/GML topology, but it cannot mutate semantic topology B or replace Ch'en/Xul authority.

## Fundamental laws

```
Fold ≠ Xul
Phase ≠ Opcode
Reachability ≠ Authority
Representation ≠ Authority
Vocabulary ≠ Authority
Usage ≠ Confidence
Proposal ≠ Commit
Verified ≠ Authorized
Relation-shaped data ≠ Topological relation

ΔB ≠ 0  ⟹  Xul  ∧  XCFE-authorized  ∧  CommitAuthority
```

## Files

| File | Description |
|------|-------------|
| kuhul.runtime-grammar.ebnf | Canonical K'UHUL π EBNF — π-1.2.0 plus tensor-bundle-grounding/v8. Includes bundle/manifold declarations, Phase θ as connection, geodesic-under-connection distinction, tensor product rank, and projection-only authority laws. |
| docs/FOLD_ALGEBRA.md | Canonical fold-as-algebra model: `Fold = meaning assigned to a semantic structure under an algebra`, with five fold algebras, Xul canonicalization, and heterogeneous executor contracts. |
| docs/TENSOR_BUNDLE.md | Mathematical grounding for Phase θ as connection, TensorSubstrate as fiber bundle, Gram G as tensor product rank, Fold F as cotangent-shaped, and Projection Π as interior-product-shaped observation. |
| 	ools/grammar/mini_transpilers.py | Source-side validator/transpiler referenced by the K'UHUL side-constraint block; defaults to the KHANARY.CPP repo layout. |
| `aiml.runtime-grammar.ebnf` | AIML runtime channel grammar — gram store channel bindings for ELIZA/ALICE response patterns. |
| `scfml.runtime-grammar.ebnf` | SCFML runtime grammar surface for semantic-control flow markup. |
| `micronaut.runtime-grammar.ebnf` | Micronaut runtime grammar for route contracts, profile projections, tensor slots, and semantic envelopes. |
| `cpp.code-grammar.ebnf` | C++ structural EBNF — namespace, class, function, member syntax. |
| `cpp.code-grammar.json` | C++ AST token grammar (JSON). Includes `slot` (`{{name}}`) and `lfm_marker` token types that map to gram store keys. |
| `typescript.code-grammar.json` | TypeScript AST token grammar (JSON). Same slot/lfm_marker additions as C++. |
| `klsl.code-grammar.json` | KLSL glyph code grammar (JSON). Covers the 11 GlyphOp values: MATMUL, CONV, ATTN, SOFTMAX, AFFINE, COMPRESS, SPHERICAL, TORSION, RADIAL, WAVE, NOP. |

## Key concepts

**Horizontal fold** — same-phase composition (Δθ=0, weight=+1.0, always authorized). `unfold(T) → work → fold(T)` may repeat without advancing the phase.

**Vertical fold** — phase transition (Δθ=π/3, weight=+0.5, requires XCFE authorization). Only adjacent single-step transitions are primitive.

**Semantic field F_Q** — working structure produced by unfolding a track. Not topology B. `field_accumulation` is observation; only an authorized Xul commit mutates B.

**CommitAuthority** — a runtime capability minted by XulGate. Move-only, single-use, non-copyable. No production in this grammar manufactures one.

**Math-µ / MathML** — activated by symbolic semantic relevance. Produces a MathML projection over semantic nodes. Does not mutate B.

**XCFE Node ISA** — 14-class closed set mapping operator names to canonical authority phases. CanonicalPhase is total; every xcfe_node_class has exactly one phase.

**WCR score** — S = W × C × R, all inputs clamped to [0,1]. W=static_weight, C=confidence, R=relevance (blended across 9 component scores). Admission threshold: 0.35.


## Related KUHUL compiler/grammar kits

KXC, KLSL, and KHLC are sibling compiler surfaces around the same K'UHUL semantic authority. Their KHANARY-V1 source surfaces live in [KHANARY-V1](https://github.com/cannaseedus-bot/KHANARY-V1.git), especially `kxc/`, `klsl/`, `khlc/`, and `grammar/`.

| Kit | Role |
|-----|------|
| [kxc-grammar-kit](https://github.com/cannaseedus-bot/kxc-grammar-kit) | Higher-level compiler/contract surface for KXC EBNF, ASX core references, registry classes, SCX/SCXQ2/JROM/XShard contracts, and trainer-adjacent source surfaces. |
| [xjson-grammar-kit](https://github.com/cannaseedus-bot/xjson-grammar-kit) | Semantic JSON/XCFE contract surface for model envelopes, gram stores, proof metadata, runtime limits, UI schemas, and client-side run/verify/hash/proof calls. |
| [klsl-grammar-kit](https://github.com/cannaseedus-bot/klsl-grammar-kit) | Shader/kernel lowering layer for `.kuhul` / `.klsl` sources, including forward/backward KLSL and generated HLSL specimens. |
| [khlc-grammar-kit](https://github.com/cannaseedus-bot/khlc-grammar-kit) | KHL/KUHUL compiler-admission surface that emits KAST/KSON artifacts for runtime validation. |

Treat KUHUL as the semantic authority, KXC as the higher-level contract/compiler surface, KLSL as the shader/kernel lowering layer, and KHLC as the KHL-to-KAST/KSON admission compiler.

## Part of

[KHANARY.CPP](https://github.com/cannaseedus-bot) — K'UHUL semantic runtime stack.
