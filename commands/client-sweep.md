---
description: Health sweep across your client workspaces — one table, with the clients that need attention first
argument-hint: "[clients, optional]"
---

Sweep client health for: $ARGUMENTS (default: every workspace the user can access).

1. Call `list_workspaces`, show the workspaces by name, and confirm the ones to include with `list_workspaces(workspace_ids=[…], mode="multi")`. Never auto-select.
2. For each confirmed workspace, call `get_agent_datasources` and note companies by `display_name`; where a workspace has several live companies, ask once which to include (or all).
3. Delegate to the `client-portfolio-analyst` agent with the confirmed workspace and company handles.
4. Relay its table and flags. Never combine one client's facts with another client's conclusion.
