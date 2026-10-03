# Fold Binding Grammar

A **Fold Binding** connects a semantic fold at a phase to a concrete executor. It sits between the fold (semantic) and the executor (physical). It is what the Binding Resolver produces, what the Executor consumes, and what the Fold Kernel carries.

The grammar has three levels:

1. **Concrete** — JSON shape.
2. **Abstract** — semantic structure independent of encoding.
3. **Operational** — what the binding does.

The standalone concrete grammar is `fold-binding.ebnf`.

---

## 1. Concrete grammar over JSON

The concrete grammar specifies what a binding record may look like, not whether it is valid.

```ebnf
Binding ::= {
    "@fold"      : FoldId ,
    "@phase"     : Phase ,
    "@semantic"  : String ,

    "@backend"   : Backend ,
    "@entry"     : EntryId ,

    "@dispatch"  : Dim3 | {
        "@dims"     : Dim3? ,
        "@bounds"   : { "@min" : Int , "@max" : Int }? ,
        "@strategy" : "fixed" | "dynamic" | "adaptive"?
    }? ,

    "@contract"  : {
        "@pre"       : Expr ,
        "@post"      : Expr ,
        "@invariant" : [Expr]? ,
        "@errors"    : [ErrorDecl]?
    } ,

    "@resources" : {
        "@threads"    : Int? ,
        "@shared"     : Int? ,
        "@registers"  : Int? ,
        "@timeout_ms" : Int?
    }? ,

    "@fallback"  : {
        "@target"    : Backend ,
        "@condition" : Expr
    }?
}

Phase ::= "Pop" | "Wo" | "Yax" | "Sek" | "Ch'en" | "Xul"

Backend ::= "hlsl" | "cuda" | "metal" | "wgsl" | "spirv"
          | "cpp" | "rust" | "wasm" | "scxq2" | "host-llm"
          | CustomBackend

ErrorDecl ::= { "@code" : String , "@condition" : String }
```

---

## 2. Abstract grammar

The abstract grammar captures what the binding is independent of JSON encoding:

```ebnf
Binding ::= ⟨
    anchor    : Anchor,
    target    : Target,
    dispatch  : Dispatch,
    contract  : Contract,
    resources : Resources?,
    fallback  : Fallback?
⟩

Anchor ::= ⟨ fold : FoldId, phase : Phase, semantic : SemanticTag ⟩
Target ::= ⟨ backend : Backend, entry : EntryId ⟩

Dispatch ::= Fixed(Dim3)
           | Dynamic(Bounds, Strategy)
           | Adaptive(Bounds, Strategy)

Contract ::= ⟨
    pre        : Predicate,
    post       : Predicate,
    invariants : [Predicate],
    errors     : [ErrorDecl]
⟩

Resources ::= ⟨
    threads    : Nat?,
    shared     : Nat?,
    registers  : Nat?,
    timeout_ms : Nat?
⟩

Fallback ::= ⟨ target : Backend, condition : Predicate ⟩
```

---

## 3. Contract expression language

Contracts use a small predicate language over input and output types:

```ebnf
Expr        ::= Term (("∧" | "∨") Term)*
Term        ::= Predicate | "¬" Predicate | "(" Expr ")"
Predicate   ::= Comparison | Membership | Shape
Comparison  ::= Value ("<" | "<=" | "=" | ">=" | ">") Value
Membership  ::= Value "∈" Set
Shape       ::= "shape(" Var ")" "=" ShapeLit
ShapeLit    ::= "[" Dim ("," Dim)* "]"
Dim         ::= Nat | "?" | Var
Value       ::= Var | Number | String
Var         ::= Identifier ("." Identifier)*
Set         ::= "{" Value ("," Value)* "}"
```

The contract can reference:

- Input variables: `input.*`, `N`, `k`, `phase`.
- Output variables: `output.*`, `B`, `confidence`.
- Shape predicates: `shape(input)`, `shape(output)`.
- Range predicates: `input.range`, `output.range`.
- Invariants: expressions that must hold before and after execution.

### Phase contract examples

Pop activation:

```json
{
  "@pre":  "shape(input) = [N] ∧ phase = Pop",
  "@post": "shape(output) = [N] ∧ Σ output = 1"
}
```

Wo constraint:

```json
{
  "@pre":  "shape(input) = [N] ∧ phase = Wo",
  "@post": "shape(output) = [N] ∧ output ⊆ input ∧ |output| ≤ |input|"
}
```

Yax candidate expansion:

```json
{
  "@pre":  "shape(input) = [N] ∧ phase = Yax",
  "@post": "shape(output) = [N, C] ∧ C ≥ 1"
}
```

Sek operation:

```json
{
  "@pre":  "shape(input) = [N, C] ∧ phase = Sek",
  "@post": "shape(output) = [N, C] ∧ operator_applied(output, input)"
}
```

Ch'en observation:

```json
{
  "@pre":  "shape(input) = [N, C] ∧ phase = Ch'en",
  "@post": "shape(output) = [N] ∧ output = reduce(input)"
}
```

Xul canonicalization:

```json
{
  "@pre":  "shape(input) = [N] ∧ phase = Xul",
  "@post": "shape(output) = [] ∧ output ∈ candidates(input)"
}
```

