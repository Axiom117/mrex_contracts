# Conventions

Shared conventions for every contract in this repo. When a contract and this file disagree, this file is authoritative unless the contract states otherwise.

## 1. Pipeline and boundaries

```
data source ──TaskDescriptor──▶ A+B (mrex_type_synthesis) ──Topology──▶ C (mrex_modularization)
                                                                        │
                                            ┌─────MechanismDescription──┘
                                            ▼
                                   D (mrex_generative) ──Evaluation──▶ loop-back
                       A+B ──TaskRequirement────────────────────────▶ D
                       C   ──MechanismDescription──────────────────▶ IV (symbolic kinematics)
```

| Contract | Produced by | Consumed by | File |
| --- | --- | --- | --- |
| `TaskDescriptor` | data sources (sim / vision / demo) | A+B | `task_descriptor.schema.json` |
| `TaskRequirement` | A+B | A+B (internal), D | `task_requirement.schema.json` |
| `Topology` | A+B | C | `topology.schema.json` |
| `MechanismDescription` | C | D, IV | `mechanism_description.schema.json` |
| `Evaluation` | D | orchestrator | `evaluation.schema.json` |

## 2. Units (default, overridable per document)

| Quantity | Default |
| --- | --- |
| length | `mm` |
| angle | `rad` |
| time | `s` |
| force | `mN` |
| torque | `Nmm` |

Every value is expressed in these units unless the document declares otherwise.

## 3. Frames and poses

- A pose is a 4x4 homogeneous transform, serialized as 16 numbers **row-major**.
- Convention: `p_parent = T * p_child` — `T` maps a point in the child frame to the parent frame.
- Handedness: right-handed.
- The world frame of a mechanism is its base frame. Forward kinematics propagates **tool-rooted** (from the end-effector outward), per the IV-chapter design.

## 4. Screws: twists and wrenches

Screw 6-vectors use the **linear-first** (Murray / MLS) ordering:

- twist `xi = [v; w]` = `[vx, vy, vz, wx, wy, wz]` (linear part first)
- wrench = `[f; m]` = `[fx, fy, fz, mx, my, mz]` (force first)

The numerical toolchain (`NxRLab/ModernRobotics`) uses the **angular/moment-first** ordering (`V = [w; v]`, `F = [m; f]`). Convert by **swapping the two 3-vectors** at the toolchain boundary. Documents record the ordering in `provenance.convention` (e.g. `murray-linear-first`).

Joint axes:

- revolute / cylindrical / helical: a unit twist (linear-first) plus a `point` on the axis.
- prismatic: `v` is the unit direction, `w = 0`.
- rigid / locked: zero 6-vector, angular part zero.

## 5. Identifiers

- All ids are **strings**, unique within their document.
- `TaskDescriptor.meta.task_id` is the task handle propagated through the pipeline; each contract references its upstream via `*_ref` / `source_descriptor` fields.

## 6. Serialization and versioning

- Documents may be **JSON or YAML**; schemas validate the parsed object, so both are equivalent.
- `meta.schema_version` is semver (e.g. `0.1.0`). Additive optional fields bump minor; breaking changes bump major.
- Each schema's `$id` is `urn:mrex:contracts:<name>:<version>`.
- Schemas are **self-contained** (shared primitives are inlined in `$defs`) so any single contract can be validated in isolation.

## 7. Status

All contracts except `TaskDescriptor` (v0.2.0) are **v0.1.0 drafts**. `MechanismDescription` is provisional until the chapter-IV symbolic-kinematics DSL is formally specified.
