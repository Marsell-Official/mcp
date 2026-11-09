# Marsell MCP

[![M8ven Score](https://m8ven.ai/badge/mcp/marsell-official-mcp-fbd6w1?v=1185f61d4ced8aeb3d79327f52e3d1ad)](https://m8ven.ai/mcp/marsell-official-mcp-fbd6w1?s=readme)

[Marsell](https://getmarsell.com) is an AI for marketing, sales and content creation. Its hosted Model Context Protocol (MCP) server lets the AI you already use work with your Marsell projects.

This is Marsell's official public repository for connection instructions and MCP Registry metadata. The hosted backend is private and is not distributed in this repository.

**Server URL:** `https://getmarsell.com/mcp`  
**Transport:** Streamable HTTP  
**Registry name:** `com.getmarsell/marsell`, verified to getmarsell.com (it replaced `io.github.Marsell-Official/marsell` on 4 November 2026)

[Full documentation](https://docs.getmarsell.com/developers/mcp) · [Try the interactive demo](https://getmarsell.com/app/demo) · [Marsell](https://getmarsell.com) · [Privacy](https://getmarsell.com/privacy) · [Terms](https://getmarsell.com/terms)

## Connect

### Sign in with OAuth

In an MCP client that supports remote servers and OAuth, add `https://getmarsell.com/mcp`, choose OAuth authentication, sign in to Marsell and review the permissions before pressing **Allow**. Do not select **No authentication**. Client plans and administrator permissions can affect connector availability; see the [connection guide](https://docs.getmarsell.com/developers/mcp).

For Claude Code:

```sh
claude mcp add --transport http marsell https://getmarsell.com/mcp
```

Then run `/mcp`, select **marsell**, and choose **Authenticate**.

### Connect with an agent key

In Marsell, open a project, go to **Agents → Your own AI**, and choose **Make a key**. Choose **Reads only**, or **Can act** if your agent should also change things and spend tokens. For missions only, choose **Make a missions key**: it can only take and report missions. Copy the key when it is shown; it is only displayed once.

For Codex, set the environment variable in the shell that starts Codex:

```sh
export MARSELL_KEY='YOUR_MARSELL_AGENT_KEY'
codex mcp add marsell --url https://getmarsell.com/mcp --bearer-token-env-var MARSELL_KEY
```

On PowerShell, set the variable with `$env:MARSELL_KEY = 'YOUR_MARSELL_AGENT_KEY'`, then run the same `codex mcp add` command.

Alternatively, configure Codex in `~/.codex/config.toml`:

```toml
[mcp_servers.marsell]
url = "https://getmarsell.com/mcp"
bearer_token_env_var = "MARSELL_KEY"
```

Other clients can use the server URL with this HTTP header:

```text
Authorization: Bearer YOUR_MARSELL_AGENT_KEY
```

Replace the placeholder with your own key through your client's secure credential settings. Never commit keys to Git or paste them into an issue.

## Plugins and skills

This repository is also a plugin package. It connects the same hosted server and adds a skill, [`skills/marsell/SKILL.md`](./skills/marsell/SKILL.md), that tells your agent how to work with Marsell.

- **Any agent that reads skills:** `npx skills add Marsell-Official/mcp`
- **VS Code:** [Install in VS Code](https://vscode.dev/redirect?url=vscode%3Amcp%2Finstall%3F%257B%2522name%2522%253A%2522marsell%2522%252C%2522type%2522%253A%2522http%2522%252C%2522url%2522%253A%2522https%253A%2F%2Fgetmarsell.com%2Fmcp%2522%257D)
- **Gemini CLI:** `gemini extensions install https://github.com/Marsell-Official/mcp`
- **Claude Code, Cursor and other plugin hosts:** the manifests are in [`.claude-plugin/`](./.claude-plugin/plugin.json), [`.cursor-plugin/`](./.cursor-plugin/plugin.json) and [`.mcp.json`](./.mcp.json).

### Hermes Agent

The root [`plugin.json`](./plugin.json) and [`mcp.json`](./mcp.json) are an Agent Plugins v1 package for Hermes Agent: configuration only.

Disclosure: every tool call goes to Marsell's hosted server at getmarsell.com, and the tools come from that server when Hermes connects, so the pinned commit doesn't fix them. Tools that write can change your Marsell projects. Tools that make pictures, films and voice-overs, and some others such as research and page changes, spend Marsell tokens from your plan; each says so before it runs. `answer_decision` answers a waiting decision in the owner's name, and the option chosen can publish a post, send a message or spend. The mission tools (`missions_next`, `mission_done` and the rest) give the agent messages to send word for word from the owner's own Reddit, X or LinkedIn account, and only where the owner said yes to that in Marsell. `get_upload_link` and `voice_clone` give the agent a `curl` command that uploads files from your computer to Marsell. Every ad spend still needs the owner's own approval in Marsell. Sign-in runs the pinned `mcp-remote@0.15.2` through npx (Node.js and npm required): on the first tool call it opens your browser to Marsell and listens on a local port for the answer, then stores the OAuth tokens under `~/.mcp-auth`. The package has no telemetry and no self-updater. It marks the server untrusted, so Hermes asks before every tool the server doesn't mark read-only and refuses them in unattended runs.

## What your AI can do

- Read projects, brand information, market research, people, analytics, token balances and decisions waiting for you.
- With permission, answer decisions, update brand information, plan work and use Marsell's content, marketing and sales tools.
- Create pictures with a token quote first, and work with Marsell's chat on content, canvases and videos.
- Set up a business using the context and files your AI already has.
- Pick up homework that needs your own AI's logins or files, and report its results to Marsell.
- Send Marsell's messages on Reddit, X and LinkedIn from your own signed-in browser, one mission at a time at Marsell's pace, where you said yes to it in setup or Settings.

The tools available depend on your permissions. Your client can discover the current list through MCP `tools/list`; the [full documentation](https://docs.getmarsell.com/developers/mcp) describes the tools and their approval requirements.

Try asking:

> What needs me on Marsell this week?

> Read my brand and market, then tell me what we should work on next.

> Check my Marsell homework and show me what is due before starting.

Homework does not create a background schedule by itself. If you want recurring checks, your AI client must support scheduling; it can report that schedule to Marsell with `homework_schedule`.

## Permissions and account access

**Reads only** lets your AI inspect information. **Can act** also allows supported changes and actions that can spend tokens. Tool-specific approval requirements still apply, and Marsell checks your current account role on every call. Team and organisation access follows your Marsell permissions.

You can disconnect an OAuth app or revoke a key under **Agents → Your own AI → Connected apps and keys**. Account, plan and token requirements apply to the hosted service.

## Troubleshooting

- **401 Unauthorized:** authenticate with OAuth or supply a valid agent key. The endpoint requires authentication.
- **Opening the URL in a browser fails:** this is a Streamable HTTP MCP endpoint, not a web page. Use an MCP client.
- **A tool is missing or an action is denied:** check the connection's scope, your current role and the tool's requirements.
- **A key stopped working:** check whether it was revoked and whether the client can still read its environment variable.

OAuth discovery is available at [protected resource metadata](https://getmarsell.com/.well-known/oauth-protected-resource/mcp) and [authorization server metadata](https://getmarsell.com/.well-known/oauth-authorization-server).

## Repository and registry maintenance

[`server.json`](./server.json) describes the hosted server for the [official MCP Registry](https://registry.modelcontextprotocol.io). It does not install or expose Marsell's backend.

Connection instructions and registry metadata are included in Marsell's MCP staleness checks. When the hosted interface changes, maintainers review this README and the manifest against the implementation. Registry metadata changes require validation with the official `mcp-publisher` and a new version before publication; changes in this GitHub repository alone do not update the registry.

Please report connection or documentation issues through this repository's issues, without credentials or private project data.

## License

The documentation and metadata in this repository are licensed under MIT. That license does not cover the hosted Marsell service, its private backend, or Marsell's trademarks and brand assets.
