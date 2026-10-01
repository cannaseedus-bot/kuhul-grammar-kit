# K'UHUL Tensor Bundle Grounding

**The single most important distinction in this document:**
> Phase θ is the **connection** — the rule that prescribes parallel transport between
> fibers. `dispatch_geodesic_propagator()` finds **geodesics** — paths selected by that
> rule. They are different objects at different levels. The connection is the rule;
> the geodesic is the path the rule selects.

---

## 1. Formal definition

A **tensor bundle of type (p, q)** over a smooth manifold M is the vector bundle
whose fiber at each point x ∈ M is

```
T^p_q M|_x  =  (T_x M)^⊗p  ⊗  (T_x*M)^⊗q
```

where T_xM is the tangent space at x and T_x*M is its dual (the cotangent space).
The **full tensor algebra bundle** is the direct sum over all non-negative integers:

```
T(M)  =  ⊕_{(p,q) ∈ ℕ×ℕ}  T^p_q M
```

A **connection** ∇ on a vector bundle E → M is an ℝ-bilinear map

```
∇ : Γ(TM) × Γ(E) → Γ(E),   (X, s) ↦ ∇_X s
```

satisfying C∞(M)-linearity in X and the Leibniz rule in s.
In local coordinates, ∇ is encoded by its **connection coefficients** Γ^b_ia
(Christoffel symbols): ∇_{∂_i} e_a = Γ^b_ia e_b.

A **geodesic** under ∇ is a curve γ : I → M satisfying

```
∇_{γ'} γ' = 0
```

The tangent vector γ' is parallel-transported along γ — the curve "goes straight"
under the connection. Geodesics are selected BY the connection, not identical to it.

---

## 2. Precise K'UHUL mappings

These are structurally identical, not analogies.

### 2.1  Phase θ = Connection ∇  (precise)

The C6 phases sit at angles 0, π/3, 2π/3, π, 4π/3, 5π/3 on S¹. The arc weight

```
arc_weight(from, to) = cos(Δθ)
```

is the pullback of the standard Riemannian connection on S¹ to the discrete phase
lattice. For a principal U(1)-bundle with connection 1-form A = dθ, parallel
transport along a path with integrated curvature Δθ gives holonomy factor
e^{iΔθ}, whose real projection is cos(Δθ). That is exactly `arc_weight`.

**Why phases cannot be reordered** is a holonomy constraint, not a design choice.
The holonomy around a loop depends on path order; reversing Pop → Wo → Sek gives
a different holonomy than Sek → Wo → Pop. XCFE enforces |Δphase| = 1 for vertical
folds because the local connection coefficients are only defined for adjacent fibers.
Non-adjacent transitions would require "jumping" the connection — undefined.

Implementation: `src/kuhul/phase_engine.h`, `arc_weight` in `src/world/field_dag.cpp`.

### 2.2  TensorSubstrate = fiber at a base point  (precise)

```
Fiber bundle E → M:
  Total space E     =  all (base_point, fiber_tensor) pairs
  Base space M      =  SVG-3D plane
  Projection π: E→M =  (x, y, z) extraction from FieldNode
  Fiber F = π⁻¹(x)  =  TensorSubstrate at that node
```

Concretely:

```
FieldNode.x, y, z           base manifold coordinate
TensorSubstrate.shape       fiber dimension {N, stride_floats}
TensorSubstrate.residency   HOST_RAM → GPU_VRAM_D3D11 after first dispatch
TensorSubstrate.provider_identity  names the fiber family (expert cluster)
```

After `load_gml("brain4/brain.gml")`, `FieldDag::substrates_` holds 61 entries
(one per expert cluster), each a HOST_RAM descriptor with unpopulated shape.
After `dispatch_geodesic_propagator()`, shape is set to {N, sizeof(GpuFieldNode)/4}
and residency is GPU_VRAM_D3D11 — the fiber is promoted onto the device.

Implementation: `src/world/tensor_substrate.h`, `src/world/field_dag.cpp`.

### 2.3  Gram G = tensor product rank  (precise)

Gram is indexed by ordered token tuples of length p. A tuple (t₁, t₂, …, tₚ)
is an element of the free tensor algebra on the token vocabulary V — exactly
the basis of V^⊗p. Multi-token Gram keys ARE the basis vectors of the
rank-p contravariant tensor space.

