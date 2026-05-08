# Maintenance Workflow

This file defines the required maintenance workflow for Razor-PHP.

It must not duplicate the project description. Use `workflow/description.md` for project identity and scope.

## Required maintenance outputs

The maintenance process must create, update, or recreate the following files under `workflow/`:

```text
workflow/versions.md
workflow/maintenance-plan.md
workflow/maintenance-scaffold.md
workflow/maintenance-step<index>.md
```

## Execution order

Maintenance must be performed in this order:

1. `workflow/versions.md`
2. `workflow/maintenance-plan.md`
3. `workflow/maintenance-scaffold.md`
4. `workflow/maintenance-step<index>.md` files

No later file may be generated before the prerequisite files it depends on have been created or updated.

## 1. versions.md

Create or update `workflow/versions.md` first.

This file must verify and record the current supported platform versions.

It must check:

- the latest stable PHP major version;
- the immediately previous stable PHP major version;
- the latest stable PHP patch version for each supported PHP major;
- the latest stable Debian major version;
- the Debian codename for the latest stable Debian major version.

The file must clearly identify:

- the source used for each version decision;
- the resolved PHP major versions;
- the resolved PHP patch versions;
- the resolved Debian major version;
- the resolved Debian codename;
- whether repository files need changes based on the resolved versions.

Hardcoded versions in repository files must be treated as implementation targets only after they have been verified in `workflow/versions.md`.

Do not assume PHP or Debian versions from memory.

## 2. maintenance-plan.md

Create or update `workflow/maintenance-plan.md` after `workflow/versions.md` is complete.

The maintenance plan must inspect the current repository state and identify what needs to change.

The plan must inspect:

- all Dockerfiles under `docker/php/`;
- all GitHub Actions workflows under `.github/workflows/`;
- README references that may depend on supported PHP or Debian versions;
- build arguments related to PHP versions;
- build arguments related to Debian versions or Debian codenames;
- package installation commands;
- package names used for build dependencies;
- package names used for runtime dependencies;
- PECL extension installation logic;
- architecture-specific build handling;
- image tag generation logic;
- registry publishing logic.

The plan must verify that:

- Dockerfiles use the resolved Debian stable baseline;
- package names are valid for the resolved Debian baseline;
- build dependencies match the compiled PHP extensions;
- runtime dependencies match the enabled PHP extensions;
- PHP versions match the resolved supported PHP versions;
- CI matrix entries match the resolved supported PHP versions;
- published tags match the repository versioning policy;
- development images inherit from or align with their matching base images;
- CLI and FPM variants remain consistent where they should be consistent;
- differences between CLI and FPM variants are intentional and documented.

The plan must separate findings into:

- required changes;
- recommended changes;
- optional cleanup;
- risks or compatibility concerns;
- files that must not be changed.

## 3. maintenance-scaffold.md

Create or recreate `workflow/maintenance-scaffold.md` after `workflow/maintenance-plan.md` is complete.

This file is the full change scaffold for the maintenance run.

It must collect all identified changes from the maintenance plan into a structured implementation scaffold.

The scaffold must include:

- target files;
- required edits per file;
- dependency order between edits;
- expected outcome per edit;
- validation command or validation method per edit;
- rollback notes for risky edits;
- unresolved questions, if any.

The scaffold must not perform implementation.

It must describe what will be changed clearly enough that implementation steps can be generated from it without re-analyzing the repository from scratch.

## 4. maintenance-step<index>.md files

Create or recreate numbered maintenance step files after `workflow/maintenance-scaffold.md` is complete.

Each step file must use this naming pattern:

```text
workflow/maintenance-step<index>.md
```

Examples:

```text
workflow/maintenance-step1.md
workflow/maintenance-step2.md
workflow/maintenance-step3.md
```

Step files must be ordered by prerequisite safety.

A step may only depend on previous lower-numbered steps.

Each step file must contain exactly one coherent implementation unit.

Each step file must include:

- step objective;
- prerequisite files or previous steps;
- files allowed to change;
- files forbidden to change;
- detailed action list;
- validation commands;
- expected successful result;
- failure handling notes.

Step files must be specific enough for an agent to execute without guessing.

Step files must not include unrelated cleanup, opportunistic refactors, or broad rewrites.

## Step ordering rules

Maintenance steps must be ordered so that foundational changes happen before dependent changes.

Recommended ordering:

1. version metadata and build matrix updates;
2. Dockerfile Debian baseline updates;
3. Dockerfile package compatibility updates;
4. PHP source build updates;
5. PECL extension updates;
6. development image updates;
7. GitHub Actions workflow updates;
8. README or documentation alignment;
9. validation and release-preparation cleanup.

The actual step order must follow the dependency chain discovered in `workflow/maintenance-scaffold.md`.

## Safety rules

The maintenance workflow must not rely on stale version assumptions.

The maintenance workflow must not hardcode PHP or Debian versions unless the target file requires concrete values.

When concrete versions are required, they must come from `workflow/versions.md`.

Do not rewrite unrelated repository structure.

Do not introduce application source folders.

Do not introduce Composer package structure.

Do not collapse CLI and FPM Dockerfiles unless explicitly planned.

Do not merge production and development image variants unless explicitly planned.

Do not make undocumented package removals.

Do not remove extensions without documenting the reason in the maintenance plan.

## Completion requirements

A maintenance run is complete only when:

- `workflow/versions.md` exists and contains verified current platform versions;
- `workflow/maintenance-plan.md` exists and reflects the inspected repository state;
- `workflow/maintenance-scaffold.md` exists and contains the full implementation scaffold;
- all required `workflow/maintenance-step<index>.md` files exist;
- every generated maintenance step has clear prerequisites and validation instructions;
- no maintenance step requires information that exists only implicitly in agent memory.

