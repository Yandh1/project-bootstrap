# Project Bootstrap

An interview-first agent skill for turning project requirements into a documented, runnable repository setup. Maintained by [Rafael (Yandh1)](https://github.com/Yandh1).

Instead of choosing a stack immediately, the skill asks about the project, records the decisions, and builds the agreed structure. It supports monorepos and separate backend, frontend, and documentation repositories, with optional Docker orchestration and a Node.js command runner.

## What this repository contains

This repository contains **agent instructions**, not a project generator executable or a ready-made application. The agent creates project-specific files after the requirements interview. There is no package to install, build step, or runtime dependency for the skill itself.

```text
project-bootstrap/
├── SKILL.md                       # Main instructions and activation criteria
├── agents/
│   └── openai.yaml                # Codex display metadata and default prompt
├── references/
│   ├── intake.md                  # Requirements interview
│   ├── architecture.md            # Repository layout and agent routing
│   ├── runner.md                  # RUN.js, Compose, entrypoints, verification
│   └── docker-session.md          # Local Docker lifecycle and cache cleanup
└── README.md
```

## Installation

Clone the repository into your personal Codex skills directory:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/Yandh1/project-bootstrap.git \
  "${CODEX_HOME:-$HOME/.codex}/skills/project-bootstrap"
```

If that directory already exists, keep a backup or choose a separate checkout before replacing it. To update an existing clean clone:

```bash
git -C "${CODEX_HOME:-$HOME/.codex}/skills/project-bootstrap" pull --ff-only
```

Open a new agent session if the skill does not appear in the current one. Other agents can use the Markdown instructions when configured to load `SKILL.md` and its relative references; `agents/openai.yaml` provides Codex-specific metadata.

## Usage

Invoke the skill explicitly, then answer its questions:

```text
Use $project-bootstrap to initialize a new inventory application.
Ask me about the stack and repository layout before generating files.
```

The skill is authored in Spanish and normally conducts the interview in Spanish. You can request another language:

```text
Use $project-bootstrap. Conduct the interview in English and write the
project documentation in English. I want a backend API and a separate web app.
```

You may provide decisions up front, ask for concrete recommendations, or explicitly ask to skip the interview. The agent should reuse answers already supplied and wait for decisions that are necessary to produce a coherent setup.

## Workflow

1. **Define the project.** Establish purpose, scope, stack, versions, repository topology, local destination, and execution model.
2. **Choose relevant services.** Discuss data, authentication, API contracts, networking, testing, deployment, and environment isolation as needed.
3. **Agree on agent and Git boundaries.** Choose documentation conventions, validation, and whether branches or isolated worktrees are required.
4. **Generate the agreed setup.** Create only the necessary repositories, configuration, infrastructure, runner commands, and documentation.
5. **Verify and report.** Check runner syntax, CLI behavior, Markdown paths, and Compose configuration when applicable. Clearly distinguish static validation from services actually started and tests actually run.

The skill applies to initial project setup. Routine changes to existing applications are outside its activation scope.

## Possible generated structure

For separate repositories, the suggested layout is:

```text
workspace/
├── example-back/
│   └── AGENTS.md
├── example-front/
│   └── AGENTS.md
└── example-docs/
    ├── AGENTS.md
    ├── README.md
    ├── RUN.js
    ├── package.json
    ├── .env.example
    ├── agents/
    │   ├── init.md
    │   ├── ROUTER.md
    │   └── STACK.md
    ├── documentacion/
    └── infra/
```

This is a pattern, not a mandatory template. Backend and frontend instructions point to the shared contract in the documentation repository using verified relative links. Dockerfiles and Compose files live alongside that contract, with build contexts explicitly resolving sibling sources. Monorepos use equivalent areas within one repository.

## Runner and Docker conventions

When selected during the interview, `RUN.js` provides a consistent interface for humans and automation. It should resolve paths relative to its own location, execute subprocesses with argument arrays, reject invalid or conflicting actions, propagate failures, and work without an interactive terminal. Its exact commands depend on the chosen stack; this repository does not ship a generic runner.

Local Docker conventions in this edition include:

- Manual startup, with `restart: "no"` for local services.
- Persistent data volumes retained when stopping or removing the stack normally.
- Separate development, testing, and local production environments when requested.
- Build-cache cleanup at host boot through one host-level task, where explicitly authorized and supported.
- No automatic deletion of application data, images, or volumes as part of cache cleanup.

The reference systemd service targets Linux with Docker Engine. Rootless Docker, Docker Desktop, other operating systems, and additional builders need their own assessment. Creating a project does not itself authorize host-wide changes. Remote deployment restart policies are decided separately.

## Personalization and portability

This is Rafael’s original configuration, preserved with its Spanish instructions and personal defaults. Before adopting it, review:

- References to Rafael and dated preferences in `SKILL.md` and the interview.
- The local Docker restart and boot-time cache-cleanup policy.
- The note about a cache-cleanup service already existing on Rafael’s machine; it is not evidence that it exists on yours.
- The historical architecture notes in `references/architecture.md`.
- Display metadata and the suggested prompt in `agents/openai.yaml`.

The architecture reference records lessons from an earlier project called SIIA. The skill is self-contained and does not require that checkout. Those historical names are context, not credentials or required project settings. New projects must not inherit its data, identities, institutional rules, or secrets.

## Safety and scope

The skill instructs agents to preserve existing work, inspect applicable repository instructions, avoid copying secrets, and respect workspace and Git boundaries. Worktrees are optional. Publishing repositories, pushing commits, resetting data, and making host-wide changes require the relevant authorization.

No application services, Docker host changes, or dependency installations occur simply by cloning this repository.

## Contributing

Keep `SKILL.md` concise and place detailed guidance in `references/`. Preserve relative links and keep the intake, architecture, runner, and Docker guidance consistent. Describe platform assumptions explicitly and document what was actually verified. Never include credentials, real database snapshots, or machine-specific secrets in examples.

## License

No license has been selected yet. Public visibility alone does not grant a general open-source license; contact the maintainer for reuse permissions until a license is added.
