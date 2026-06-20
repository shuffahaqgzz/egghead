# Agent Deployment Template

## Per-Agent Setup Checklist

For each new agent (ATLAS, YORK, LILITH, BONNEY, STUSSY, SHAKA):

### 1. Create Profile
```bash
hermes profile create <agent_name>
```

### 2. Copy Config
```bash
cp ~/.hermes/profiles/stella/config.yaml ~/.hermes/profiles/<agent_name>/config.yaml
```
Then patch discord channels:
```bash
# Edit config.yaml — set:
# discord.free_response_channels: '<channel_id>'
# discord.allowed_channels: '<channel_id>'
```

### 3. Write .env
```
DISCORD_BOT_TOKEN=<bot_token>
DISCORD_HOME_CHANNEL=<channel_id>
DISCORD_ALLOWED_USERS=705848263948894289,1502701102083080282
DISCORD_ALLOW_BOTS=all
TERMINAL_DOCKER_IMAGE=nikolaik/python-nodejs:python3.11-nodejs20
TERMINAL_ENV=local
OPENROUTER_API_KEY=<from stella .env>
OPENCODE_GO_API_KEY=<from stella .env>
TINKER_API_KEY=<from stella .env>
WANDB_API_KEY=<from stella .env>
OLLAMA_API_KEY=<from stella .env>
CAMOFOX_URL=http://10.50.0.111:9377
FIRECRAWL_API_KEY=<from stella .env>
```

Note: DISCORD_ALLOWED_USERS = Gilang (705848263948894289) + STELLA bot (1502701102083080282)
DISCORD_ALLOW_BOTS=all so STELLA can send messages to the agent.

### 4. Write SOUL.md
See soul-drafts/ for templates per agent.

### 5. Create Systemd Service
```bash
cat > ~/.config/systemd/user/hermes-gateway-<agent_name>.service << 'EOF'
[Unit]
Description=Hermes Agent Gateway - <AGENT_NAME_UPPER>
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/home/infra/.hermes/hermes-agent/venv/bin/python -m hermes_cli.main --profile <agent_name> gateway run --replace
Restart=on-failure
RestartSec=5
Environment=HOME=/home/infra
WorkingDirectory=/home/infra

[Install]
WantedBy=default.target
EOF
```
Replace `<agent_name>` and `<AGENT_NAME_UPPER>`.

### 6. Enable + Start
```bash
systemctl --user daemon-reload
systemctl --user enable hermes-gateway-<agent_name>.service
systemctl --user start hermes-gateway-<agent_name>.service
```

### 7. Verify
```bash
systemctl --user is-active hermes-gateway-<agent_name>.service
hermes --profile <agent_name> chat -q "What is your name and role?"
```

---

## Bot IDs Reference

| Agent | Bot ID | Channel |
|---|---|---|
| edison | 1501608989656482003 | 1501447521086476348 |
| pythagoras | 1501608874132897802 | 1501447723000266793 |
| atlas | 1501609076503740466 | 1501447906396344320 |
| stella | 1502701102083080282 | 1502667343279423619 |
| kumachii | 1485148050685956117 | 1502667177218539560 |
| shakaa | 1500373431382704259 | 1502666915808542951 |
| lilith | 1501609251716599909 | 1502667034532774041 |
| york | 1502701165765332992 | 1502667093571534958 |
| bonney | 1502701232949694535 | 1502926214413811792 |
| stussy | 1502739573636075723 | 1502926523789742191 |

## STELLA Allowed Users Update

When adding new agents, update STELLA .env:
```
DISCORD_ALLOWED_USERS=705848263948894289,1485148050685956117,<new_bot_ids_that_send_to_stella>
```

Currently STELLA accepts from: Gilang + KUMACHII bot.