Each contract is checkable. An executor is valid for a phase iff its entry point satisfies the declared pre/post conditions.

---

## 4. Binding validity

A binding is valid iff the executor behavior satisfies the declared contract:

```text
Binding ⊨ Contract
```

Validity rules:

```ebnf
V1 ::= anchor.fold ≠ ⊥ ∧ anchor.phase ≠ ⊥ ∧ anchor.semantic ≠ ⊥
V2 ::= target.backend ≠ ⊥ ∧ target.entry ≠ ⊥
V3 ::= dispatch matches contract.shape
V4 ::= executor(target) ⊨ contract
V5 ::= resources compatible with backend
V6 ::= fallback.target ≠ target.backend
     ∧ fallback.condition references valid vars
V7 ::= ∀ error ∈ contract.errors:
         executor reports error.code when error.condition holds
```

A binding `B` is well-typed iff it satisfies V1–V7.

---

## 5. Binding as morphism

A binding is a morphism in the category of semantic-to-physical mappings:

```text
B : (α_θ, backend) -> K_θ^backend
```

It takes a semantic fold at a phase plus a backend and produces the concrete kernel that realizes it.

Bindings compose along the phase cycle:

```text
B_Pop ∘ B_Wo ∘ B_Yax ∘ B_Sek ∘ B_Ch'en ∘ B_Xul
```

The composition is the pipeline binding for a full K'UHUL cycle.

The identity binding maps a fold to the host-LLM fallback:

```text
id_α : (α_θ, host-llm) -> HostLLMEntry
```

Every fold has at least this binding.

Bindings form a thin category `Bind`:

- Objects: pairs `(α_θ, backend)`.
- Morphisms: bindings `B : (α_θ, backend) -> K`.
- Composition: pipeline binding.
- Identity: host-LLM binding.

Thin means there is at most one binding between any two objects up to equivalence.

---

## 6. Binding lifecycle

```ebnf
BindingState ::= Draft | Resolved | Validated | Active | Deprecated
```

Transitions:

| Current | Event | Next |
|---|---|---|
| Draft | resolve | Resolved |
| Resolved | validate | Validated |
| Validated | activate | Active |
| Active | deprecate | Deprecated |
| Deprecated | resolve | Resolved |
| Active | contract_failed | Resolved |

Fold Kernel state mapping:

| Fold Kernel `@state` | Binding state |
|---|---|
| `unbound` | — |
| `observed` | — |
| `learned` | Draft |
| `bound` | Resolved |
| `validated` | Validated |
| `autonomous` | Active |

The binding lifecycle is subordinate to the Fold Kernel lifecycle. XCFE governs both.

---

## 7. Binding registry

The registry maps:

```text
(fold, phase, backend) -> Binding
```

Registry grammar:

```ebnf
Registry      ::= "{" RegistryEntry ("," RegistryEntry)* "}"
RegistryEntry ::= "{" EntryKey ":" Binding "}"
EntryKey      ::= '"' FoldId ":" Phase ":" Backend '"'
```

Operations:

| Operation | Description |
|---|---|
| `register(B)` | Add a binding |
| `resolve(α, θ, backend)` | Find a binding for a fold-phase-backend triple |
| `invalidate(B)` | Mark a binding invalid |
| `list(α, θ)` | List bindings for a fold at a phase |

The Binding Registry is the source of truth for the Binding Resolver.

---

## 8. Compressed statement

| Concept | Meaning |
|---|---|
| Binding | Object connecting a fold at a phase to an executor |
| Anchor | `(fold, phase, semantic)` |
| Target | `(backend, entry)` |
| Dispatch | fixed, dynamic, or adaptive |
| Contract | pre/post/invariants/errors |
| Resources | threads, shared memory, registers, timeout |
| Fallback | alternative backend with condition |
| Validity | `executor ⊨ contract` |
| Category | bindings form a thin category |
| Registry | `(fold, phase, backend) -> binding` |

One sentence:

> A Fold Binding is an anchored, contracted, executable object that connects a semantic fold at a phase to a concrete backend entry point; it carries a contract, dispatch specification, optional resources, and optional fallback, forming a thin category whose morphisms compose along the phase cycle and whose registry maps `(fold, phase, backend)` triples to bindings.

---

## 9. Completed stack

| Layer | Grammar | Authority |
|---|---|---|
| Semantic Fold | no grammar | K'UHUL |
| Fold Kernel | Fold Kernel Grammar | multi-authority |
| Fold Binding | Fold Binding Grammar | Binding Resolver |
| XCFE Policy | XCFE Policy Grammar | XCFE |
| Executor | backend-specific | Backend |

The stack is closed:

```text
Fold -> Binding -> Executor
Fold Kernel = carrier of Bindings
```

The binding is the connective tissue that makes the Fold Kernel executable.

---

## 10. Remaining artifact

The next artifact is the **Executor Interface Specification**: the ABI and dispatch convention any backend must satisfy to accept a Fold Binding.

Required surfaces:

- Entry point ABI: how the executor receives input and returns output.
- Dispatch convention: how the runtime invokes the executor.
- Error protocol: how contract violations are reported.
- Resource protocol: how threads, shared memory, and timeouts are managed.
- Fallback protocol: how an executor signals fallback.
