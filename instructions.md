# Fly.io Deployment Template

Du deployest eine PulseOS App auf Fly.io als Docker Container.

## Neue Dateien erstellen
```
apps/<name>/
├── index.html      ← BEHALTEN oder als Wrapper umbauen
├── manifest.json   ← BEHALTEN, fly-io zu stacks hinzufuegen
├── Dockerfile      ← NEU
├── fly.toml        ← NEU
└── .dockerignore   ← NEU
```

## Dockerfile (Node.js App)
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 8080
CMD ["node", "server.js"]
```

## fly.toml
```toml
app = "pulse-app-NAME"
primary_region = "fra"

[build]

[http_service]
  internal_port = 8080
  force_https = true
  auto_stop_machines = "stop"
  auto_start_machines = true
  min_machines_running = 0
  processes = ["app"]

[[vm]]
  memory = "256mb"
  cpu_kind = "shared"
  cpus = 1
```

## .dockerignore
```
node_modules
.git
*.md
```

## Deploy-Befehle
```bash
# Einmalig: App erstellen
fly launch --name pulse-app-NAME --region fra --no-deploy

# Deployen
fly deploy

# Logs
fly logs

# Secrets setzen
fly secrets set KEY=VALUE
```

## Regeln
- App muss auf Port 8080 lauschen (Fly.io Standard)
- Fly.io Token in PulseOS Env als FLY_API_TOKEN speichern
- Auto-stop auf 0 Maschinen wenn idle (kostenguenstig)
- Region fra (Frankfurt) fuer Europa
- manifest.json: stacks:["fly-io"] hinzufuegen