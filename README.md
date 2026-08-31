# Product Fruits agent plugin

Cross-compatible plugin for Codex and Claude Code. It bundles:

- the remote Product Fruits MCP server;
- OAuth authentication handled by the MCP client;
- one example skill that summarizes a workspace and suggests follow-up analysis.

## Structure

```text
plugin/
├── .codex-plugin/plugin.json       # Codex manifest
├── .claude-plugin/plugin.json      # Claude Code manifest
├── .mcp.json                       # Shared MCP connection
└── skills/
    └── workspace-overview/
        └── SKILL.md                # Shared agent skill
```

The MCP endpoint is `https://my.productfruits.com/mcp`. It uses Streamable HTTP and OAuth; users sign in with Product Fruits rather than copying an API key. The current MCP is read-only.

## Try it in Claude Code

From the repository root:

```powershell
claude --plugin-dir ./plugin
```

In Claude Code, open `/mcp`, authenticate Product Fruits when prompted, then run:

```text
/product-fruits:workspace-overview
```

For a quick MCP-only smoke test, you can also register the server directly:

```powershell
claude mcp add --transport http product-fruits https://my.productfruits.com/mcp
```

## Try it in Codex

For the first iteration, add `https://my.productfruits.com/mcp` in **Settings > Plugins > MCP servers** as a Streamable HTTP server, authenticate, and start a new task. Then point Codex at the example skill in `plugin/skills/workspace-overview/SKILL.md` while developing it.

The next packaging step is to add this plugin to a local Codex marketplace, install it, and test the bundled MCP and skill together in a new conversation.

## Example prompts

- “Give me an overview of my Product Fruits workspace.”
- “Which onboarding flow should I investigate first?”
- “Summarize recent adoption signals and tell me what data is missing.”

## Validate

Validate the shared skill and each host-specific plugin manifest before release. Claude Code provides:

```powershell
claude plugin validate ./plugin
```

Codex plugin validation is available through its built-in plugin creator workflow.

## Suggested next increment

Keep the first release narrow. After the overview workflow works reliably, add separate skills for flow drop-off analysis, survey synthesis, knowledge-base gap analysis, and Elvin conversation summaries. Reuse the same `.mcp.json`; each new skill should describe one clear outcome.