Implementation: `src/kuhul/gram_store.h`, `src/kuhul/gram_store.cpp`.

---

## 3. Structural mappings

These are coherent at the algebra level but not operator-identical.

### 3.1  Fold F ≈ cotangent bundle V*

The cotangent space T_x*M at a point is the space of linear functionals on T_xM.
The fold `arc_weight = cos(Δθ)` takes a phase direction (a tangent vector) and
returns a scalar — that IS a cotangent-shaped operation. But the fold engine
operates as a bounded evaluation context (horizontal_fold, vertical_fold),
not as a continuous linear functional space. The mapping holds at the algebra
level (fold takes a direction, returns a weight) but not at the operator level
(the fold engine is not T*M).

### 3.2  Projection Π ≈ interior product ι  (NOT exterior derivative d)

The exterior derivative d: Ω^k(M) → Ω^(k+1)(M) increases form rank and is
antisymmetric. It applies to differential forms, not vectors.

The interior product ι_X: Ω^k(M) → Ω^(k-1)(M) contracts a k-form with a
vector field X, lowering rank by one:

```
(ι_X ω)(Y₁, …, Y_{k-1}) = ω(X, Y₁, …, Y_{k-1})
```

`project_v()` projects a state vector onto the field — it does not increase
rank, and it contracts a vector with a field structure. This is ι-shaped.
The antisymmetric observation is correct (ι is antisymmetric in the same sense),
but the rank behavior of d is wrong.

**Correction:** Π ≈ ι_v (interior product with the projection vector v), not d.

---

## 4. The geodesic propagator as connection computation

The HLSL shader `geodesic_propagator.hlsl` computes, for each node i:

```
V_i = -∇ρ_i + β⁻¹ · eh_i           (relevance gradient + entropic force)
S_ij = ½|V_i|² · L_ij               (action along edge ij)
amplitude_ij = exp(-S_ij) · ...      (path amplitude)
H_G_i = -log(Σ_j amplitude_ij)      (geodesic entropy at node i)
```

