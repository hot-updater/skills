# Hot Updater Skills

Install the Hot Updater agent skill with `npx skills`:

```sh
npx skills add hot-updater/skills
```

The skill source lives at [`skills/hot-updater/SKILL.md`](skills/hot-updater/SKILL.md).

Then ask your agent:

```text
$hot-updater Set up infrastructure on Cloudflare for this project.
```

The agent discovers your app and existing configuration, creates missing projects
and resources, and applies the generated deployment templates through available
MCP, CLI, API, or browser tools. It asks only for unresolved choices or access.
It verifies remote state and resumes unfinished steps after a failure.
Cloudflare, Supabase, AWS, and Firebase are supported by the infrastructure workflow.

Authenticate through the provider or save credentials directly in a local ignored
file; do not paste tokens, passwords or private keys into the conversation.
The generated environment guide explains each variable's purpose, conditions and
source so optional fields do not become unnecessary onboarding questions.

To upgrade an existing server:

```text
$hot-updater Upgrade this project's existing Cloudflare server infrastructure.
```

The agent reads the CLI's versioned release files in order, including intermediate
releases, applies pending changes, and verifies the server with doctor. You can
also request standalone server templates without deploying them.

The skill checks the installed CLI's help before using `agent infra setup`,
`agent infra upgrade`, or `infra scaffold`. These require a CLI release that
advertises those commands; installing the skill does not add them to an older CLI
or grant cloud access.
