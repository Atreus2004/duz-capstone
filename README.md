# FIM Agent

Lightweight file integrity monitoring (FIM) agent with CLI and web UI options for watching directories, persisting events, and exporting alerts.

## Configuration overview

Copy `config/config_example.yaml` to `config/config.yaml` and adjust the monitored paths, exclusions, and alert thresholds to match your environment. The same configuration file controls both the CLI agent and the optional web server entry point.

Key logging settings:

```yaml
log_file: "./logs/fim_agent.log"
log_format: "json"
```

- `log_file` captures the persisted log output.
- `log_format` accepts `json`, `text`, or `wazuh`.
- When set to `json`, logs are emitted as JSON Lines (one JSON object per line) to both stdout and the configured log file.

## JSON logging

All application logs can be switched to JSON without code changes by setting `log_format: "json"` in `config/config.yaml`.

### Linux / macOS (bash)
```bash
# Ensure the config exists and force JSON logging
cp config/config_example.yaml config/config.yaml
python - <<'PY'
from pathlib import Path
cfg = Path("config/config.yaml")
text = cfg.read_text().splitlines()
text = ["log_format: \"json\"" if line.strip().startswith("log_format:") else line for line in text]
cfg.write_text("\n".join(text) + "\n")
PY

# Start the agent (press Ctrl+C to stop)
python -m fim_agent.cli.main --config config/config.yaml run-agent
```

### Windows (PowerShell)
```powershell
# Ensure the config exists and force JSON logging
Copy-Item config/config_example.yaml config/config.yaml -Force
(Get-Content config/config.yaml) -replace '^log_format:.*', 'log_format: "json"' | Set-Content config/config.yaml

# Start the agent (Ctrl+C to stop)
python -m fim_agent.cli.main --config config/config.yaml run-agent
```

### Verifying JSON output

1. Start the agent as shown above (it logs to stdout and `logs/fim_agent.log`).
2. Confirm each line is valid JSON:
   - Linux/macOS: `head -n 3 logs/fim_agent.log`
   - Windows: `Get-Content logs\\fim_agent.log -TotalCount 3`

The lines should each be standalone JSON objects suitable for forwarding to a log aggregator.
