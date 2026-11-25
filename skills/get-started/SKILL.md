---
name: get-started
description: First-run onboarding for MosoFin. Use when the user has just installed the plugin, asks how to get started, wants to know what MosoFin can do or what data they can access, wants to connect QuickBooks or another accounting platform, or runs /mosofin:setup.
---

# Get started with MosoFin

Take the user from install to their first real answer in as few turns as possible. Do each step only if the previous one succeeded.

## 1. Check sign-in

Look for MosoFin's `list_workspaces` tool (any MosoFin source: `mcp__plugin_mosofin_mosofin__list_workspaces` in Claude Code, `mcp__MosoFin__list_workspaces` for a Claude connector, `mosofin.list_workspaces` in ChatGPT) and call it with no arguments.

- **Tool missing, `Unknown tool`, or sign-in required:** follow the `connect` skill. It gives the single sign-in step for this host and continues here when the user says *done*. Never ask for a password or anything from the sign-in flow in chat.
- **No workspaces returned:** the account has no workspace yet. Tell the user to create one and connect their accounting platform at [app.mosofin.com](https://app.mosofin.com), then come back. A MosoFin account is required (sign up at [mosofin.com](https://mosofin.com)).
- **Success:** go on.

## 2. Confirm the workspace

Follow the `workspaces` skill: show workspaces by name and role, ask which one (single or multi), and confirm with `list_workspaces(workspace_ids=[…], mode=…)`. With one workspace it auto-confirms; still read the name back and wait for a yes. Never show workspace handles.

## 3. See what's connected

Call `get_agent_datasources` for the confirmed workspace (see the `connections` skill). Summarise in one short list: platform, company `display_name`, connected yes/no. Don't dump every field.

- **Nothing connected:** connecting a company file happens in the MosoFin app, not in chat. Point them to the workspace data-sources page at [app.mosofin.com](https://app.mosofin.com), and wait.
- **A company shows `connected: false`:** that's a company-file reconnect, separate from MosoFin sign-in. Give the `reconnect_url` when a call returns one, or the data-sources page.
- **Several companies:** ask which one to start with, by `display_name`.

## 4. First answer

Once a company is connected, run one small, useful read straight away: last complete month vs the month before — revenue, gross profit, total expenses and net income from `get_profit_and_loss`, plus cash and A/R from `get_balance_sheet` as of month end. Show it as a compact table, say whether the numbers are live (`mock: false`) or fixture, and end with the **Data sources** line.

## 5. Save their review profile

Ask, briefly, for the few settings that change every later analysis: accounting basis, fiscal year start, materiality, and any known context. Use the `review-profile` skill. Skip this if a profile already exists or the user declines.

## 6. Point to what's next

Suggest three questions tailored to what's connected and who they are (bookkeeper, controller, CFO or owner, accounting firm). Mention:

- `/mosofin:month-end-close` for a full close review
- `/mosofin:daily-cash-check` (can be scheduled)
- `/mosofin:owner-pack` for a monthly owner summary
- `/mosofin:client-sweep` if they manage several client workspaces
- `/mosofin:save-skill` to keep any workflow that worked

Remind them once that MosoFin is read-only: it never posts, pays, sends or reconciles anything in their books.