This is a discrete approximation of the geodesic equation ∇_{γ'}γ' = 0
in the entropy metric. The path amplitudes are the holonomy factors for
parallel transport along each edge under the connection defined by the
entropy gradient field ∇ρ. Paths that minimize the action S are the
geodesics under that connection.

`dispatch_geodesic_propagator()` does not *define* the connection — the
connection is given by the phase cycle and the entropy gradient field
(already on the nodes from `load_gml()`). The propagator *evaluates* the
geodesic structure of the field under that pre-existing connection.

---

## 5. Which (p,q) tensors are instantiated

The tensor bundle is the direct sum over all (p,q) pairs:

```
Tensor Bundle = ⊕_{(p,q)∈ℕ×ℕ} V^⊗p ⊗ (V*)^⊗q
```

| (p,q) | type | mathematical object | K'UHUL implementation | status |
| --- | --- | --- | --- | --- |
| (0,0) | scalar | function f: M → ℝ | `local_entropy`, `geodesic_entropy` | explicit |
| (1,0) | vector | tangent vector | `FieldNode.x/y/z`, `VectorMuMicronaut::projection_vector` | explicit |
| (0,1) | covector | cotangent vector (1-form) | `arc_weight` direction (structural) | structural |
| (1,1) | linear map | endomorphism T: V → V | `Fold F` (structural) | structural |
| (2,0) | bivector | wedge V ∧ V | not instantiated | missing |
| **(0,2)** | **bilinear form** | **metric tensor g_{ij}** | **`MetricTensor::euclidean()` — Phase 1 complete** | **explicit** |
| (2,1) | structure constants | torsion tensor | not instantiated | missing |
| **(1,2)** | **connection coefficients** | **Christoffel symbols Γ^k_ij** | **`ChristoffelSymbols::from_conformal_gradient()` — Phase 2 complete** | **explicit** |
| (3,0) | trivector | volume form | not instantiated | missing |
| (p,0) | rank-p contravariant | V^⊗p basis | Gram multi-token key of length p | explicit |
| (N,k) | fiber tensor | fiber at base point | `TensorSubstrate.shape` | explicit |

### Phase 1 status (complete)

`MetricTensor::euclidean()` is the explicit (0,2) tensor. Conformance:
17/17 cases pass (`KHANARY_METRIC_TENSOR_TEST`), including all 36 phase-pair
arc_weight equivalences. Canonical formula in `phase_engine.h` is unchanged;
`MetricTensor::arc_weight()` is the derived form that proves equivalence.

Antipodal pairs on C6: Pop↔Sek, Wo↔Chen, Yax↔Xul (Δθ = π, cos = -1).
Adjacent step (Δθ = π/3, cos = 0.5) is the only vertical fold weight.

### Phase 2 status (complete)

`ChristoffelSymbols::from_conformal_gradient()` is the explicit (1,2) tensor.
Conformance: 19/19 cases pass (`KHANARY_CHRISTOFFEL_TEST`).

The conformal metric `g_ij(x) = e^{2Φ(x)} δ_ij` is constructed via
`MetricTensor::conformal_from_entropy(float phi)`, which delegates to
`diagonal(e^Φ, e^Φ, e^Φ)` so `g[i][i] = e^{2Φ}`.

For `g = I` (flat Euclidean, Φ = 0), all Christoffel symbols are zero —
the flat-limit test confirms this is the correct degenerate case.

For a position-dependent entropy field Φ(x), the Christoffel symbols are:

```
Γ^k_ij = δ^k_j ∂_i Φ + δ^k_i ∂_j Φ − δ_ij ∂_k Φ
```

This formula requires only ∇Φ — the conformal factor e^{2Φ} cancels with the
inverse metric. The connection is torsion-free (Levi-Civita): Γ^k_ij = Γ^k_ji.

Angle-preservation: conformal maps scale lengths by e^{2Φ} but preserve angles.
`arc_weight(a, b)` returns `e^{2Φ} cos(Δθ)` (raw inner product); the normalized
ratio `g(e_a, e_b) / g(e_a, e_a) = cos(Δθ)` is invariant across all Φ.

`MetricTensor::diagonal(sx, sy, sz)` is available for the simpler case where
the three axes have physically different scales (e.g., time vs. frequency in
brain4 before normalization).

### Phase 3 (design deferred)

Riemann tensor R^i_jkl, Ricci tensor R_ij, and scalar curvature R all derive
from Christoffel symbols. For the conformal metric over a 3D flat base:

```
R = -2 e^{-2Φ} [ ΔΦ + |∇Φ|² ]
```

Deferred until a downstream consumer needs curvature measurements.

---

## 6. The one-sentence definition (corrected)

> K'UHUL is a computational implementation of tensor bundle calculus over the
> SVG-3D base manifold M, where Phase θ is the connection ∇ (the prescription
> for parallel transport between fibers), `dispatch_geodesic_propagator()` finds
> geodesics under ∇ (paths minimising entropy growth), TensorSubstrate is the
> fiber bundle (each node's shape and residency), Gram G is the tensor product
> rank (multi-token keys are rank-p contravariant tensors), Fold F is
> structurally cotangent-shaped at the algebra level, and Projection Π is
> closer to interior product ι than to exterior derivative d.

---

## 7. Code pointers

| concept | file | symbol |
| --- | --- | --- |
| Connection (C6 arc_weight) | `src/kuhul/phase_engine.h` | `arc_weight()` |
| (0,2) metric tensor | `src/world/metric_tensor.h` | `MetricTensor` |
| (1,2) Christoffel symbols | `src/world/christoffel.h` | `ChristoffelSymbols` |
| Fiber bundle descriptor | `src/world/tensor_substrate.h` | `TensorSubstrate` |
| Fiber population (load_gml) | `src/world/field_dag.cpp` | `load_gml()` |
| Geodesic computation | `data/shaders/geodesic_propagator.hlsl` | `CSMain` |
| Geodesic dispatch | `src/world/field_dag.cpp` | `dispatch_geodesic_propagator()` |
| Gram rank-p tensors | `src/kuhul/gram_store.h` | `GramStore` |
| Projection operator | `src/world/vector_projection.cpp` | `load_gml_topology_vectors()` |
| VectorMuMicronaut (ι) | `src/micronaut/vector_mu.h` | `ExecuteProjection()` |
