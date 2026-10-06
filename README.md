# mrex_contracts

Shared data contracts for the M-REx **Ta*** generation pipeline** (thesis chapter III): the schemas and DTOs that cross module boundaries. **No algorithms live here** — only data contracts, conventions, and validation.

## Role

```
data source ──TaskDescriptor──▶ A+B (mrex_type_synthesis) ──Topology──▶ C (mrex_modularization)
                                                                        │
                                            ┌─────MechanismDescription──┘
                                            ▼
                                   D (mrex_generative) ──Evaluation──▶ loop-back
                       A+B ──TaskRequirement────────────────────────▶ D
                       C   ──MechanismDescription──────────────────▶ IV (symbolic kinematics)
```

Each stage is an independent module (separate repo). They communicate **only** through the contracts defined here, so any module can be swapped or versioned without touching the others.

## Contracts

| Contract | Boundary | File |
| --- | --- | --- |
| `TaskDescriptor` | data source ↔ A+B | `schemas/task_descriptor.schema.json` (v0.2.0) |
| `TaskRequirement` | A+B (+ → D) | `schemas/task_requirement.schema.json` (v0.1.0) |
| `Topology` | A+B → C | `schemas/topology.schema.json` (v0.1.0) |
| `MechanismDescription` | C → D / IV | `schemas/mechanism_description.schema.json` (v0.1.0) |
| `Evaluation` | D | `schemas/evaluation.schema.json` (v0.1.0) |

`MechanismDescription` **is** the symbolic-kinematics description document (YAML) that chapter IV consumes: C's output = IV's input, no transform. It is provisional until the IV-chapter DSL is formally specified.

## Layout

```
mrex_contracts/
├── schemas/                                  # JSON Schema documents (source of truth)
│   ├── task_descriptor.schema.json           # data source → A+B
│   ├── task_requirement.schema.json          # {S}/{W} + metrics
│   ├── topology.schema.json                  # B → C
│   ├── mechanism_description.schema.json     # C → D / IV (symbolic kinematics YAML)
│   └── evaluation.schema.json                # Φ result
├── examples/
│   ├── topology.example.yaml
│   └── mechanism_description.example.yaml
├── docs/
│   └── conventions.md                        # units / frames / screw ordering / versioning
└── README.md
```

Schemas are **self-contained** (shared primitives are inlined in `$defs`) so any single contract validates in isolation. Documents may be JSON or YAML; schemas validate the parsed object.

Conventions (units, pose, screw ordering, ids, versioning) are authoritative in [`docs/conventions.md`](docs/conventions.md).

## Status

- `TaskDescriptor` v0.2.0 — stable draft.
- Other contracts v0.1.0 — **drafts**; `MechanismDescription` pending IV-chapter ratification.
- Python (pydantic) model bindings planned; the JSON Schemas are authoritative.

## References

- Vault: `3. Task Driven Type Synthesis/1. Task Recognition and Motion Primitives Retrieval/3. TaskDescriptor 数据接口规范` (v0.2.0)

## Related repos

- A+B: `mrex_type_synthesis`
- Data source: `M-REx_Perception`
- C: `mrex_modularization` (planned)
- D / orchestrator: `mrex_generative` (planned)
- IV: `symbolic-modular-kinematics`
