# `.u13/config.md`

Optional. If the file does not exist, every default applies and nothing breaks. Keys live in the YAML front matter; the body is free text you can use for notes.

```yaml
---
language: English      # language of reports to the user. Code, comments and docs follow the project.
decisions: manual      # manual | delegate. delegate sends questions to the decider agent (task-planner).
level: auto            # auto | light | standard | strict. auto is recommended.
docs_edit: propose     # propose | auto. Product docs outside .u13/ are only proposed unless auto.
gates: auto            # the checks to run on a change. auto = detect from the project; rarely needs setting.
decider_model: ask     # ask | inherit | opus | sonnet | haiku (task-planner). ask = ask once, then store the answer here.
---
```

`gates` means the project's own checks: typecheck, lint, tests, build. Detect them from `package.json` scripts, `Makefile`, `pyproject.toml`/`tox`, `Cargo.toml`, `go.mod`, `composer.json`, Gradle/Maven. Use the configured value only if the user set one. The decider's effort is not configurable: it is fixed to `high` in the agent definition.

Rules:
- Unknown keys are ignored. Missing keys use the defaults above.
- Do not require the file. Never create it just to store defaults.
- The first time a value has to be stored (for example `decider_model`), create the file with only that key.
