---
name: hot-updater
description: Operate and diagnose Hot Updater CLI projects across legacy Bundle-policy and Release Catalog versions. Use for setup, deployment, rollout, rollback, promotion, Bundle and Release inspection, patch artifacts, storage cleanup, channels, signing keys, database migration, fingerprints, doctor repair, or other React Native OTA tasks. Discover the locally installed CLI command surface from just-in-time `--help` output instead of assuming a Hot Updater version or memorizing commands and options.
---

# Hot Updater CLI

Treat the target project's locally installed CLI as the source of truth. Keep
this skill focused on discovery, ownership, and safety rather than duplicating
the CLI reference.

## Pin the target CLI

1. Start at the target project root unless the user specifies another app.
2. Read `package.json`, its package-manager declaration, its lockfile, and
   `hot-updater.config.ts` when present.
3. Resolve the project-local `hot-updater` binary with the project's package
   manager. Examples include `pnpm exec hot-updater` and
   `npx --no-install hot-updater`. Store that complete invocation as `<cli>`.
4. Do not use a global binary. Do not let `npx`, `bunx`, or another executor
   download a missing or newer CLI implicitly.
5. If no local CLI can be resolved, stop and report that discovery is blocked.
   Do not install or upgrade Hot Updater without explicit authorization.
6. Run `<cli> --version` for reporting only. Never select a workflow from the
   version string.

## Discover commands just in time

Run `<cli> --help` before planning a CLI workflow. Before invoking any nested
command, run `--help` at that exact command path as well.

```text
<cli> --help
<cli> <group> --help
<cli> <group> <command> --help
```

Use the generated help as the syntax contract:

- Read command and option tokens semantically. Do not parse column positions,
  whitespace, wrapping, ordering, or prose with a fixed regular expression.
- Use only arguments and options shown by the target command's current help.
- Treat required arguments, accepted values, and displayed defaults as local
  to that CLI. Do not carry them over from another project or earlier run.
- Use `--json`, `-y`/`--yes`, `--dry-run`, `preflight`, filters, or limits only
  when the exact command help exposes them.
- Treat JSON output dynamically. Do not assume a field exists merely because a
  different Hot Updater version returned it.
- If a command or option is absent, adapt to an available safe workflow or
  report the missing capability. Never silently install a different version.
- Repeat exact-command help discovery after the dependency or lockfile changes.

Help probes are read-only. Run them freely before asking the user for mutation
approval.

## Select the ownership model by capability

Probe capabilities rather than comparing semantic versions.

### Release Catalog model

Select this model when the top-level help exposes `release` and
`<cli> release --help` succeeds.

- Release owns channel delivery policy, enablement, rollout, targeting,
  chronology, and promotion.
- Bundle is an immutable install artifact and native crash identity.
- For rollback intent, inspect Releases, identify the exact affected Release,
  read the disable command help, and disable that Release. Do not infer a policy
  mutation from Bundle identity.
- For promotion, use the Release promotion capability shown by help. Expect a
  new Release that can reuse the source Bundle, then verify both source and
  target policy.
- For cleanup, remove Release references before deleting an unreferenced Bundle,
  then preview Storage pruning when that capability exists.

### Legacy Bundle-policy model

Select this model when Release commands are absent and Bundle help exposes
policy mutations or the top-level help exposes rollback.

- Bundle rows own delivery policy as well as artifact identity.
- Use only the Bundle policy commands exposed by `<cli> bundle --help`.
- For rollback intent, prefer the rollback command when exposed. Read its exact
  help before selecting channel, platform, or target arguments.
- For promotion, use the Bundle promotion capability shown by help and verify
  the resulting Bundle state.
- For cleanup, delete eligible Bundle records before previewing Storage pruning.

If both models appear or a required subcommand is missing, exact-command help
wins. Do not guess across models before a mutation.

## Execute requests safely

For every request:

1. Classify it as read-only, local file writing, or external mutation.
2. Discover top-level and exact-command help.
3. Read current state with the available list/show/doctor command and supported
   filters. Prefer JSON only when help offers it.
4. Resolve an exact target. Ask one concise question if channel, platform,
   Release, Bundle, patch base, destination, or server URL remains ambiguous.
5. Use a displayed preview, preflight, or dry-run capability when available.
6. Execute only the mutation the user authorized. Use a displayed noninteractive
   confirmation flag only after the target and consequence are unambiguous.
7. Verify with a help-discovered read command. Do not assume the mutation's
   output schema.

Treat deploy, patch creation, policy changes, rollback, promotion, record
deletion, destructive pruning, channel changes, key export/removal, and database
migration as mutations. Treat key generation and schema generation as local
file writes.

## Invariant guardrails

- Do not run interactive initialization on the user's behalf. Guide the user
  through the choices shown by its current help.
- Before a server-aware doctor run, obtain the server base URL from the user or
  local configuration. Prefer JSON only when the current doctor help offers it.
- In a doctor repair loop, change only local auto-fixable issues or explicitly
  listed commands. Treat credential, infrastructure, and redeployment needs as
  blockers unless separately authorized.
- For current-app-version deployment, first discover and run the available app
  version command, then read deploy help and use its current platform and target
  options. Deploy one platform at a time and stop after a failure.
- Do not automatically repair a failed deploy, edit setup, change credentials,
  install dependencies, or run migrations. Report the failure and relevant next
  checks unless the user requests remediation.
- Patch artifacts remain Bundle-to-Bundle data. Resolve exact Bundle identities
  and read patch help regardless of the selected policy model.
- Treat an unqualified cleanup request as preview-only. Before destructive
  pruning, confirm exclusive ownership of the storage prefix and stop every
  operation that can write to it.
- Do not edit provider credentials unless explicitly requested. Projects often
  use `.env.hotupdater`, but local configuration may load another environment.
