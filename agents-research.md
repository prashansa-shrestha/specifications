# Scene Search and memory integration

Read [README.md](README.md) and the shared [project policy](../../AGENTS.md)
before changing setup or public behavior. The parent policy governs communication,
approvals, environments, downloads, hosted calls, Git, logging, and recovery.
Address Prashansa by name and explain what each planned change or unfamiliar
command does and why. Apply these additional project-specific instructions.

## Ownership and approved work

- This checkout is the approved implementation home for the combined scene-agent
  and memory integration, an explicit exception to the parent's `3d_agent/`
  default. Extend its existing source, tests, tools, configuration, and docs.
- Do not create a second implementation in `../../3d_agent/`.
- Read `../3D-Mem/` and `../concept-graphs/` as donors for this integration.
  Reuse relevant ideas or code selectively; preserve licenses and attribution.
  Do not change donor code, environments, caches, or outputs as part of integration.
- The explicitly requested agent-instructions maintenance task separately permits
  instruction and associated documentation edits in the named donor repositories.
  That exception does not authorize product implementation there.
- Prefer ordinary functions, explicit inputs, and simple local data structures.
  Avoid frameworks or abstractions without a concrete need.
- Explain the plan and show proposed diffs before replacing existing files.
  Continue routine milestones covered by an approved implementation plan without
  asking again. A design discussion alone does not authorize implementation.
- Preserve unrelated changes. Ask before materially expanding architecture,
  behavior, evaluation validity, cost, privacy, or scope.

## Scene and tool contracts

- Accept supported standalone Gaussian PLY inputs without requiring occupancy maps
  or a donor dataset pipeline. Preserve all source Gaussians, their original
  coordinates, and their rendered attributes; do not silently downsample geometry.
- Preserve unrestricted cameras and the existing renderer, CLI, MCP, and saved
  investigation workflows. Resource refusal must not silently simplify the scene.
- Keep rendering, optional perception, memory, controller, and report generation
  as separate, simple responsibilities with explicit data passed between them.
- Renderer, geometry, and storage tools perform deterministic local operations.
  Keep model calls in explicit perception/controller paths; never hide inference
  in storage, retrieval, geometry, or report regeneration.
- Preserve Codex/MCP operation. Approved standalone work may add direct Gemini
  control and bounded helper agents without requiring Codex for that mode.
  These are requirements for approved work, not claims that those modes exist now.
- Local perception is optional. Disabled features must not load their models,
  execute their pipelines, or contribute stale results to a run.
- Reject invalid configuration combinations clearly before starting a run.
  Document actual tool contracts and configuration dependencies.

## Sessions, evidence, and memory

- Distinguish a fresh session from an explicit resume. Fresh sessions must not
  silently inherit another investigation's context, answers, memory, or caches.
- Persist session/question budgets and evidence. Resume reuses saved state and
  remaining budgets; it does not reset attempts or grant additional budget.
  Increase a budget only with explicit authorization.
- Automatically record each acquired observation with its source view, camera,
  scene/session identity, step, provenance, and actual tool/model result.
- Cite saved evidence for claims. Distinguish observations, hypotheses,
  uncertainty, and missing evidence; do not turn a model guess into an observation.
- Use conservative automatic identity matching. Keep ambiguous matches unresolved
  for agent review, and preserve the evidence behind acceptance or rejection.
- Maintain stable object and sighting IDs and append their history. Seeing the
  same object twice does not establish two objects.
- Retain merge, split, relabel, and other correction history with reasons and
  evidence. Track dependent claims so corrections invalidate or flag affected
  conclusions rather than silently leaving old conclusions current.
- Preserve earlier geometry/calibration provenance and mark stale derived values
  when observations, identity, scale, or calibration changes.
- Summaries are retrieval aids, not the source of truth. Keep underlying records
  and evidence available with bounded, explicit retrieval.
- Empty retrieval means no matching stored result; it does not prove scene absence.
  Never substitute a summary or incomplete memory for direct evidence.

## Geometry and calibration

- Gaussian centers, image masks, triangulated patches, and approximate object
  positions describe uncertain observed regions, not complete object surfaces.
- Masks and approximate geometry provide no occlusion guarantee. Rendered depth
  is not a validated physical surface or a guarantee of hidden-space geometry.
