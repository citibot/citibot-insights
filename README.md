# Citibot Insights

Standalone Node 23 CLI for five fixed, privacy-suppressed US fleet metrics. The AU-routed organization is explicitly excluded. This is a runnable internal handoff, not a service and not a municipality/customer tool. It has no HTTP server, token flow, Redis dependency, raw SQL command, or organization filter.

## Owner setup

Only the Citibot owner named in the credential-custody record may hold this ZIP or its dedicated US aggregate-reader DSN. This build uses the trusted-owner boundary: database grants block raw relations and writes, while the owner is trusted not to bypass the CLI with overlapping aggregate queries. On that owner's encrypted, managed device:

```bash
chmod 700 <root>
cd <root>
cp .env.example .env
chmod 600 .env
```

The owner manually fills the one empty value in `.env` from the approved credential channel. Never paste a DSN into chat, a prompt, shell argv, docs, `CLAUDE.md`, source control, CI, cloud sync, or logs. Never reuse a Citibot app-owner, migration-owner, org-bypass, or raw-reader URL. Presence-only check: `test -s .env && test "$(stat -f %Lp .env 2>/dev/null || stat -c %a .env)" = 600`; do not print or inspect the file.

```bash
node citibot-insights.mjs --help
node citibot-insights.mjs catalog --json
node citibot-insights.mjs doctor --json
node citibot-insights.mjs query top-departments --days 90 --limit 3 --json
node citibot-insights.mjs query sentiment --start 2026-01-01 --end 2026-03-31 --json
```

## Fixed catalog

`fleet-overview`, `top-departments`, `top-topics`, `channel-mix`, `sentiment`.

Ranges are inclusive UTC dates, default and maximum 90 days. Only top lists accept `--limit` (default 10, maximum 50). [Metric semantics](docs/metric-contract.md), [privacy rules](docs/privacy-contract.md), [operations](docs/operations.md), and [threat model](docs/threat-model.md) are part of the contract.

The CLI is the reviewed safe workflow, but the copied DSN is a bypass capability: its holder could use another PostgreSQL client to read US aggregate atoms and issue overlapping queries. Database grants block raw tables and writes; they cannot force the CLI's suppression. If non-bypassable privacy is required, stop using this package and restore a trusted gateway.

## Development gates

```bash
nvm use 23
npm ci --ignore-scripts
npx tsc --noEmit
npx vitest run
npx eslint . --max-warnings=0
python /Users/sabino/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/citibot-fleet-insights
npm run build
npm run package:zip
```

Tests use only ephemeral databases and synthetic credentials. No production provisioning or live DSN belongs in this repository.
