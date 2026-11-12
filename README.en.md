<div align="center">

🇬🇧 English | 🇫🇷 [Français](README.md)

</div>

# claude-skills-and-team-for-business-game-generation

A team of Claude Code agents for business game generation projects: a trainer picks parameters, a deterministic
engine assembles and computes the simulation, and AI NPCs state the computed facts. The team comes from the G-mix
project, generalized so it can be reused in any project of the same kind, whatever the stack.
Principle: few agents, short descriptions, the model matched to the task (haiku for mechanical work, sonnet for
code, opus for design and domain accuracy), and complete briefs.

The generic team (orchestrator, architect, developer, qa-reviewer, documentaliste, coach-equipe, and the
`regles-equipe` and `mesure-equipe` skills) comes from the sister repository
[claude-skills-and-team-for-every-project](https://github.com/Romain-LM-Dev/claude-skills-and-team-for-every-project),
installed automatically as a dependency. This repository only adds the business game agents and skills; the business
game review points live in the CLAUDE.md template, which every agent reads.

## Contents

Core agents (business game):

| Agent | Role | Model |
|---|---|---|
| `game-designer` | domain accuracy, figures, sources, learning objectives, NPCs, debrief | opus |
| `moteur-simulation` | deterministic engine, generation from a spec, NPC logic and fallback | sonnet |
| `ia-runtime` | AI tasks, NPC prompts, budgets, dialogue evaluations | sonnet |
| `rag-knowledge` | ingestion, search, corpus, sources and rights | sonnet |

Optional agents, depending on the stack (Godot, Unity, FastAPI, Kafka, etc. are only examples):

| Agent | Role | Model |
|---|---|---|
| `client-dev` | 3D client or web front end, HUD, per-role panels | sonnet |
| `backend-api` | API, roles, database, migrations | sonnet |
| `pipeline-evenements` | event bus and asynchronous workers | sonnet |
| `assets` | asset catalog, licenses, coverage | sonnet |
| `devops` | containers, scripts, environment | haiku |

Skills:
- `moteur-business-game`, the engine method (pure functions, validated spec, autopilot, calibration, NPCs);
- `business-game-designer`, design of business games and training simulations (preloaded by game-designer).

Templates to copy into a project, in `templates/`: `CLAUDE.template.md` (agent table, organization rules, brief,
report, business game rules and review points) and `ownership.example.json` (ownership map).

For read-only exploration, use the built-in `Explore` agent.

## Installation in Claude Code

As a plugin, once the repository is published, in a session:

```
/plugin marketplace add Romain-LM-Dev/claude-skills-and-team-for-every-project
/plugin marketplace add Romain-LM-Dev/claude-skills-and-team-for-business-game-generation
/plugin install team-for-business-game-generation@skills-and-team-business-game
```

The `team-for-every-project@skills-and-team` dependency is installed with it, or the copy you already have is reused.
Without the first marketplace the plugin does not load ("Dependency ... is not installed"): add it and the dependency
installs itself.

Local trial without installing: `claude --plugin-dir <this-repo> --plugin-dir <every-project-repo>`. The agents are
named `team-for-business-game-generation:game-designer`, `team-for-every-project:orchestrateur`, and so on. Skills
preloaded by an agent only apply when the orchestrator launches that agent as a subagent.

Manual copy:
1. copy the agents you want from `agents/` in this repository and in the every-project repository into the project's
   `.claude/agents/` (or `~/.claude/agents/`);
2. copy the folders of `skills/` from both repositories into `.claude/skills/`;
3. copy `templates/CLAUDE.template.md` to `CLAUDE.md` at the project root, fill it in, and delete the lines for the
   agents you did not install;
4. copy `templates/ownership.example.json` to `.claude/ownership.json` and adapt the file patterns.

The agents contain no path, port or command: the stack and folders are defined in the project's `CLAUDE.md`, which
they refer to.

## Enabling the orchestrator

It is not forced: on a small task it costs more than it saves. For a project with several domains:

- for one session: `claude --agent team-for-every-project:orchestrateur`;
- permanently for a project: in the project's `.claude/settings.json`,
  `{ "agent": "team-for-every-project:orchestrateur" }`.

## Business game principles

- **True, sourced knowledge**: every notion and every figure is a sourced fact, a calibration choice or an
  illustrative value, and says so; without a source, "to be checked".
- **Deterministic engine**: the simulation is computed by pure functions from a validated spec, with randomness
  derived from the seed; a game run on autopilot must finish it.
- **Factual AI NPCs**: they only state the facts computed by the engine, with bounded memory and a deterministic
  fallback.
- **An AI task, never a model**: the code calls a task from a catalog; the model chooses, says or classifies, the
  engine computes.
- **Learning debrief** based on events that actually happened; an untested game remains a prototype.
- **Roles** player, trainer (their own lobbies) and admin, checked on the server side.

## Using the skill in claude.ai

Agents do not run in the claude.ai chat; only skills can be imported there, as a ZIP.

```powershell
.\scripts\package-skills.ps1
```

The script writes one ZIP per skill into `dist/` (ignored by git), after checking `name` (64 characters at most,
identical to the folder) and `description` (200 at most). Then, in claude.ai: Customize, Skills, Upload.

## Validation

```
claude plugin validate .
claude plugin validate . --strict
```

GitHub CI (`.github/workflows/validate.yml`) runs strict validation and skill packaging (fails if a skill is
skipped) on every push.

Estimated cost (`claude plugin details`): about 1,080 tokens always on per session (2026-11-10), plus about 1,070
for `team-for-every-project` (2026-11-12).

## License

Non-commercial source-available license: see `LICENSE.md`. Commercial use requires written permission from the
rights holder.
