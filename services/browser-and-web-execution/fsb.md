# FSB (Full Self Browsing)

> **"DOM-first AI browser automation for Chrome and MCP clients."**

| | |
|---|---|
| **Website** | https://full-selfbrowsing.com |
| **Docs** | https://github.com/fullselfbrowsing/FSB/blob/main/mcp/README.md |
| **GitHub** | https://github.com/fullselfbrowsing/FSB |
| **Stars** | [![GitHub Stars](https://img.shields.io/github/stars/fullselfbrowsing/FSB?style=social)](https://github.com/fullselfbrowsing/FSB) |
| **npm** | https://www.npmjs.com/package/fsb-mcp-server |
| **Classification** | `agent-native` |
| **Category** | [Browser & Web Execution Services](README.md) |
| **License** | MIT |
| **Latest-month signal** | [fsb-mcp-server v0.11.2](https://github.com/fullselfbrowsing/FSB/releases/tag/mcp-v0.11.2) released 2026-10-14 and served as `latest` by the [npm registry](https://registry.npmjs.org/fsb-mcp-server/latest); last GitHub push 2026-10-20; [full-selfbrowsing.com](https://full-selfbrowsing.com), the [MCP README](https://github.com/fullselfbrowsing/FSB/blob/main/mcp/README.md) and the [ClawHub listing](https://clawhub.ai/lakshmanturlapati/full-selfbrowsing) HTTP 200 (verified 2026-10-20) |
| **Verified at** | 2026-10-20 |

---

## Official Website

https://full-selfbrowsing.com

---

## Official Repo

https://github.com/fullselfbrowsing/FSB — Chrome extension (`extension/`), MCP server (`mcp/`, published as `fsb-mcp-server`) and the `fsb` Agent Skill (`skills/fsb/`)

---

## How to Use (Agent Onboarding)

**Interaction pattern:** `MCP` + browser extension (local bridge)

**Quickest verified path:**

```bash
npx -y fsb-mcp-server@latest install --claude-code
```

The installer writes the MCP config for one client: `--claude-code`, `--claude-desktop`, `--cursor`, `--vscode`, `--windsurf`, `--codex`, or `--all`. Append `--dry-run` to preview the change. Other clients can use `"command": "npx", "args": ["-y", "fsb-mcp-server"]`.

The person then installs the FSB extension once from the [Chrome Web Store](https://chromewebstore.google.com/detail/badgafnfchcihdfnjneklogedcdkmjfk) and restarts the client. `npx -y fsb-mcp-server@latest doctor` checks that the bridge and the extension are connected. No FSB account or API key is needed; the calling agent's own model does the reasoning.

This is a one-time human setup. The MCP server and the extension bridge bind to the local machine only, so a hosted agent cannot reach FSB unless it runs on the same computer as the browser.

---

## Agent Skills

**Status:** ✅ Available

```bash
npx skills add fullselfbrowsing/FSB --skill fsb
```

OpenClaw users can install the same skill from ClawHub: https://clawhub.ai/lakshmanturlapati/full-selfbrowsing

| Skill | What It Teaches the Agent |
|---|---|
| `fsb` | When to use FSB instead of a plain web fetch, the tool decision tree, the multi-agent tab contract, the vault boundary and restricted-tab recovery, plus doctor and config-print scripts for OpenClaw and Hermes ([source](https://github.com/fullselfbrowsing/FSB/blob/main/skills/fsb/SKILL.md)) |

The skill supplies instructions and helper scripts. The person still installs the browser extension.

---

## MCP

**Status:** ✅ Available

| Detail | Value |
|---|---|
| **MCP Repo** | https://github.com/fullselfbrowsing/FSB/tree/main/mcp |
| **Package** | `fsb-mcp-server` on npm (`npx -y fsb-mcp-server`) |
| **Transport** | stdio; optional local Streamable HTTP via `npx -y fsb-mcp-server@latest serve` at `http://127.0.0.1:7226/mcp` |
| **Extension bridge** | WebSocket on `ws://localhost:7225`; the first server process becomes the hub and later processes relay through it |
| **Authentication** | No token. Binds to loopback only, and HTTP requests that carry an `Origin` header (web pages) are rejected |
| **Compatible Clients** | One-command installers for Claude Code, Claude Desktop, Cursor, VS Code, Windsurf and Codex; any stdio or Streamable HTTP MCP client by manual config |

---

## What It Does

FSB is an open-source browser extension plus a local MCP server that lets an AI agent drive the person's own signed-in browser (Chrome, Edge, Brave or Chromium 116+). The agent reads the live page as structure (DOM, element refs, selectors, ARIA labels, form state) instead of screenshots. It acts with clicks, typing, keys, scrolling, navigation, tabs, spreadsheet ranges and real file uploads, and FSB checks each step against the page.

The MCP surface has two modes. In manual mode the agent plans each step itself using about 70 tools ([tool reference](https://github.com/fullselfbrowsing/FSB/blob/main/mcp/README.md#tools-69-total)). In autopilot mode the agent calls `run_task` with a natural-language goal and FSB's own loop picks the steps, sending a progress heartbeat every 30 seconds. A capability catalog covering 128 apps (`search_capabilities` / `invoke_capability`) runs verified first-party API actions inside the person's authenticated session; entries that are not yet verified come back labelled pending instead of executing. Trigger watchers watch one page element for threshold, delta, regex or compound conditions and report back to the calling agent.

FSB also has a standalone mode in which a person types a task into the extension popup or side panel with their own model API key. That is a secondary surface; the MCP path needs no FSB key. FSB is intentionally not a stealth browser or scraper farm, and it works only while the browser and the extension are open.

---

## Why It Is Agent-Native

| Criterion | Evidence |
|---|---|
| **Agent-first positioning** | GitHub description: *"DOM-first AI browser automation for Chrome and MCP clients."* README Quick Start: *"FSB shines when your AI client drives it directly… no FSB API key needed (your MCP client's model handles the reasoning)"*; https://github.com/fullselfbrowsing/FSB |
| **Agent-specific primitive** | Per-agent tab ownership (calls into another agent's tab reject with `TAB_NOT_OWNED`); vault fills where the secret never crosses the MCP bridge; an `outcome: "unknown"` / `mayHaveExecuted: true` result instead of a silent retry when an action was sent but not confirmed; replay manifests; trigger watchers; [multi-agent contract](https://github.com/fullselfbrowsing/FSB/blob/main/mcp/README.md#multi-agent-contract-v080) |
| **Autonomy-compatible control plane** | After the one-time setup an agent completes full browser loops without the person clicking anything: step tools for agent-planned work, `run_task` for a delegated run, an agent concurrency cap (default 8, range 1–64), and confirmation gates kept for payments and replays |
| **M2M integration surface** | MCP over stdio (`npx -y fsb-mcp-server`) and local Streamable HTTP (`http://127.0.0.1:7226/mcp`); the extension connects to the bridge on `ws://localhost:7225` |
| **Identity / delegation** | The extension issues an `agent_id` to each MCP server process and binds tabs to it with ownership tokens. The agent works inside the person's signed-in browser, vault secrets are resolved inside the extension, and payment fills need the person's confirmation |

---

## Primary Primitives

| Primitive | Description |
|---|---|
| **Agent-owned tabs** | Each agent's tabs are bound to its `agent_id`. Calls into another agent's tab, an incognito tab or an unbound window reject with `TAB_NOT_OWNED`, `TAB_INCOGNITO_NOT_SUPPORTED` or `TAB_OUT_OF_SCOPE` |
| **Structured page reads** | `read_page`, `get_page_snapshot` and `get_dom_snapshot` return text, element refs, selectors and form state rather than pixels |
| **Verified actions** | Each action is checked against the page afterwards. An unconfirmed dispatch returns `outcome: "unknown"` with `mayHaveExecuted: true` and a next-step hint, so the agent inspects before retrying |
| **Delegated run** | `run_task` hands a natural-language goal to FSB's own loop, with 30-second progress heartbeats and a structured partial outcome if the extension's service worker is evicted |
| **Vault fill** | `fill_credential` and `use_payment_method` fill forms from the extension's encrypted vault; `list_credentials` and `list_payment_methods` return metadata only |
| **Capability catalog** | `search_capabilities` / `invoke_capability` over 128 apps, with readiness labels (`t1-ready`, `t1-guarded-fail-closed`, `learn-pending`, `discovery-pending`), recipe signature checks, an origin denylist and audit records without secrets |
| **Trigger watcher** | `trigger` arms a watch on one element in `live-observe` or `refresh-poll` mode, blocking or detached; `get_trigger_status`, `list_triggers` and `stop_trigger` manage it |
| **Session replay** | `get_session_replay` returns a verified replay manifest with step risks; `replay_session` reruns it after one approval in the side panel |

---

## Autonomy Model

```
Person adds the MCP server to a client and installs the FSB extension once
    ↓
Agent calls a tool; the extension issues its agent_id on the first call
    ↓
Agent opens or claims a tab, which is bound to that agent_id
    ↓
Agent reads the page as structure (read_page / get_page_snapshot)
    ↓
Agent acts (click / type_text / select_option / upload_file …), or hands the whole goal to run_task
    ↓
FSB checks that the page reacted; an unconfirmed action comes back as outcome "unknown"
    ↓
Agent ends with complete_task / partial_task / fail_task, which closes one history session
```

Several MCP clients can run at once against the same browser profile, each in its own tabs.

---

## Identity and Delegation Model

- **Agent identity** — the extension issues an `agent_id` (`crypto.randomUUID()`) to each MCP server process on its first tool call; clients cannot supply their own. Action tools also carry a `client` label and a `visual_reason`, which feed the visible overlay on the driven tab.
- **Delegated scope** — tabs are bound to one agent with a fresh ownership token on every bind, so a recycled tab ID cannot be taken over. A lock releases when the task ends, when the client disconnects for longer than a 10-second grace window, or when the person closes the tab.
- **Acting as the person** — websites see the person's own signed-in session. FSB does not give the agent a separate web identity.
- **Secrets** — passwords, full card numbers and CVVs are resolved inside the extension and injected into the page; they are never returned to the MCP client. The fill domain comes from the tab's URL, not from the agent's request.
- **Audit trail** — each MCP task is recorded as one history session across its tabs, with action logs. Capability calls write audit records without secrets, and replay manifests list each step and its risk.
- **Boundary** — this is local delegation inside one browser profile. It is not a cryptographic or cloud-issued agent identity.

---

## Protocol Surface

| Interface | Detail |
|---|---|
| MCP (stdio) | `npx -y fsb-mcp-server` |
| MCP (Streamable HTTP) | `npx -y fsb-mcp-server@latest serve`; endpoint `http://127.0.0.1:7226/mcp`, health at `http://127.0.0.1:7226/health` |
| Extension bridge | WebSocket on `ws://localhost:7225`; several MCP clients share one hub |
| CLI | `fsb-mcp-server install --<client>`, `doctor`, `--dry-run` |
| Agent Skill | `skills/fsb/SKILL.md` with doctor and config-print scripts |
| Extension UI | Popup and side panel for standalone natural-language runs (secondary surface) |

---

## Human-in-the-Loop Support

The person does the one-time install and unlocks the vault. After that, agents run without per-action confirmation, with a few deliberate gates:

- `use_payment_method` shows the card brand, last 4 digits and merchant domain, and fills only after the person confirms.
- `replay_session` needs one approval of the exact replay manifest in the side panel.
- Network-capture discovery on a sensitive origin asks for extra confirmation, and denylisted origins stay blocked.
- `upload_file` refuses paths on a sensitive-path denylist.

The person can watch the overlay on each tab an agent is driving. FSB does not try to defeat CAPTCHAs or bot checks.

---

## Why Generic Alternatives Do Not Qualify

| Alternative | Why It Fails |
|---|---|
| **Puppeteer / Playwright scripts** | Libraries for people writing automation code. A fresh browser context per script, with no agent identity, tab ownership or secret boundary |
| **Screenshot-based computer-use agents** | Work from pixels and take over the person's cursor and keyboard. FSB reads page structure and acts in background tabs without taking focus |
| **Stock browser with saved passwords** | Autofill is built for a person at the keyboard. An agent driving it has to type the secret itself, which puts it in tool arguments and model context, and nothing keeps two agents out of each other's tabs |

Playwright MCP and hosted browsers such as Browserbase or Steel are valid agent browser models with their own entries. FSB's difference is that several agents share the person's own signed-in browser, kept apart by tab ownership, with secrets held inside the extension.

---

## Use Cases

- **Signed-in dashboards** — pull figures from analytics or admin pages the person is already logged into.
- **Inbox and support triage** — label, draft and route in Gmail or Zendesk under the person's account.
- **QA and regression checks** — click through local or staging flows with structured reads and checked actions.
- **Parallel agents, one browser** — several MCP clients each drive their own tabs in the same profile without colliding.
- **Watching a value** — arm a trigger on a price, stock or queue element and let the agent decide what to do when it fires.
- **Logins without exposing secrets** — fill login and checkout forms from the vault without the secret reaching the model.
