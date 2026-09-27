# K'UHUL π Grammar Kit

Canonical EBNF for the **K'UHUL π** semantic runtime — version **π-1.2.0**.

K'UHUL is a closed-cycle semantic execution language. It operates over semantic fields and a persistent topology B, with XCFE as the control authority and XulGate as the only topology commit boundary.

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
| `kuhul.runtime-grammar.ebnf` | Canonical K'UHUL π EBNF — π-1.2.0. Sections 1–23 define the language; sections 24–29 cover the implementation layer (XCFE Node ISA, KLSL glyphs, gram store, bracket programs, WCR/B_τ, NGram pipeline). SC-1..SC-20 side constraints at the bottom. |
| `aiml.runtime-grammar.ebnf` | AIML runtime channel grammar — gram store channel bindings for ELIZA/ALICE response patterns. |
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

## Part of

[KHANARY.CPP](https://github.com/cannaseedus-bot) — K'UHUL semantic runtime stack.
