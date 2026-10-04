# K'UHUL Fold Algebra

A K'UHUL semantic fold is not merely a container of related nodes and not merely a reduction over similar tokens. The stronger canonical model is an **interpretation algebra**:

```text
Fold = meaning assigned to a semantic structure under an algebra
```

Suppose a semantic object has signature:

```text
F = { token, relation, operator, phase, collapse }
```

The object does not by itself determine what those constructors ultimately mean. A particular fold supplies that interpretation:

```text
α : F(B) -> B
Fold_α : μF -> B
```

The same semantic structure can therefore be interpreted differently without rewriting the structure. For example:

```text
CAUSE
 ├─ why
 ├─ because
 ├─ INFER
 ├─ COUNTEREXAMPLE
 └─ Sek
```

Different target algebras can read that same structure as:

| Algebra | Interpretation |
|---|---|
| reasoning | infer a causal explanation |
| retrieval | retrieve causal evidence |
| compression | canonical symbol `⟁CAUSE` |
| execution | `XCFE:INFER -> executor` |

The semantic object did not change. The target algebra changed.

## Word -> Book, algebraically

The manifest symbol stands for an algebraically meaningful neighborhood:

```text
Book(k) ≡ N(k)
```

A fold does not merely iterate the members of `N(k)`. It interprets that neighborhood according to the active algebra.

## Horizontal and vertical folds

The runtime graph is a product graph:

```text
G_runtime = G_semantic □ G_phase
v = (k, θ)
```

A horizontal fold preserves phase while changing the semantic neighborhood being interpreted:

```text
(k, θ) -> (u, θ)
```

A vertical fold preserves semantic identity while changing the phase interpretation:

```text
(k, θ_i) -> (k, θ_i+1)
```

The closed C6 cycle has six phases, but **Xul is the sixth phase, not a sixth fold**. Five semantic fold algebras transform state; Xul is the boundary/canonicalization operation that closes the cycle.

The frozen semantic core is:

```text
K = (G_semantic, Θ, 𝒜, Π, X)
Θ = (P, W, Y, S, C, X)
𝒜 = (α_P, α_W, α_Y, α_S, α_C)
```

Execution law:

```text
k
 -> N_G(k)
 -> α_P -> B_P
 -> α_W -> B_W
 -> α_Y -> B_Y
 -> α_S -> B_S
 -> α_C -> B_C
 -> X_Π -> k'

k'_t -> P_(t+1)
```

### Fold phases are operations over a Fold

The six phases do not define six kinds of fold. They define operations over the same folded semantic structure:

```text
F_h --Pop--> F_h --Wo--> F_h --Yax--> F_h --Sek--> F_h --Ch'en--> F_h --Xul--> F_h'
```

Typed phase operators:

```text
T_Pop   : perceive / activate
T_Wo    : establish state / constraints
T_Yax   : admit relevant neighborhood
T_Sek   : execute operation
T_Ch'en : verify / produce certificate
T_Xul   : collapse / commit
```

The phase-wheel matrix satisfies `P^6 = I`, but `P` only moves the phase pointer. `T_φ` performs the semantic operation.

### Full runtime coordinate system

The execution-level state `(F_h, φ_t, X_t)` is necessary but not sufficient for semantic routing and learning. The full coordinate system is:

```text
𝒦_t = (F_h, φ, T, s, σ, X_t)
```

| Coordinate | Meaning |
|---|---|
| `F_h` | WHAT structure: fold/hypergraph structure |
| `φ` | WHERE/WHEN in execution: C6 phase |
| `T` | WHICH semantic space or track is active |
| `s` | BOUNDED or EXPANDED: folded/unfolded state |
| `σ` | HOW LEARNED/TRUSTED: N-Gram Burner learning/binding state |
| `X_t` | CURRENT mutable runtime state |

Fold/unfold is an independent axis:

```text
Handle -> unfold(T) -> Neighborhood -> Attention -> fold(T) -> C_T
```

`Fold ≠ Xul`: folding changes `s`; Xul is the only phase that may produce `F_h -> F_h'`.

The N-Gram Burner therefore does not learn "six folds." It learns behavior within semantic fold structures while those structures are operated on at particular phases:

```text
τ = (F_h, φ, T, s, Q, input, operation, output, evidence)
```

Example observation:

```text
FOLD       CAUSE
TRACK      REASON-µ
PHASE      Sek
STATE      unfolded
INPUT      admitted causal neighborhood
OPERATION  XCFE:INFER
OUTPUT     causal candidate
```

Then `fold(REASON-µ)` compresses the learned working context back into `C_T`.

### Fold Graph layer

The Fold Graph is the typed relational graph exposed by unfolding a Fold/Book:

```text
Unfold(h, τ) = { e | B_τ[h, e] ≠ 0 }
𝒢_F(h, τ, Q) = the query-conditioned graph exposed by unfolding Fold h
```

