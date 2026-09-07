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

The agent asks for missing choices or access, generates the provider's deployment
templates, and applies them through available MCP, CLI, API, or browser tools.
It verifies remote state and resumes unfinished steps after a failure.
Cloudflare, Supabase, AWS, and Firebase are supported by the infrastructure workflow.

For upgrades, the agent reads the CLI's versioned release files in order, including
intermediate releases, before changing the server. You can also request standalone
server templates without deploying them.

The skill checks the installed CLI's help before using `agent infra setup`,
`agent infra upgrade`, or `infra scaffold`. These require a CLI release that
advertises those commands; installing the skill does not add them to an older CLI
or grant cloud access.
