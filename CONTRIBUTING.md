# Contributing

Thanks for considering a contribution.

## What is useful

- **Bug reports.** Tell us the client (Claude Desktop, Cursor, ChatGPT, Codex), what you asked, what the assistant did, and what you expected.
- **Prompt recipes.** Question phrasings that reliably get good results are worth sharing.
- **New read-only tools.** Ideas that fit the existing pattern: one Search Console call, optional calculation, one response.

## Security: never commit secrets

Scenario exports and screenshots leak easily. Before opening a pull request, check that you have not included:

- connection identifiers (`{{#...}}` values in node parameters)
- API keys or bearer tokens
- the MCP Trigger `path` value, which is your live server address
- workspace, folder or owner identifiers
- email addresses
- real property URLs and traffic figures in screenshots

The export in `scenario/` has been scrubbed. Placeholder values look like `YOUR_SERVER_PATH` and `{{#YOUR_GOOGLE_SEARCH_CONSOLE_CONNECTION}}` - leave them as placeholders.

## Design rules for new tools

- **Read-only.** Destructive Search Console operations stay out of the MCP surface.
- **Lock the grouping.** Tools with a calculation behind them must fix `dimensions` in the node rather than letting the assistant choose. A wrong grouping produces plausible nonsense instead of an error.
- **Cap the API calls.** Anything that loops needs a hard ceiling, both for Google's quota and for execution cost.
- **Keep the explanatory notes.** The `note` field in tool responses stops assistants from misreading results. Do not strip it.
- **Write the tool description for a model, not a human.** State what it does, what it needs, what comes back, and when to use it instead of a neighbouring tool.
