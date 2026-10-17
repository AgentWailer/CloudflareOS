# Dein Cloudflare OS

Dieses Repository ist deine eigene Kopie von
[Cloudflare OS](https://github.com/cloudflare/cloudflare-os). Ausgangspunkt ist
Upstream-Commit `8060cd9fde6bdec1159df46054f0a559e93822a1`.
Die ursprüngliche Apache-2.0-Lizenz bleibt erhalten.

## Lokal starten

Voraussetzungen: Node.js 24.19.0 und Corepack. Die genaue pnpm-Version einschließlich
Integritätsprüfung steht in `package.json`.

```sh
git clone https://github.com/AgentWailer/CloudflareOS.git
cd CloudflareOS
corepack enable
pnpm install --frozen-lockfile
CI=true pnpm run-local
```

Öffne auf deinem eigenen Rechner `http://localhost:8787`. Der lokale Start benötigt
keine Cloudflare-Anmeldung. Lege dein Konto selbst an; der Benutzername `admin`
bekommt in diesem lokalen Entwicklungsmodus Zugriff auf `/admin`. Dort kannst du
unter anderem den Namen deiner Instanz, Logo und Farben einstellen.

Die lokalen Daten liegen unter `.wrangler`. Diese Verzeichnisse und die generierten
Entwicklungskonfigurationen sind ignoriert und gehören nicht ins Repository.
Beende den Server mit Strg+C. Für die Entwicklung mit Vite starte stattdessen
`pnpm dev-server` und `pnpm dev-client` in getrennten Terminals.

In dieser Codex-Cloud-Umgebung liegen beschreibbare Werkzeug-Caches unter
`/workspace`. Aktiviere vor Befehlen die vorbereitete Umgebung:

```sh
source /workspace/.cloudflareos/env.sh
cd /workspace/CloudflareOS
CI=true pnpm run-local
```

## KI und Verbindungen einrichten

Der lokale Server startet ohne KI-Schlüssel. Für echte Agentenantworten verbindest
du in der Oberfläche einen Modellanbieter mit deinem eigenen Zugang oder richtest
einen Cloudflare AI Gateway ein. Für Workers AI starte mit
`CI=true pnpm run-local --use-workers-ai-binding`; dafür ist eine gültige
Cloudflare-Anmeldung erforderlich.

Optionale Serverwerte liest der Entwicklungsserver aus einer ignorierten
`.dev.vars` im Repository-Hauptverzeichnis. Trage dort nur benötigte Werte ein;
Zugangsdaten gehören weder ins Git noch in Chatnachrichten. Die einzelnen
Gatekeeper dokumentieren ihre OAuth-Einrichtung jeweils in ihrer `README.md`.

Weitere Details: [AI Gateway](ai-gateway-billing.md),
[Anmeldung und öffentlicher Betrieb](public-server.md).

## In deinem Cloudflare-Konto betreiben

Cloudflare bietet zwei offizielle Wege:

- [Geführte Installation](https://os.cloudflare.app/deploy): stellt die offizielle
  Version in deinem Cloudflare-Konto bereit. Dieser Weg übernimmt keine lokalen
  Codeänderungen aus deinem Repository.
- [Deployment-Starter](https://github.com/cloudflare/cloudflare-os-starter):
  verwaltet eigene Worker-Namen, Domain, Anmeldung und eine festgelegte
  Cloudflare-OS-Version. Für eine eigene Codeversion muss dessen
  `cloudflare-os`-Submodul auf den gewünschten Commit dieses Repositorys zeigen;
  gleiche dabei die Werkzeug- und Katalogversionen nach der Upgrade-Anleitung ab.

Der lokale Wrangler-Entwicklungsserver ist für Entwicklung gedacht. Für eine
öffentliche Instanz benötigst du die Bereitstellung in deinem Cloudflare-Konto,
die gewünschte Adresse und die Administrator-Identität. Der Starter dokumentiert
die nötigen Dienste und die Einrichtung mit Cloudflare Access.

Diese Repository-Einrichtung erzeugt noch keine öffentlichen Cloudflare-Ressourcen.

## Änderungen prüfen und Upstream aktualisieren

```sh
pnpm lint
pnpm --filter @gadgets/router test:run
```

`pnpm lint` umfasst Lint, Skript-Typen, generierte Konfigurationen und den
Workspace-Build. Weitere vorhandene Tests beschreibt [testing.md](testing.md).

Wenn das Upstream-Remote noch fehlt, füge es einmal hinzu:

```sh
git remote add upstream https://github.com/cloudflare/cloudflare-os.git
```

Hole Änderungen mit `git fetch upstream`. Prüfe und integriere sie anschließend
bewusst; eigene Änderungen und lokale Daten sollen dabei erhalten bleiben.