Yax derives the Active Fold Graph for the current query:

```text
𝒢_F(h) --Yax / Gate_Q--> 𝒢_F^Q(h)
```

Unfold reveals structure; it does not create semantic relationships. Fold returns the active expanded graph to a bounded representation; it does not destroy the graph. Folding is recoverable contraction: `Unfold(Fold(𝒢_F)) ≃ 𝒢_F` under the Fold contract. Only Xul can persist a topology delta. See `FOLD_GRAPH.md`.

For the µN-RAG reasoning pipeline:

```text
α_P : F(B) -> B_trigger       Pop
α_W : F(B) -> B_state         Wo
α_Y : F(B) -> B_candidate     Yax
α_S : F(B) -> B_operation     Sek
α_C : F(B) -> B_observation   Ch'en
X_Π : B_C -> k'               Xul canonicalization/commit boundary
```

Phase angles provide geometry over relationships between interpretations:

```text
θ = { 0, pi/3, 2pi/3, pi, 4pi/3, 5pi/3 }
R_θ(a,b) = cos(θ_a - θ_b)
```

They do not make something a fold. They locate interpretations in the K'UHUL cycle.

## Layer boundary

| Layer | Meaning |
|---|---|
| Signature `F` | Which semantic constructors and relations exist |
| Fold/algebra `α` | What those structures mean |
| Phase `θ` | Where that interpretation lies in the C6 cycle |

SCX adds symbolic compression of the interpreted neighborhood. It is still not semantic authority.

## Canonical statement

> A K'UHUL semantic fold is an algebraic interpretation of a semantic neighborhood. It maps a structure from its canonical signature into a target semantic domain while preserving declared relations. Phase determines the interpretive position of that fold; Xul collapses the resulting interpretation into canonical output.

Compressed notation:

```text
N(k) --α / θ--> B --Xul--> k'
```

The grouping is the neighborhood. **The fold is what the neighborhood means.**

## Executor contract layer

The frozen core intentionally does **not** define a universal `Execute(Fold)` interface. Semantic contracts are heterogeneous, so executor capabilities are heterogeneous too:

```text
E_θ : (contract, input, context) -> (result, evidence, status)
```

The common surface is the envelope, not the computation:

```text
EXECUTOR_REQUEST
  phase
  fold
  input
  context
  policy
  capabilities
  representation?

EXECUTOR_RESULT
  phase
  fold
  output
  evidence
  confidence
  status
  diagnostics
```

Phase requirements:

| Phase | Semantic contract | Executor must provide |
|---|---|---|
| Pop | Activation | Resolve `N_G(k)` and activate the relevant semantic field |
| Wo | Constraint | Evaluate constraints and identify keep/reject state |
| Yax | Candidate expansion | Produce candidate interpretations and evidence/weights |
| Sek | Operation | Execute the selected XCFE operation through a deterministic or model-backed executor |
| Ch'en | Observation | Convert operation results into normalized observations |
| Xul | Canonicalization | Apply `Π` to observations and return canonical result/status |

Rule:

```text
Executor implements contract; contract does not implement executor.
```

The connective object between the semantic fold and the executor is the **Fold Binding**: it anchors `(fold, phase, semantic)`, targets `(backend, entry)`, and carries contract, dispatch, resources, and fallback. See `FOLD_BINDING.md`.

A Micronaut does not have to be the fold. It advertises realized fold contracts:

```text
CODE-µ
  realizes:
    Yax.candidate
    Sek.CODE
    Ch'en.observation

REASON-µ
  realizes:
    Yax.candidate
    Sek.INFER
    Sek.COMPARE
    Sek.COUNTEREXAMPLE
    Ch'en.observation
```

Runtime selection can use the existing score:

```text
S_μ = W_μ * C_μ * R_μ(Q)
```

while the semantic operation remains `α_S`:

```text
α_S --resolve--> E_μ --execute--> B_S
```

Changing from `REASON-µ` to a deterministic `COMPARE` executor does not change the K'UHUL program.

## Representation and substrate independence

SCXQ2 sits between representation and executor when compression/lowering is useful:

```text
α_θ -> Rep(α_θ) -> SCXQ2 -> E_θ
```

It is bypassed when it is not useful:

```text
α_θ -> E_θ
```

A GPU realization may lower through tensor representation, SCXQ2 lanes, KXC, HLSL, and D3D11. A CPU realization may satisfy the same `α_S` contract through a different path. The complete separation is:

```text
G -> N_G(k) -> 𝒜 -> X_Π      K'UHUL semantic law
              -> Rep(𝒜)      optional representation
              -> E           executor contract
              -> CPU/GPU/model/etc. replaceable substrate
```

Canonical statement: **K'UHUL is a semantic algebra over a graph with phase-indexed folds and policy-governed canonicalization; representation and physical realization remain replaceable.**

```text
A fold is the semantic transformation. Everything after that is how you choose to realize it.
```
