# @sebastienelet/agent-reach-adapter

Local release candidate for 0.0.1, not published or qualified from npm yet.
Complete integration targets macOS ARM64 only. Bun 1.4.3 runs the TypeScript
entry point directly; no build, Moon, Volta or dotfiles checkout is required.

After publication, acquire the exact version in a durable, dedicated directory:

```sh
bun install --exact --ignore-scripts @sebastienelet/agent-reach-adapter@0.0.1
bun run agent-reach-adapter plan
bun run agent-reach-adapter prepare "$HOME"
bun run agent-reach-adapter doctor
```

Acquisition runs no lifecycle installer. `plan` reports the pinned upstream
revision and Python/Node prerequisites. `prepare HOME` requires Git and fetches
that exact revision, verifies its files, then assembles the marketplace under
`HOME/.local/share/agent-reach/adapter/0.0.1/marketplace`. It preserves an
identical replay and refuses divergent files, directories or symlinks. It does
not replace the historical marketplace or activate a host. Partial acquisition
can leave a verified upstream cache, but not a partial marketplace.

Python 3.13, uv, the Python tools reported by `plan`, ffmpeg, Node, Docker,
GitHub CLI, and the selected agent are external prerequisites. Install Python
and its tools separately with native tooling, explicitly requested by the user;
Bun never installs them. Preserve the historical isolated interpreter at
`~/.local/share/agent-reach/python-tools/agent-reach/bin/python`, which the
packaged RSS and podcast commands use. The upstream runtime must use the full
Git revision in `resources/source.json`. Install the exact Node tools reported
by `plan` with Bun in a durable tool directory and expose their binaries on PATH.
Claude and Codex stay on their latest releases, without a pin.

`doctor` runs version probes with a ten second timeout per command and checks
that declared platform executables and the historical isolated interpreter exist.
Presence does not establish their versions, imports or platform behavior.
It reports the first missing, failed or empty probe with a nonzero exit. It
does not inspect browser sessions, invoke upstream `agent-reach doctor`, test
credentials, contact backends, or establish plugin activation.

Personal mcporter configuration, credentials, cookies, browser sessions and
Docker configuration are supplied by the workstation owner. The existing
LinkedIn backend uses
`stickerdaniel/linkedin-mcp-server:4.26.2@sha256:c7aa139cfaaecc8ae9f5e810d6517e5e36b5a1a52cadcb4eef6f0832810b4da6`.
Keep the image digest and existing profile volume when configuring Docker.
Pulling, starting and logging in to that service are separate explicit native
Docker operations; preparation never performs them.

Inspect each host's existing marketplace/plugin registrations before activation.
Refuse an unknown homonym, preserve unrelated entries, and do not register both
copies of `agent-reach`. In a clean host configuration, the native commands are:

```sh
claude plugin marketplace add "$HOME/.local/share/agent-reach/adapter/0.0.1/marketplace"
claude plugin install agent-reach@agent-reach-adapter --scope user
codex plugin marketplace add "$HOME/.local/share/agent-reach/adapter/0.0.1/marketplace"
codex plugin add agent-reach@agent-reach-adapter --json
```

For the historical `agent-reach@dotfiles-agent-reach`, record the previous path,
version and registrations first. Disable/remove only the known old registration
using that host's native CLI before activating the new one. For rollback,
remove the new registration and add/install the recorded previous marketplace
and plugin using native commands. Preserve the old marketplace until discovery,
activation and rollback have been observed. Never purge profiles or overwrite
shared configuration to complete a switch; there is no transaction across hosts.

Generic web reading remains anonymous Jina Reader access for public,
non-sensitive URLs only. Private/authenticated reading and browser fallback
are unavailable. The bundled skill records the budgets and content checks;
HTTP success alone does not establish retrieval.

The adapter retains the dotfiles BSD-2-Clause license. The exact upstream
Panniantong/Agent-Reach MIT license is copied into the prepared plugin alongside
its adapted references and scripts; `ADAPTER-LICENSE` retains the adapter notice.
`resources/source.json` records provenance.

Development uses `bun install --frozen-lockfile --ignore-scripts`, `bun test`,
`bun run typecheck`, `bun run lint` and `bun run format:check`. Tests and resources ship with the
package. A local tarball smoke is preparation evidence only: registry
installation, provenance, real Claude/Codex discovery, activation and rollback
remain release acceptance work. Linux and other architectures are not qualified.

Sources, tests, resources and the BSD-2-Clause license were extracted with
`git filter-repo` from dotfiles PR #568, commit
`206c4ac44423748bbd08e57733842969625c4c21`. The standalone history preserves
that package commit; the first standalone version is 0.0.1.

CI runs the existing checks, tests and `bun pm pack --ignore-scripts` on
macOS 26 and Ubuntu 24.04. Linux checks cover the portable adapter code, not
complete Linux integration. The official Gitleaks image scans all Git history,
including the first push. Its local Docker action keeps the image pin in a
Dockerfile so Dependabot can update it. Dependabot checks Bun dependencies,
GitHub Actions and the Gitleaks Docker image weekly with a three day
version-update cooldown.
JavaScript actions use full commit SHAs and the Gitleaks image uses a content
digest, with their versions recorded alongside them.

The manual `publish.yml` workflow calls CI before publishing from `main`, using
the GitHub `npm` environment. Bun installs dependencies and packs `release.tgz`;
the exact npm CLI dependency publishes that archive with OIDC and provenance.
Node 24.21.0 is used only for the publishing CLI. No npm token is configured.

To publish:

1. Create the public `SebastienElet/agent-reach-adapter` GitHub repository,
   add its remote and push `main`. Confirm that CI passes there.
2. Create the GitHub `npm` environment, restrict it to `main`, and configure any
   required release approval.
3. Configure the npm trusted publisher for `@sebastienelet/agent-reach-adapter`:
   owner `SebastienElet`, repository `agent-reach-adapter`, workflow `publish.yml`,
   environment `npm`, with direct `npm publish` allowed. If npm requires an
   initial package bootstrap before these settings are available, complete that
   separately with a distinct bootstrap version so 0.0.1 remains available for
   OIDC publication; never add a publishing token to this workflow.
4. Run **Publish** with `workflow_dispatch` on `main`. Inspect registry version,
   integrity and provenance before retrying an uncertain publication; never
   overwrite or unpublish a release as a retry strategy.
5. Install the exact registry version with Bun on macOS ARM64, outside dotfiles,
   and qualify preparation, Claude/Codex discovery, activation and rollback
   before switching existing consumers.

See [npm trusted publishing](https://docs.npmjs.com/trusted-publishers/).
