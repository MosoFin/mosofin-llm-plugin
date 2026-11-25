# MosoFin for Claude: financial review workflows on your live books

**Ask about your books, review them like a controller, and hand over something you can sign.** This plugin connects your AI assistant to QuickBooks Online and other connected accounting platforms through [MosoFin](https://mosofin.com). It adds 42 review workflows built for bookkeepers, controllers, fractional CFOs, business owners and accounting firms: month-end close, P&L flux, balance sheet review, collections, payment runs, cash runway, owner packs and sweeps across client workspaces.

The MosoFin connector is included: install the plugin, sign in once, confirm a workspace, and start asking. Everything is **read-only** and scoped to the workspace you confirm. A MosoFin account is required (sign up at [mosofin.com](https://mosofin.com)).

## Install

### Claude Code

```text
/plugin marketplace add mosofin/mosofin-mcp-plugin
/plugin install mosofin@financehub
```

Then run `/mosofin:setup`. It signs you in to MosoFin with OAuth, confirms your workspace, shows which company files are connected, runs a first report, and offers to save your review profile.

### claude.ai, Claude Desktop and Cowork

Install **MosoFin** from the Claude plugin directory, or add it as a custom connector at `https://mcp.mosofin.com/mcp` (**Settings → Connectors**), then **Connect** and complete the MosoFin sign-in in the browser.

### Grok Build

The plugin uses the same manifest and skill format Grok Build reads. Once the `mosofin` entry is listed in the [xAI plugin marketplace](https://github.com/xai-org/plugin-marketplace), install it from Grok Build's plugin catalog and sign in when prompted. Listing status is in [`docs/xai-marketplace-submission.md`](docs/xai-marketplace-submission.md).

### ChatGPT and Codex

1. Open ChatGPT on the web → **Settings → Apps & Connectors** (or [chatgpt.com/plugins](https://chatgpt.com/plugins)).
2. Turn on **Developer mode** if you are adding a custom connector (**Settings → Security and login**, or Apps → Advanced settings).
3. Create **MosoFin**:
   - Name: `MosoFin`
   - MCP server URL: `https://mcp.mosofin.com/mcp`
   - Authentication: **OAuth**
4. Complete MosoFin sign-in in the browser.
5. Start a **new** chat, turn on the MosoFin app/connector, then continue below.

If you see `Unknown tool: mosofin.list_workspaces`, MosoFin is not enabled for this chat. Connect it as above (or turn the app on in this conversation). That is not a data-source reconnect.

### Any other MCP client

Add a remote MCP server at `https://mcp.mosofin.com/mcp` (Streamable HTTP, OAuth). The client registers itself; there is nothing to configure.

## Try asking

- "Compare last month's P&L to the month before and the same month last year. What changed and why?"
- "Is the books ready to close for October? What's left?"
- "Does the trial balance tie, and is anything sitting in uncategorized or suspense?"
- "Review the balance sheet as of 31 October and flag anything odd."
- "Which customers should we chase this week? Draft the reminders but don't send them."
- "What bills are due in the next two weeks, and what should we pay first with the cash we have?"
- "Which vendors did we spend more with this quarter, and which subscriptions are new?"
- "Any unusual expenses last month?"
- "How many months of runway do we have at the current burn?"
- "We're profitable but cash is tight. Why?"
- "How are we doing against the budget, year to date?"
- "Build the October owner pack for Acme."
- "Which of my clients need attention this month?"
- "Compare gross margin across these three companies — not a consolidation, just side by side."
- "Is this client ready for year-end and the tax preparer?"

## Slash commands

| Command | What it does |
|---|---|
| `/mosofin:setup` | Sign in, confirm the workspace, check connected company files, run a first report |
| `/mosofin:set-profile` | Save basis, fiscal year, materiality, KPI targets and known context; every review then uses them |
| `/mosofin:daily-cash-check` | Morning check: cash, what came in and went out, A/R past due, bills due this week. Can be scheduled |
| `/mosofin:weekly-briefing` | Weekly finance briefing: the one priority, cash, receivables, payables, what can wait |
| `/mosofin:month-end-close` | Close readiness, trial balance tie-out, balance sheet review, P&L flux and a sign-off summary |
| `/mosofin:books-health` | Can you trust these books? TB tie, suspense, undeposited funds, aging vs control, duplicates |
| `/mosofin:pnl-review` | P&L flux analysis with drivers and open questions |
| `/mosofin:balance-sheet-review` | Line-by-line balance sheet review at period end |
| `/mosofin:unusual-expenses` | Expenses unusual by amount, frequency, vendor or account, logged neutrally |
| `/mosofin:budget-vs-actual` | Actuals against the budget saved in your books, with commentary |
| `/mosofin:ar-aging` | A/R aging, concentration and exceptions, tied to the control account |
| `/mosofin:collections` | Prioritised collections list with review-only reminder drafts |
| `/mosofin:ap-aging` | A/P aging and a proposed payment run |
| `/mosofin:vendor-spend` | Top vendors, changes, concentration, recurring and subscription spend |
| `/mosofin:cash-runway` | Burn, months of runway and the profit vs cash bridge |
| `/mosofin:owner-pack` | Monthly owner pack: headline, P&L, cash, A/R, A/P, KPIs and decisions needed |
| `/mosofin:client-sweep` | Health sweep across client workspaces, flagged clients first |

**Managing several clients?** Ask "which of my clients need attention?" The client portfolio analyst sweeps every workspace you confirm and returns one table with the clients that need a look listed first, without mixing one client's facts into another's.

**It remembers your settings.** Basis, materiality, targets and known context, such as "legal fees are high this quarter because of a one-off settlement", are saved as a private skill in your MosoFin workspace with your OK, and used by every later review in any AI host connected to MosoFin.

You don't need to remember any of these. Describe what you want and the right workflow is picked for you.

## Workflows by role

| Skill | For | Workflows |
|---|---|---|
| `bookkeeper` | Bookkeepers and close preparers | Close readiness · Uncategorized and suspense cleanup · Duplicate transactions · Undeposited funds and unapplied payments · Journal entry review · Customer and vendor list hygiene · Deposits and purchases sanity check |
| `controller` | Controllers, accountants, close reviewers | P&L flux · Balance sheet review · Trial balance tie-out · Cutoff and accruals · Unusual expenses · Gross margin by customer and item · Budget vs actual |
| `receivables` | A/R and collections | A/R aging and control tie · Collections priority · DSO trend · Customer concentration and credit risk · Unapplied payments and credits · Invoice-to-cash cycle · Reminder drafts |
| `payables` | A/P and spend owners | A/P aging and payment run proposal · Vendor spend · Duplicate bills · Spend trend by category · Vendor concentration · Recurring and subscription spend · Upcoming cash commitments |
| `fractional-cfo` | CFOs, finance leads, owners | Monthly owner pack · Cash runway and burn · Profit vs cash bridge · KPI scorecard · Revenue concentration · 13-week cash scenario · Quarterly board summary |
| `accounting-firm` | Accounting firms and CAS teams | Portfolio health sweep · Multi-client month-end status · Advisory meeting prep · Cleanup scoping · Selected-company comparison · Client KPI benchmarking · Year-end readiness |

Each role skill has the same layout: `SKILL.md` (when to trigger, principles, chart framework), `references/workflows.md` (step-by-step sequences), `references/calculations.md` (formulas and thresholds) and `assets/` (deliverable templates: memos, checklists, owner emails, board outlines).

Each workflow pulls the data first, confirms what it got, then analyzes it. Results come back as tables and charts coloured by whether a change is good or bad for the business, ending with a **Data sources** line naming the report and fetch time. You can also get a Word doc, spreadsheet or deck when you need something to share.

## Core skills

| Skill | What it does |
|---|---|
| `get-started` | First-run sign-in, workspace confirmation, connected companies and a first report |
| `review-profile` | Saves basis, fiscal year, materiality, targets and known context, and applies them to every review |
| `quickbooks` | QuickBooks Online know-how: which operation answers which question, date rules, pagination and quirks |
| `connect` | Sign in to MosoFin from the conversation, then continue the original request |
| `workspaces` | List, confirm or switch the workspace for this chat |
| `connections` | See which company files are connected, and reconnect links |
| `query-workspace` | Any open data question, end to end |
| `list-tools` / `run-tool` | See the available operations for a company file, or run one by name |
| `list-skills` / `replay-skill` / `save-skill` | List, replay or save your own MosoFin skills |

Two agents work alongside them: `mosofin-analyst` (read-only data fetches delegated from the main conversation) and `client-portfolio-analyst` (the multi-client sweep).

## Supported platforms

**Live:** QuickBooks Online.
**In the catalog:** other platforms such as Xero, Stripe and Shopify may appear in your workspace's catalog; until a platform is wired live its results are marked `mock: true`, and the plugin says the numbers are demo data.

Connect company files in the MosoFin app at [app.mosofin.com](https://app.mosofin.com).

## Signing in

MosoFin sign-in is standard OAuth handled by your AI host; the plugin takes no part in it and ships no local helper. Ask your question first — the `/mosofin:connect` skill notices when you're signed out, keeps your question, gives the single sign-in step for the host you're in, and continues once you say *done*.

| Host | Where sign-in happens |
|---|---|
| Claude Code | `/mcp` → `plugin:mosofin:mosofin` → **Authenticate**. Without a browser, use Claude Code's no-browser MCP sign-in for the same server. |
| Claude Desktop / claude.ai / Cowork | **Settings → Connectors** → MosoFin → **Connect** |
| ChatGPT / Codex | **Settings → Apps & Connectors** (steps above), then a new chat with MosoFin on |
| Grok Build | The sign-in prompt shown after installing `mosofin` |

Signing in to MosoFin is separate from a company file (e.g. QuickBooks) showing as disconnected; that is fixed with the `reconnect_url` MosoFin returns, or on the workspace data-sources page (`/mosofin:connections`).

## Update

Plugin hosts do not pick up GitHub changes automatically. After this repo updates, refresh the marketplace and the plugin (Claude Code shown):

```text
/plugin marketplace update financehub
/plugin update mosofin@financehub
```

Codex and ChatGPT talk to the live MosoFin MCP server, so server tools update without this step. Skill text in this repo updates only after you refresh the plugin as above.

## Privacy and safety

- **Read-only.** Reviews, checks and packs never create, change, post, pay, send, reconcile or delete anything in your accounting platform. Payment runs, adjusting entries and reminder emails are proposals and unsent drafts for a person to act on.
- **Workspace-scoped.** Every session starts by confirming the workspace by name, and companies by name. Facts from one client never appear in another client's review.
- **Grounded.** Figures come only from reports fetched in the conversation, each answer ends with the report and fetch time, and fixture data is labelled. Causes are marked observed, possible or unknown; nothing is called an error or fraud without evidence.
- **One additive write, only with your OK.** Saving a skill or your review profile writes a private skill to your own MosoFin workspace after you approve the exact text.
- **People sign off.** A read-only review is not a close, a reconciliation, an audit or a consolidation. The responsible person checks the support and owns every decision.

See the [privacy policy](https://mosofin.com/privacy) and [terms](https://mosofin.com/terms).

## Security and network access

Declared for directory and marketplace review:

| Item | Detail |
|---|---|
| Network endpoints | `https://mcp.mosofin.com/mcp` only, hardcoded in `.mcp.json`. No user-configurable URL. Nothing is downloaded or installed. |
| Sign-in | OAuth in the browser, run entirely by your AI host (Claude Code, Claude Connectors, ChatGPT, Grok Build). The plugin takes no part in sign-in, stores nothing, and never asks you to type or paste anything from the sign-in flow into the chat. It reads nothing from your machine. |
| Data access | Read-only, scoped to the workspace(s) you confirm in the chat. No write, post, pay, send or reconcile operations. `create_skill` saves a workflow or your review profile to your own MosoFin workspace only after you confirm. |
| Executable code | None. The plugin is one remote MCP server declaration plus Markdown: skills, slash commands, agents and templates. No hooks, scripts, binaries or local servers. Calculations are documented in Markdown and done in the conversation. |
| Account | A MosoFin account is required to sign in; the plugin is free. |
| Telemetry | None from the plugin itself. |

## Public workflow resources

- [Financial review prompt library](docs/financial-review-prompt-library.md) — starting prompts for P&L review, cash questions, A/R exceptions, multi-client work, and selected-company analysis.
- [Implementation notes](docs/implementation-notes.md) — the workspace, permission, source-grounding and human-review decisions behind the plugin.
- [Multi-client month-end review checklist](https://www.mosofin.com/multi-client-quickbooks-month-end-review-checklist) — public web checklist plus printable PDF, editable workbook and prompt pack.

These resources are examples, not accounting conclusions. Confirm the workspace, company, period, basis and available source coverage before a run.

## Support

- Plugin issues: [open an issue](https://github.com/MosoFin/mosofin-mcp-plugin/issues)
- Account, connection or data questions: support@mosofin.com

## For contributors

Test locally without installing:

```text
claude --plugin-dir .
```

```text
.mcp.json   the MosoFin remote MCP server (Streamable HTTP, OAuth)
skills/     role skills (SKILL.md + references/ + assets/), core and tool skills
commands/   slash commands
agents/     mosofin-analyst and client-portfolio-analyst
docs/       tool contract and public workflow resources
```

Run `claude plugin validate --strict .` before opening a PR. Bump `version` in both `plugin.json` files and both marketplace entries together.

Issues and pull requests are welcome for documentation fixes, new read-only review workflows and improvements to existing ones. Keep examples synthetic, state required source coverage, separate facts from assumptions, and do not add instructions that post, send, pay, reconcile or otherwise change source records. For a new workflow, include its audience, required inputs, expected output, review checkpoints and failure behaviour.

## License

MIT. See [LICENSE](LICENSE).
