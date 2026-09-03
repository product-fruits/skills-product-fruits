# Product Fruits agent plugin

Cross-compatible plugin for Codex and Claude Code. It bundles:

- the remote Product Fruits MCP server;
- OAuth authentication handled by the MCP client;
- one example skill that summarizes a workspace and suggests follow-up analysis.

## Structure

```text
.agents/plugins/marketplace.json    # Codex and ChatGPT marketplace
.claude-plugin/marketplace.json     # Claude marketplace
plugin/
|-- .codex-plugin/plugin.json       # Codex manifest
|-- .claude-plugin/plugin.json      # Claude Code manifest
|-- .mcp.json                       # Shared MCP connection
`-- skills/
    `-- workspace-overview/
        `-- SKILL.md                # Shared agent skill
```

The MCP endpoint is `https://my.productfruits.com/mcp`. It uses Streamable HTTP and OAuth; users sign in with Product Fruits rather than copying an API key. The current MCP is read-only.

## Install in Cowork or Claude Code

In Cowork or the Claude desktop app:

1. Open **Customize > Plugins**.
2. Under **Personal plugins**, select **+ > Add marketplace**.
3. Choose **Add from a repository** and enter `product-fruits/skills-product-fruits`.
4. Install **Product Fruits**.

In Claude Code, run:

```text
/plugin marketplace add product-fruits/skills-product-fruits
/plugin install product-fruits@product-fruits
```

Open `/mcp` and authenticate with Product Fruits when prompted. You can then run:

```text
/product-fruits:workspace-overview
```

Third-party Claude marketplaces do not enable automatic updates by default. Enable auto-update for the marketplace in the plugin manager, or refresh it manually:

```text
/plugin marketplace update product-fruits
```

### Managed Claude rollout

Enterprise administrators can deploy and keep the plugin current with `managed-settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "product-fruits": {
      "source": {
        "source": "github",
        "repo": "product-fruits/skills-product-fruits"
      },
      "autoUpdate": true
    }
  },
  "enabledPlugins": {
    "product-fruits@product-fruits": true
  }
}
```

Managed settings locations are `/Library/Application Support/ClaudeCode/managed-settings.json` on macOS, `/etc/claude-code/managed-settings.json` on Linux and WSL, and `C:\Program Files\ClaudeCode\managed-settings.json` on Windows.

## Install in Codex or ChatGPT

A workspace administrator imports the GitHub marketplace once for the workspace:

1. Open **Admin > Plugins** and select **Add > Import marketplace**.
2. Enter `https://github.com/product-fruits/skills-product-fruits` as the source.
3. Leave **Path** empty so the root marketplace is used, and leave the revision empty to follow the repository's default branch.
4. Import the marketplace, review the result, and configure the Product Fruits plugin's installation policy and access.

Imported GitHub marketplaces sync daily. Administrators can use **Sync now** from **Admin > Plugins > Marketplaces** after a release. Importing the plugin does not authenticate members automatically; each member still signs in to Product Fruits when prompted.

For local development or CLI testing, add the repository marketplace and install the plugin with:

```powershell
codex plugin marketplace add product-fruits/skills-product-fruits
codex plugin add product-fruits@product-fruits
```

Start a new conversation after installing so the bundled skill and MCP server are loaded.

## Local development

From the repository root:

```powershell
claude --plugin-dir ./plugin
```

For a quick MCP-only smoke test, you can also register the server directly:

```powershell
claude mcp add --transport http product-fruits https://my.productfruits.com/mcp
```

## Example prompts

- “Give me an overview of my Product Fruits workspace.”
- “Which onboarding flow should I investigate first?”
- “Summarize recent adoption signals and tell me what data is missing.”

## Validate

Validate the catalogs, shared skill, and each host-specific plugin manifest before release. Claude Code provides:

```powershell
claude plugin validate ./plugin
```

Codex plugin validation is available through its built-in plugin creator workflow.

Keep the `version` in both host manifests identical and bump both on every release. The Claude marketplace intentionally does not duplicate the version: Claude resolves it from `plugin/.claude-plugin/plugin.json`. Codex resolves it from `plugin/.codex-plugin/plugin.json`.

## Suggested next increment

Keep the first release narrow. After the overview workflow works reliably, add separate skills for flow drop-off analysis, survey synthesis, knowledge-base gap analysis, and Elvin conversation summaries. Reuse the same `.mcp.json`; each new skill should describe one clear outcome.
