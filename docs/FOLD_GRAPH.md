# Fold Graph

A **Fold Graph** is the typed relational graph exposed when a Fold/Book is unfolded. It is not a synonym for Fold Space, Fold Field, or the persistent global topology.

The primitive is:

```text
Unfold(h, τ) = { e | B_τ[h, e] ≠ 0 }
```

So:

```text
𝒢_F(h, τ, Q) = the query-conditioned graph exposed by unfolding Fold h
```

## Layer distinction

| Layer | Symbol | Meaning |
|---|---|---|
| Fold | `F_h` | bounded semantic structure |
| Fold Graph | `𝒢_F` | unfolded relational structure |
| Fold Space | `F_K` | all navigable Fold coordinates/states |
| Fold Field | `M_F` | relational/weighted field over that space |
| Fold Event | `E_F` | typed movement/change |

The Fold is bounded structure. The Fold Graph is what becomes operationally visible when that structure is unfolded.

## Handle → Fold Graph → Handle

The canonical traversal is:

```text
h
  --Unfold--> 𝒢_F(h)
  --Attention--> 𝒢_F^Q(h)
  --Fold--> C_h
```

This is the operational form of:

```text
Handle -> Unfold(T) -> Neighborhood -> Attention -> Fold(T) -> C_T
```

The neighborhood is what is contained. The Fold Graph is the relational structure formed when that neighborhood is unfolded.

## Active Fold Graph

Yax does not unfold the whole universe. Starting from the structural Fold Graph:

```text
𝒢_F(h)
```

Yax applies the query gate:

```text
m_h = Gate_Q(B_τ[h,*], W_E)
B_h_active = B_τ[h,*] ⊙ m_h
```

The result is the **Active Fold Graph**:

```text
𝒢_F^Q(h) = Active Fold Graph
```

Thus:

```text
𝒢_F(h) --Yax / Gate_Q--> 𝒢_F^Q(h)
```

Sek operates on the Active Fold Graph, not on the persistent topology directly:

```text
A_h_active = B_h_active W_E (B_h_active)^T
```

## Graph vs Field vs Space

```text
𝒢_F = (V, E, B)
```

The Fold Graph answers: **what is connected here?**

```text
M_F = (𝒢_F, W, θ, tensor/state fields, ...)
```

The Fold Field answers: **what pressures, weights, and relations exist over those connections?**

Fold Space answers: **where can the runtime be?**

Operationally, the zoom levels are:

```text
Fold Space
  ⊃ Fold Field
  ⊃ Fold Graph
  ⊃ Active Fold Graph
```

This is a runtime zoom relation, not necessarily strict set-theoretic containment.

## Word-for-book chain

```text
Token
  -> Handle
  -> Book
  -> Fold Graph
  -> Active Fold Graph
  -> Fold
```

Operational form:

```text
h
  --Book(h)--> N(h)
  --Unfold--> 𝒢_F(h)
  --Yax/Q--> 𝒢_F^Q(h)
  --Sek--> R
  --Ch'en--> V
  --Fold--> C_h
  --Xul/Π--> h'
```

## Hard invariants

```text
Unfold does not create semantic relationships.
Fold does not destroy the graph.
```

Unfold reveals/resolves the Fold Graph already represented by Fold topology. Fold returns the active expanded working structure to a bounded representation. Only the verified/authorized Xul path can persist a topology delta.

Canonical statement:

> Fold Graph `𝒢_F(h)` is the typed relational graph exposed by unfolding an addressable Fold/Book. Yax derives an Active Fold Graph `𝒢_F^Q(h)` from it for the current query; Fold contracts that working graph back into a bounded semantic unit without altering persistent topology.
