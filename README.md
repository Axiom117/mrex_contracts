# mrex_contracts

Shared data contracts for the M-REx **Ta*** generation pipeline** (thesis chapter III): the schemas and DTOs that cross module boundaries. **No algorithms live here** — only data contracts and validation.

## Role

```
A task recognition → B type synthesis → C voxel modularization → D evaluation & closing the loop
```

Each stage is an independent module (separate repo). They communicate **only** through the contracts defined here, so any module can be swapped or versioned without touching the others.

## Contracts

| Contract | Boundary | Contents |
| --- | --- | --- |
| `TaskDescriptor` | data source ↔ A | source-agnostic task trace (schema v0.2.0) |
| `TaskRequirement` | A ↔ B | motion screw system `{S}`, constraint screw system `{W}`, metrics (DOF / working range / stiffness / motion profile) |
| `Topology` | B ↔ C / D | mechanism topology (connection graph / adjacency, joint & limb types) |
| `MechanismModel` | C ↔ D / IV | concrete voxel mechanism (modules, poses, realized `{S}`, working range, stiffness) |
| `Evaluation` | D | Φ evaluation interface + result DTO (score / pass-fail / Φ breakdown) |
| `Common` | all | SE(3) pose, screw `vec6`, units, ID / serialization / versioning conventions |

## Layout

```
schemas/                          # JSON Schema documents (language-agnostic, source of truth)
  task_descriptor.schema.json     # TaskDescriptor v0.2.0
```

Python (pydantic) model bindings are planned; the JSON Schemas are authoritative.

## Status

Initial commit: scope + the `TaskDescriptor` schema (v0.2.0). Remaining contracts and Python bindings to be added as the pipeline modules land.

## References

- Vault: `3. Task Driven Type Synthesis/1. Task Recognition and Motion Primitives Retrieval/3. TaskDescriptor 数据接口规范` (v0.2.0)

## Related repos

- A: `mrex_task_recognition` (planned), `M-REx_Perception` (data source)
- B: `mrex_type_synthesis`
- C: `mrex_modularization` (planned)
- D / orchestrator: `mrex_generative` (planned)
