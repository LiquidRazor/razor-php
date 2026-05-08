# AGENTS.md

This file defines mandatory agent constraints for the Razor-PHP repository.

Do not duplicate project description, maintenance flow, unattended upgrade flow, or generated maintenance output rules here.

For project context, read:

```text
workflow/description.md
```

For maintenance planning, read:

```text
workflow/maintain.md
```

For unattended upgrade execution, read:

```text
workflow/unattended-upgrade.md
```

## Source freshness rules

Agents must not rely on cached, remembered, assumed, or previously known version information.

All version information must be verified against current internet sources before being used.

This applies to:

- PHP major versions;
- PHP patch versions;
- PHP release status;
- Debian major versions;
- Debian codenames;
- Debian release status;
- package names;
- package availability;
- package compatibility;
- PECL extension versions;
- PECL extension compatibility;
- GitHub Actions behavior;
- Docker Buildx behavior;
- registry publishing behavior.

If current internet access is unavailable, stop and report that the task cannot be completed safely.

Do not substitute cached knowledge for live verification.

## Internet source priority

When resolving current facts, prefer official upstream sources.

Required preferred sources:

- PHP official release pages and PHP source distribution metadata for PHP versions;
- Debian official release pages, package repositories, and package search for Debian versions and packages;
- PECL official package pages for PECL extension versions and compatibility;
- Docker official documentation for Docker and Buildx behavior;
- GitHub official documentation for GitHub Actions behavior;
- Docker Hub and GHCR documentation for registry behavior.

Third-party sources may be used only as supporting references.

Third-party sources must not override official upstream sources.

If official sources disagree, record the disagreement and stop before making version-sensitive changes.

## Latest-information rule

Only the latest updated information may be used for maintenance decisions.

When multiple sources are available, agents must prefer the most recently updated official source that directly answers the question.

Agents must check publication dates, release dates, package timestamps, or repository metadata where available.

Do not use stale documentation when fresher official metadata exists.

Do not use old examples from documentation as proof of current compatibility.

## Error investigation rules

If an error occurs during maintenance, build planning, package installation, Dockerfile validation, workflow validation, or unattended upgrade execution, investigate using current internet sources before proposing a fix.

The internet is the primary source of truth for:

- Debian package compatibility;
- package renames;
- removed packages;
- replaced packages;
- changed build dependencies;
- PHP source build requirements;
- PECL extension compatibility;
- GitHub Actions syntax or runner changes;
- Docker Buildx syntax or behavior changes.

Local error output is evidence of what failed.

Current official internet sources are evidence for why it failed and how it should be fixed.

Do not guess package replacements.

Do not pin obsolete package names because they worked in a previous Debian release.

Do not keep broken compatibility workarounds unless current upstream documentation still justifies them.

## Version hardcoding rule

Do not hardcode PHP or Debian versions unless the target file requires concrete values.

When concrete values are required, they must be taken from the verified current data recorded in:

```text
workflow/versions.md
```

Do not invent examples using current-looking versions unless they are verified.

Prefer policy-based placeholders in documentation:

```text
<major>
<version>
<tag>
```

Use exact versions only in files where exact versions are operationally required.

## Generated files rule

Agentic generated files belong under:

```text
workflow/
```

The only agentic configuration file allowed outside `workflow/` is:

```text
AGENTS.md
```

Do not create additional root-level agent instruction files.

Do not create ad-hoc planning files outside `workflow/`.

## Repository-scope rule

This repository is Docker-image infrastructure.

Do not introduce unrelated application, framework, library, or Composer-package structure.

Do not add folders such as:

```text
src/
lib/
include/
app/
vendor/
```

unless a later explicit repository decision changes the project scope.

## Change discipline

Agents must only change files required by the active workflow step.

Do not perform opportunistic refactors.

Do not normalize unrelated formatting.

Do not collapse Dockerfile variants unless explicitly required by a generated maintenance step.

Do not merge CLI and FPM behavior unless explicitly required by a generated maintenance step.

Do not remove extensions, packages, workflows, or tags without documented justification.

## Validation discipline

Every change must be validated using the strongest available checks for the touched files.

For Dockerfiles, validate Docker syntax or build planning where possible.

For GitHub Actions workflows, validate YAML syntax and workflow structure where possible.

For version-sensitive changes, validate against `workflow/versions.md`.

For package-sensitive changes, validate against current Debian package metadata.

For PECL-sensitive changes, validate against current PECL metadata.

If validation cannot be performed locally, record the limitation clearly in the relevant generated maintenance file.

## Failure discipline

If a step fails, do not continue by assumption.

Follow the failure handling rules in:

```text
workflow/unattended-upgrade.md
```

Failure reports must include enough current-source evidence to support the next regenerated maintenance plan.

A fix without current-source evidence is not acceptable for version, package, build, or compatibility failures.

