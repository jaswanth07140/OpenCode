# Machine + Project State — Knowledge Handoff

Persisted via OpenCode `instructions` (config key `instructions`). Loaded into every new session. Keep it short; put details in files, not here.

## Hardware / OS / tooling (this machine)
- Linux x86_64 container (Debian 13). User uid 1000, home /home/jaswanth072012. No systemd system units — user units in ~/.config/systemd/user work.
- Node is installed at /home/jaswanth072012/.local/node (v24.22.3, v22 copy deleted). PATH via ~/.bashrc (`~/.local/node/bin`). `git`, `npm`, `npx`, `docker`, `bun` are NOT installed. Only python3, curl, tar, xz available.
- Storage: /dev/vdb, 7.9G total, ~3.3G used. Disk discipline: no big repo downloads (tarballs ~10M / node_modules ~460M), clean up temp after use.

## OpenCode (v2.3.0)
- Global config: /home/jaswanth072012/.config/opencode/opencode.json. V2 schema (`$schema`, `providers`, `mcp.servers`, `agents`, ...). V1-style `provider`/`npm` shape is deprecated.
- Agents: Markdown files in /home/jaswanth072012/.config/opencode/agents/<slug>.md with YAML frontmatter (name, description, mode, color). ID = filename. Subagent mode only. OpenCode ~119 agent cap (per upstream bug #27988). 87 agents currently installed here (agency-agents: engineering 66 + security 12 + testing 9).
- Key collision to avoid: ECC already defines `code-reviewer` (JSON, prompts/agents/code-reviewer.txt). Renamed agency copy to `agency-code-reviewer.md` / name "Code Reviewer (Agency)". Don't re-import raw agency `code-reviewer.md`.

## FreeLLMAPI (running) — state as of this session
- Source: /home/jaswanth072012/Default Project/freellmapi (was downloaded from tashfeenahmed/freellmapi, main branch, MIT; built via npm ci + npm run build).
- Service: systemd --user unit /home/jaswanth072012/.config/systemd/user/freellmapi.service (enabled, Wants default.target, Restart=on-failure, WorkingDirectory .../freellmapi/server, ExecStart=~/.local/node/bin/node dist/index.js). Linger enabled. Running (PID ~17263). Logs in journalctl --user -u freellmapi; freellmapi/freellmapi.log no longer written by unit.
- Health: listening on http://127.0.0.1:3001 (HOST=127.0.0.1 in freellmapi/.env; PORT=3001; ENCRYPTION_KEY in freellmapi/.env; .env is gitignored). Dashboard + API + MCP mount at /mcp (MCP surface OFF by default; needs PUT /api/settings/enable-mcp).
- Unified key: stored at /home/jaswanth072012/.config/opencode/freellmapi.key (mode 600, no trailing newline). Format freellmapi-<24hex>. Get from GET /v1/models with Bearer. DB: freellmapi/server/data/freeapi.db. First-run setup code (when dashboard unclaimed): JQHLUHFPMX.
- Provider keys: NOT yet added. This is the only missing piece. Recommended next adds (free tiers): Google AI Studio (https://aistudio.google.com/apikey), Groq (https://console.groq.com/keys), NVIDIA NIM (https://build.nvidia.com/settings/api-keys). NOTE: Cerebras is advertised in the dashboard but has 0 seeded models in this build's catalog — don't waste a key there. OpenCode provider wired: freellmapi via @opencode/ai/providers/openai-compatible, settings.baseURL http://127.0.0.1:3001/v1, apiKey {file:~/.config/opencode/freellmapi.key}, models auto+fusion (context 32768, output 8192, tools true).
- Router catalog (seeded, monthly snapshot): 42 platforms / 483 models / 329 /v1/models ids incl. `auto`, `fusion`. intelligence_rank: lower = smarter (range 17-203; Gemini 3.8 Flash ≈25, GLM-5.3-flash ≈23 via nvidia). Groq RPD up to 14400 on llama-prompt-guard; google ~20 RPD/model; electronhub 76 models/55 with tools but shared credits.
- Error signature when no keys: {"error":{"message":"No candidate model has a configured, usable provider key...","code":"no_providers_configured"}} — expected until keys are added.

## Agency-agents (installed) — state as of this session
- Source: github.com/msitarzewski/agency-agents (MIT; 168K stars; default branch main). 330 agent md files / 23 divisions; converted to OpenCode format by its own scripts/convert.sh (--tool opencode → integrations/opencode/agents).
- Installed: 87 files into /home/jaswanth072012/.config/opencode/agents/ (engineering 66, security 12, testing 9). Converted outputs then deleted from /tmp; only the 87 .md files remain there (1.6M).
- OpenCode capability: ~119 agents cap; at 87 + 27 ECC JSON agents = 114, close to cap.

## gstack (not yet installed) — state as of this session
- Source: github.com/garrytan/gstack (MIT; 141K stars; size ~266MB but ~80MB working tree). 23 slash commands (/office-hours, /plan-ceo-review, /plan-eng-review, /review, /design-consultation, /deslop-shared-libs, /write-skill, /test-audit, /verify, /design-review, /devex-review, /design-shotgun, /design-html, /design-motion, /qa, /qa-only, /pair-agent, /cso, /ship, /land-and-deploy, /modernize, /canary, /benchmark, /eval-plan, /document-release, /document-generate...).
- OpenCode host: experimental; ./setup --host opencode → ~/.config/opencode/skills/gstack-*/ + /gstack-* commands (advisory, not blocked). Requires bun (not installed) and git (not installed) and for /qa /browse a browser (bundled by setup; Aside recommended on macOS). Costs ~300MB+ total → user declined full install in favor of markdown-only.
- User decision pending: markdown-only subset install was recommended to user; not yet executed.

## Latest session arc (2026-11-15, continued handoff)
- User pasted github.com/tashfeenahmed/freellmapi + ?mcp_token=... (decoded token, pid/sid/ax/ts/exp) → assessed as not a real FreeLLMAPI artifact (freellmapi.co/mcp 404s, no mcp_token in source) and burned.
- Installed FreeLLMAPI locally: Node via npm ci + npm run build in /home/jaswanth072012/Default Project/freellmapi; ENCRYPTION_KEY/PORT/HOST in freellmapi/.env; systemd user unit freellmapi.service (enabled, linger on, Restart=on-failure); DB server/data/freeapi.db; first-run setup code JQHLUHFPMX; unified key at /home/jaswanth072012/.config/opencode/freellmapi.key (freellmapi-<24hex>, no trailing newline).
- Wired OpenCode provider freellmapi (V2 @opencode/ai/providers/openai-compatible, baseURL http://127.0.0.1:3001/v1, apiKey {file:...}); models auto+fusion. Smoke-tested via subagent → got expected no_providers_configured (needs provider keys).
- User pasted msitarzewski/agency-agents → installed 87 agents (engineering 66 + security 12 + testing 9) into /home/jaswanth072012/.config/opencode/agents/; fixed code-reviewer ID collision by renaming to agency-code-reviewer.md (display name "Code Reviewer (Agency)").
- User pasted garrytan/gstack → assessed install cost (~300MB+, needs bun+git+chrome, OpenCode host experimental); user chose markdown-only subset; NOT executed.
- User asked about ECC → no repo clone; installed ecc-universal 2.2.3 at ~/.config/opencode (files predate session; npm latest is also 2.2.3). OpenCode DB only holds session/instruction indexes, not ECC content.
- User asked whether deleted downloads are remembered → answered: files gone, conversation text only; later sessions need persisted instructions file.
- Wrote + registered SESSION-KNOWLEDGE.md (instructions list) as the cross-session memory; confirmed live via system-update; re-finished after edits.
- Final storage cleanup: rm -rf ~/.local/node22 (199M), npm cache clean --force (97M), npm prune --omit=dev (63M), rm freellmapi/freellmapi.log; disk back to ~3.3G used/4.4G free; /tmp cleaned.
- Post-prune verification: systemctl --user restart freellmapi → active; dashboard 200; /v1/models 200 with key, 401 without key.

## Credentials / security notes
- A GitHub `mcp_token` URL param was pasted (eyJ… token, decodable JSON with pid/sid/ax/ts/exp) from tashfeenahmed/freellmapi. It matched nothing in the repo and /freellmapi.co/mcp 404s; treated as burned; never written to disk. Freellmapi unified key goes ONLY through /home/jaswanth072012/.config/opencode/freellmapi.key (no trailing newline).

## Repos consumed this session (research + integration)
1. github.com/tashfeenahmed/freellmapi — with ?mcp_token=... — installed locally; OpenCode provider wired; token deemed bogus/burned; missing provider keys remains the only blocker.
2. github.com/msitarzewski/agency-agents — 87 agents installed; ECC name-collision fixed.
3. github.com/garrytan/gstack — assessed, markdown-only path chosen, not executed (CEO/design/review/QA commands; OpenCode host experimental; requires bun+git+browser; ~300MB+; markdown-only chosen).
4. github.com/affaan-m/ECC — installed as npm package ecc-universal 2.2.3 at ~/.config/opencode (no clone; files dated Nov 6–12, npm latest is also 2.2.3); source of agents, instructions, skills, plugins.
