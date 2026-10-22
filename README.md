# GraphCore plugins

Plugins that connect coding agents to GraphCore apps. Each plugin ships for
both Claude Code and Codex from the same directory.

| Plugin | What it does |
|---|---|
| `toast` | Connects your agent to the Toast wedding app at `toast.graphcore.dev` |

## Install

This repo is private: you need read access to `graphcore-org/graphcore-plugins`
and git credentials that can clone it (SSH key, `gh auth login`, or a
credential helper).

### Claude Code

```
/plugin marketplace add graphcore-org/graphcore-plugins
/plugin install toast@graphcore
```

Then run `/mcp`, pick the plugin's `toast` server and sign in. A browser
opens the GraphCore sign-in page.

Background auto-updates of a private marketplace need a token in your
environment: `GITHUB_TOKEN` or `GH_TOKEN`.

### Codex

```
codex plugin marketplace add graphcore-org/graphcore-plugins
```

Then install `toast` from the GraphCore marketplace and sign
in when prompted.

## Layout

```
.claude-plugin/marketplace.json     Claude Code marketplace
.agents/plugins/marketplace.json    Codex marketplace
plugins/<name>/
  .claude-plugin/plugin.json        Claude Code manifest
  .mcp.json                         Claude Code MCP servers
  plugin.json                       Codex manifest (Agent Plugins standard)
  mcp.json                          Codex MCP servers
  skills/<skill>/SKILL.md           skills, shared by both clients
```

Each client reads only its own files, so both sets live side by side in the
plugin directory. Skills use the same `SKILL.md` format in both clients and
are written once.

## Add a plugin

1. Create `plugins/<name>/` with the four config files and `skills/`.
2. Add an entry to both marketplace files.
3. Validate the Claude Code side:

   ```bash
   claude plugin validate ./plugins/<name>
   claude plugin validate .
   ```

4. Test locally before pushing:

   ```bash
   claude --plugin-dir ./plugins/<name>
   ```

Bump `version` in both manifests on every release; installed copies only
update when it changes.

## Sign-in

GraphCore servers use OAuth against the deployment's Keycloak realm. Claude
Code signs in with the realm's public `graphcore-mcp` client, whose only
redirect URIs are `http://localhost:8765/callback` and
`http://127.0.0.1:8765/callback`, so `.mcp.json` sets `callbackPort: 8765`.
Codex registers its own client through the realm's dynamic client
registration, which accepts loopback redirect URIs.
