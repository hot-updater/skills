---
name: hot-updater
description: Operate and diagnose Hot Updater CLI projects across changing Bundle-owned and Release-owned policy command surfaces. Use for setup, deployment, rollout, rollback, promotion, Bundle and Release inspection, patch artifacts, storage cleanup, channels, signing keys, database migration, fingerprints, doctor repair, or other React Native OTA tasks. Discover the locally installed CLI command surface from just-in-time `--help` output instead of memorizing commands and options.
---

# Hot Updater CLI

Use this skill as a router, not a CLI manual. Treat the target project's locally
installed CLI help as the syntax source of truth.

## Start Here (Read This First)

1. Start at the target project root.
2. Read `package.json`, its package-manager declaration, its lockfile, and
   `hot-updater.config.ts` when present.
3. Resolve the project-local `hot-updater` invocation and call it `<cli>`.
4. Run `<cli> --help`.
5. Pick the matching intent from the decision map.
6. Run `--help` at every exact command path needed by the flow.
7. Read state, preview when supported, perform only the authorized mutation,
   and verify the result.

## Decision Map

- Unknown command surface: top-level help -> policy ownership probe -> exact
  group help.
- Inspect delivery state: discover the policy owner's list/show capabilities.
- Deploy: app-version capability when needed -> deploy help -> one platform ->
  policy and artifact verification.
- Change rollout, targeting, enablement, or message: discover the policy owner
  -> exact mutation help -> preview -> mutate -> verify.
- Roll back: discover the policy owner -> identify the exact affected policy
  record -> use the available rollback or disable capability -> verify fallback.
- Promote: discover the policy owner -> source inspection -> promotion help ->
  preview -> mutate -> verify source and target.
- Create a patch: resolve exact base and target Bundles -> patch help -> mutate
  -> verify artifact metadata.
- Clean Storage: policy references -> Bundle deletion eligibility -> prune help
  -> preview -> exclusive writer check -> explicit deletion -> recheck.
- Diagnose: doctor help -> supported structured output -> focused repair loop.
- Setup, keys, channels, or database work: exact group help -> classify local
  file writes versus external mutations -> execute only authorized steps.

## Local CLI Selection Rules

- Use the target project's package manager to resolve its installed binary.
  Examples include `pnpm exec hot-updater` and
  `npx --no-install hot-updater`.
- Do not use a global binary.
- Do not let `npx`, `bunx`, or another executor download a missing or newer CLI
  implicitly.
- If no local CLI can be resolved, stop. Do not install or upgrade Hot Updater
  without explicit authorization.
- Re-run discovery after the dependency or lockfile changes.

## Help Discovery Contract

Probe help hierarchically:

```text
<cli> --help
<cli> <group> --help
<cli> <group> <command> --help
```

- Read help semantically. Do not parse columns, whitespace, wrapping, ordering,
  or prose with a fixed regular expression.
- Use only arguments, options, accepted values, and defaults shown by the exact
  target command's help.
- Use `--json`, confirmation bypass, dry-run, preflight, filters, or limits only
  when that exact command exposes them.
- Treat command output dynamically. Do not assume fields from another command
  surface.
- If a command or option is absent, adapt to an available safe flow or report
  the missing capability. Never swap in another CLI.

Help probes are read-only and can run before mutation approval.

## Policy Ownership Rules

- When the top-level help exposes `release` and Release group help succeeds,
  treat Release as the owner of delivery policy, chronology, and promotion.
  Treat Bundle as an immutable install artifact and native crash identity.
- When Release policy capabilities are absent and Bundle help exposes policy
  mutations or top-level rollback, treat Bundle as the policy and artifact
  owner.
- When capabilities are mixed or partial, trust exact-command help. Stop before
  mutation if ownership or the target remains ambiguous.

## Target Selection Rules

- Resolve exact policy and artifact identities before mutation.
- Do not treat a Bundle identity as a Release policy identity.
- Do not interpret “latest” across multiple channels, platforms, compatibility
  scopes, or target cohorts without narrowing the request.
- Ask one concise question when channel, platform, policy record, Bundle, patch
  base, destination, or server URL cannot be inferred safely.
- For noninteractive execution, use a displayed confirmation-bypass option only
  after the exact target and consequence are authorized.

## Canonical Flows

### 1) Read-Only Inspection

```text
top-level help -> ownership probe -> read-command help -> read current state
```

Prefer structured output only when the read command advertises it. Interpret
the returned shape at runtime.

### 2) Policy Mutation

```text
ownership probe -> list/show help -> exact target -> mutation help
-> preview/preflight when available -> mutate -> list/show verification
```

For rollback intent under Release-owned policy, disable the exact affected
Release rather than inferring policy from Bundle identity. Under Bundle-owned
policy, use the rollback or Bundle mutation capability actually exposed by
help.

### 3) Deployment

```text
app-version help when needed -> deploy help -> deploy one platform
-> inspect resulting policy and Bundle
```

Stop after a failed deployment. Do not continue to another platform or attempt
automatic repair unless the user requests remediation.

### 4) Storage Cleanup

```text
inspect policy references -> remove authorized references -> delete eligible Bundle
-> prune help -> preview when available -> stop writers -> explicit delete -> recheck
```

Treat an unqualified cleanup request as preview-only. Require exclusive
ownership of the Storage prefix before destructive pruning.

### 5) Doctor Repair

```text
doctor help -> current diagnostics -> one focused local repair -> rerun diagnostics
```

Obtain the server base URL from the user or local configuration before a
server-aware check. Treat credentials, infrastructure changes, and redeployment
as blockers unless separately authorized.

## Guardrails (High Value Only)

- Do not run interactive initialization on the user's behalf.
- Classify deploy, patch creation, policy changes, rollback, promotion, record
  deletion, destructive pruning, channel changes, key export/removal, and
  database migration as mutations.
- Classify key and schema generation as local file writes.
- Resolve exact base and target Bundle identities before patch creation.
- Do not automatically edit setup, credentials, dependencies, or migrations
  after a failed command.
- Before destructive pruning, stop every operation capable of writing to the
  same Storage prefix.
- Verify mutations with help-discovered read commands rather than assuming the
  mutation output shape.

## Common Failure Patterns

- Local CLI cannot be resolved: report the missing project dependency; do not
  download another CLI.
- `unknown command` or `unknown option`: refresh top-level and exact-command
  help; do not reuse syntax from memory.
- Policy command appears under a different group: rerun the ownership probe and
  continue only after resolving the exact target.
- Noninteractive prompt blocks execution: check whether the exact command
  advertises a bypass option and whether the user authorized the consequence.
- Cleanup has no preview capability: report the limitation rather than treating
  a destructive command as a preview.

## Security and Trust Notes

- Prefer the project-local installed binary over on-demand execution.
- Treat provider configuration and environment variables as sensitive.
- Do not edit provider credentials unless explicitly requested. Projects often
  use `.env.hotupdater`, but configuration may load another environment.
- Keep database migration, signing-key writes, and Storage deletion inside the
  user's explicitly authorized scope.
