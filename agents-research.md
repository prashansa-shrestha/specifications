keep implementation really simple and accurate. i dont want the BEST solution to everything but instead a simple solution that works. it doesn't need to be 100% fault proof but enough that it works accurately in a research codebase

This is research code, not a library.
- Smallest change that satisfies the request.
- Don't refactor or reformat code outside the task; mention it instead.
- Never alter seeds, splits, or metric definitions silently.
- Before coding: state a plan (files touched/created). After: map each requirement to where it's done and list assumptions.

### my device's hardware specification where the agent is built/run

x. Never modify the rest of this machine.

### Allowed
- Download dependencies, datasets, model weights, configs, and tools ONLY into:
  - the project directory (./), or
  - ./.cache/, ./.venv/, ./node_modules/, ./third_party/, ./downloads/
- Create and use isolated environments:
  - Python: `python3 -m venv .venv` (or `uv venv`), then `.venv/bin/pip install ...`
  - Node: local installs only (`npm install`, never `-g`)
  - Conda: a new named env only (`conda create -n <project>-env`)
  - Rust/Go: project-local target/vendor directories
- Use `curl`/`wget`/`git clone`/`huggingface-cli download` with an explicit
  output path inside the allowed directories.
- Download only from official sources (PyPI, npm, GitHub releases, Hugging Face,
  the project's own docs). Verify checksums/signatures when they are provided.

### Forbidden (ask me first, never do silently)
- `sudo`, `apt`/`apt-get`/`snap` installs, or anything that needs root
- `pip install` outside a venv, `pip --break-system-packages`, `npm -g`
- Editing system or user-level config: /etc, ~/.bashrc, ~/.zshrc, ~/.profile,
  ~/.config, ~/.ssh, ~/.gitconfig, crontab, systemd units, PATH, env vars
- Changing GPU drivers, CUDA, kernel modules, or system Python
- Touching, moving, or deleting files outside the project directory
- Piping remote scripts into a shell (`curl ... | sh`, `curl ... | bash`)
- Overwriting existing files without showing me a diff first

### Before downloading anything
1. State what you're downloading, from where, how large it is, and the target path.
2. If it's over ~500 MB, or from a source that isn't official, wait for my approval.
3. If something truly requires a system-level change, stop and give me the exact
   command and the reason. Do not run it yourself.

### After setting up
- Log every install/download to ./SETUP_LOG.md (what, source, version, path).
- Pin versions in requirements.txt / package-lock.json / environment.yml so the
  setup is reproducible and can be removed by deleting the project folder.
- Confirm that nothing outside the project directory was changed.

## Implementation logging

After each meaningful implementation milestone, update both:

1. `LOGS.md`(create it if not created yet): Keep a concise index of implementations. Each entry should contain:
   - Implementation date.
   - Task name and a brief summary of what changed.
   - A relative Markdown link to its detailed record in `logs/`.

2. `logs/<task-name>.md`(create it if not created yet): Record the implementation details:
   - Purpose and what was implemented.
   - Files changed and how the changes work.
   - Downloads, when applicable: what, source URL, and storage location.
   - Prashansa’s explicit decisions and their stated reasons.
   - Implementation dates and Codex chat/session IDs, when available.
   - Verification performed, results, limitations, and unfinished work.
   - Relevant commit hashes, when available.

Keep explanations simple and proportional to the task. Update an existing
task log when continuing the same implementation; create a separate log
for a distinct task. Creating these documentation files is authorized.

Keep implementation details out of `LOGS.md`; place them in `logs/`.
Preserve existing records, avoid duplicates, and use relative links.

Use only available conversation history and verifiable repository evidence.
Never invent dates, decisions, sources, verification results, or session IDs.
Mark unavailable information as "unknown/not available." Clearly distinguish
completed implementation from proposed or unfinished work.

If this logging requirement conflicts with the current task or another
repository instruction, explain the conflict and ask Prashansa before proceeding.


## New Findings

if a new idea for the architecture of the scene agent is conceived, add it to IDEAS.md (create it if not created yet), and if the user implements it, write [implemented] beside it

## New functionalities

if new ways of running the repository is made, write it in the README.md
in the readme also define what IDEAS.md and LOGS.md mean

If any instruction here conflicts with a task, follow this section and ask me.