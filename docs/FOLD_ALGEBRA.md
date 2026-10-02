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

The closed C6 cycle is therefore six interpretive positions applied during one semantic catamorphism:

```text
Pop -> Wo -> Yax -> Sek -> Ch'en -> Xul -> Pop
```

For the µN-RAG reasoning pipeline:

```text
α_Pop   : F(B) -> B_trigger
α_Wo    : F(B) -> B_state
α_Yax   : F(B) -> B_candidate
α_Sek   : F(B) -> B_operation
α_Ch'en : F(B) -> B_observation
α_Xul   : F(B) -> B_collapse
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