- The initial implementation may use approximate positions, clearly labeled with
  their method, evidence, uncertainty, and coordinate convention.
- Physical distances, fit, clearance, and units require user-confirmed scale and
  calibration. Do not infer physical accuracy from passing software tests.
- Retain original coordinates and calibration history. Mark stale measurements
  after a calibration or geometry change and preserve their earlier provenance.

## Evaluation integrity

- Never read reference answers during an investigation. Keep answer keys, judge
  inputs, and reference-only material outside the investigator's context/tools.
- Do not silently change seeds, splits, question sets, rubrics, metrics, resource
  budgets, or benchmark protocols to improve apparent results.
- Label saved replay separately from a real closed-loop investigation. Fixed
  scripted steps are not an agent-selected search policy.
- Prevent answer/context/cache leakage across questions, runs, and evaluation
  tracks. Record which state is intentionally shared and which is isolated.
- Preserve failed attempts, incomplete runs, budget charges, and uncertainty.
  Report actual outcomes and separate software, semantic, and metric validation.
- Keep generated evidence, datasets, model weights, caches, runtime state, and
  reports Git-ignored; commit only appropriate source and authored documentation.

## Local environment and hosted models

- Keep the environment, dependency installs, downloads, and caches inside this
  checkout or its explicitly approved project-local subdirectories. Do not alter
  donor environments, global runtimes, drivers, or persistent user/system settings.
- Prefer the documented `.venv` and project-local package caches. `uv.lock` is the
  dependency source of truth; keep dependency declarations and any requirements
  exports synchronized when approved changes require them.
- Before a download, state its purpose, official source, expected size, and exact
  project-local destination. Mark unknown size honestly.
- Downloads over approximately 500 MB or from non-official sources need explicit
  approval. An approved exact manifest covers its unchanged listed items;
  changed items or limits require approval again. Log actual installs/downloads
  in `SETUP_LOG.md`, including sources, versions, and destinations.
- A Gemini connection or configured credential does not authorize a live request.
  Obtain consent covering provider/model, transmitted inputs, request/token
  limits, and spending restrictions before live verification or operation.
- Include retries and helper-agent calls in the authorized usage budget. Preserve
  provider and project rate limits, ledgers, cooldowns, and request caps.
- Do not bypass quota through resets, repeated restarts, other keys/providers, or
  silent paid fallback. Never expose credentials or fabricate model responses.
- When live checks are unauthorized or unavailable, use clearly labeled eligible
  mocks and report real-model behavior as unverified.

## Observable reports

- Approved running modes must produce or update the HTML record after each step,
  including partial failures and recovery. Distinguish scripted, agent-controlled,
  mocked, replayed, Codex/MCP, and standalone operation accurately.
- Show actual observable tool/model inputs and outputs, steps, budgets, object
  sightings, identity/correction history, evidence links, and recorded decisions.
  Do not invent events, private reasoning, hidden model thoughts, or success.
- Preserve failure and recovery records; an interrupted report must remain honest
  about the last completed step and unfinished work.
- Regenerating a report reads saved records only: no inference, rendering, or
  changes to the authoritative investigation or memory store.
- Keep reports usable offline and escape untrusted text and embedded data safely.
  HTML is a view of saved records, not the authoritative memory/checkpoint store.

## Verification

Run relevant existing checks from this checkout using its existing environment:

```sh
.venv/bin/python -m pytest -q
.venv/bin/python tools/mcp_smoke.py
```

The first command runs the Python test suite. The second starts a local MCP
client/server smoke check and lists tools. It does not verify scene semantics.
Run checks proportional to the change and report actual results and limitations.

Only on a supported Mac with the required existing Xcode/Metal tooling, use the
project-local Swift build procedure in README.md. Its release-build check is:

```sh
DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer swift build -c release --package-path native
```

This selects Xcode for that command and builds the native renderer. The complete
README procedure also sets project-local build/cache paths. On Linux, report the
Mac check as unsupported; do not install system tooling or change the machine to
satisfy it. Start with modest scenes and inspect available memory before heavy runs.

## Documentation, logging, and recovery

- Apply the parent's implementation-log format in this owning repository:
  `LOGS.md` is a concise dated index with relative links, and
  `logs/<task-name>.md` records purpose, changed files, decisions/reasons,
  downloads, dates/session IDs, actual verification, limitations, and commits.
