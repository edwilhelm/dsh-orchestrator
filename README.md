# DeepSeek Harness — Orchestrator Mode

Orchestrator mode for the [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness). A full coding agent that runs as an **orchestrator**: the user independently chooses two model routes, and every spawned sub-agent uses its own route.

## Repository Structure

```
.
├── agent-presets/orchestrator/    # Orchestrator mode — dual-model orchestrator setup
│   └── plugins/                   # orchestrator-models, orchestrator-skills
├── examples/                      # Example configuration snippets
├── install.ps1 / install.sh        # One-command installer (backup + auto-detect)
├── README.md
└── LICENSE
```

---

## Orchestrator Mode (`agent-presets/orchestrator/`)

Full coding agent that runs as an **orchestrator**. The user independently chooses two model routes:
- **Orchestrator route** — the model the orchestrator runs on
- **Sub-agent route** — the model every spawned sub-agent uses

Neither choice is exposed as a model-facing tool. The orchestrator sees skill descriptions only and hands the full SKILL.md to spawned sub-agents.

**Configuration:** Add an `orchestrator:` block to your `$DSH_HOME/settings.yaml` (see `examples/settings.orchestrator.example.yaml`).

**Plugins:**
- `plugins/orchestrator-models/` — Reads `$DSH_HOME/settings.yaml` namespace `orchestrator` for the dual-model routes. Never exposes a model-facing write tool.
- `plugins/orchestrator-skills/` — Orchestrator sees skill catalog descriptions only; spawned sub-agents receive the full SKILL.md.

---

## Installation

### Prerequisites

- DeepSeek Harness (v0.1.0-rc.6 or later)
- `$DSH_HOME` set to `~/.dsh` (default)

### Option A: install script (recommended)

**Windows:**
```powershell
.\install.ps1
```

**macOS / Linux:**
```bash
chmod +x install.sh
./install.sh
```

The script will:
1. Detect your `$DSH_HOME` (defaults to `~/.dsh`, respects the `$DSH_HOME` env var)
2. **Back up** any existing files it's about to overwrite into a timestamped folder (`$DSH_HOME/.dsh-orchestrator-backup-<timestamp>`)
3. Copy the Orchestrator preset and plugins
4. Print next steps

**Flags:**
- `-DryRun` / `--dry-run` — preview without changing anything
- `-Yes` / `--yes` — skip prompts

### Uninstalling

Both installers restore the previous state from the latest backup:

**Windows:**
```powershell
.\install.ps1 -Uninstall
```

**macOS / Linux:**
```bash
./install.sh --uninstall
```

What `--uninstall` does:
1. Finds the newest backup folder (`$DSH_HOME/.dsh-orchestrator-backup-<timestamp>`)
2. **Restores** every backed-up file to its original location
3. **Removes** presets that were created by the installer and didn't exist before
4. Deletes the backup folder (use `-KeepBackup` / `--keep-backup` to keep it)

If no backup exists (e.g., a clean install with nothing pre-existing), it prints manual removal instructions instead. Combine with `-DryRun` / `--dry-run` to preview before touching anything.

### Option B: manual copy

```bash
# Copy the preset
cp -r agent-presets/orchestrator $DSH_HOME/.agent-presets/
```

Then add the `orchestrator:` block to `$DSH_HOME/settings.yaml` (see `examples/`).

### Configure orchestrator routes

Add an `orchestrator:` block to your `$DSH_HOME/settings.yaml`:

```yaml
orchestrator:
  orchestrator:
    provider: grok
    model: grok-4.6
  subagent:
    provider: clinepass
    model: cline-pass/deepseek-v4-flash
```

### Restart the harness

```bash
dsh web restart
```

---

## Usage

Once installed, select **Orchestrator** from the mode picker in the Web GUI or CLI:

| Preset | ID | Description |
|--------|----|-------------|
| Orchestrator | `orchestrator` | Dual-model orchestrator setup |

---

## License

MIT