- Continue the same task record, preserve older records, and mark unavailable
  facts as unknown/not available. Never invent results or commit hashes.
- Keep README.md as the short entry point. Document new working run modes with
  prerequisites, explained commands, expected outputs, recovery, and whether
  they download models or transmit data. Mark unexecuted examples unverified.
- Explain in README.md that `LOGS.md` indexes implementations and `IDEAS.md`
  records architectural proposals. Add new ideas only when actually conceived;
  mark `[implemented]` only after delivery.
- Write resumable local checkpoints before stopping or nearing limits: completed
  changes, pending work, checks, blockers, and the next concrete action.
- Follow parent Git rules: inspect status/diffs/branch/remotes, stage only this
  task's changes, record tested milestone commits, and report real commit/push
  status. Never discard unrelated work or delete non-temporary outputs without
  explicit permission. Do not bypass approvals, sandboxing, or usage limits.

## Handoffs and reviews

- Keep CONTINUATION_HANDOFF.md to one page: current state, next action,
  frozen work, external gates, rules, commands. When updating it, rewrite it;
  do not add "latest continuation" blocks. Move superseded history to
  HANDOFF_HISTORY.md.
- List only facts verified now (commit hash, test counts from the last real
  run). Mark anything unverified as unverified. Do not carry forward a rough
  progress estimate as a status line.
- Name one concrete next action, and what is frozen. New findings go under
  "Known gaps"; do not fix them without Prashansa's approval.
- Write the handoff before nearing usage limits, as the existing checkpoint
  rule requires, but keep it short enough to read in a minute.
- Run the existing checks once per milestone. Request an independent review
  only for changes that could affect evaluation validity (answer-key leakage,
  protocol changes, cross-question state), and tell Prashansa why first. A
  review is not required after each fix, and a fix found by review does not by
  itself require another review.

## Porting

Prashansa's task: port [function names] from [source path] into [destination
path]. This port is approved; implement it after you show me the plan.

Goal: behavior-identical port, not a redesign.

Rules for this task:
1. Before changing anything, list: the functions to port, what each one reads
   and writes (files, state, saved evidence, session/budget records), and
   which destination helpers already do the same job. Show me this plan and
   wait for my OK.
2. Copy the logic as-is first. Change only what the destination needs:
   imports, names, config access, and existing helpers that replace
   duplicated ones. Do not rename, restructure, split, or "improve" logic
   that works. Behavior changes go in a separate later step that I approve.
3. Add no new classes, frameworks, config options, or abstractions. Keep
   ordinary functions with explicit inputs. If you think an abstraction is
   needed, name the concrete problem and ask first.
4. Do not touch the source or any donor repo. Keep license and attribution
   for copied code.
5. Verification: run the same input through the old and new function and
   compare outputs (deterministic, no model calls, no Gemini). One
   comparison test per ported function is enough. Do not write a broad new
   test suite or start independent reviewer agents. Run the existing
   `pytest` and `tools/mcp_smoke.py` once at the end.
6. Keep the on-disk formats for evidence, sessions and decision records
   unchanged, and tell me if the port would alter any of them.
7. Do not do geometry/multiview work, garage integration, or unrelated
   cleanup. Report anything you notice instead of fixing it.
8. Port in small steps (one function at a time), commit each tested step,
   and write a checkpoint note before stopping.

When done, report: functions ported, what was changed from the original and
why, the comparison results, and anything unverified.

## Scope control and reviews

- Do not start independent reviewer agents or fresh-review rounds unless
  Prashansa asks. A fix found by a review does not by itself require another
  review.
- Request an independent review only for a change that could affect evaluation
  validity: answer-key or reference leakage, changes to seeds, splits, question
  sets, rubrics, metrics or budgets, or state leaking across questions, runs or
  tracks. Tell Prashansa why before starting one.
- Run the existing checks (`pytest` and `tools/mcp_smoke.py`) once per
  milestone, then commit. Add a test only for behavior the current change
  touches; do not grow the suite for its own sake.
- Do not start geometry, multiview, or provider-scope hardening unless the
  current task requires it. When you notice another gap, record it under "Known
  gaps" in CONTINUATION_HANDOFF.md instead of fixing it, and tell Prashansa.