# Contributing to RuleMetric documentation

We welcome corrections, clearer explanations, and examples that help someone use RuleMetric successfully.

## Make a change

1. Fork the repository and create a branch.
2. Edit the relevant Markdown file under `docs/`. Use relative links between repository documents.
3. Check that commands and flags match the relevant CLI version. Include prerequisites and expected results for examples.
4. Preview the Markdown, verify links, and open a pull request explaining the problem and the change.

The pages render directly on GitHub; no application build or credentials are needed to edit them.

For a documentation bug, include the page, the step that failed, the CLI version, and the expected behavior. For product issues, include a small reproducible example where possible.

Use synthetic examples. Remove API keys, passwords, private transcripts, customer data, and private repository details from proposed changes and diagnostic output.

## Reference pages

The initial content came from these public sources:

- `https://rulemetric.com/agent-docs/quickstart.md`
- `https://rulemetric.com/agent-docs/integrate.md`
- `https://rulemetric.com/agent-docs/suggestions.md`
- `https://rulemetric.com/agent-docs/cli.md`
- `https://rulemetric.com/agent-docs/api.md`
- `https://rulemetric.com/agent-docs/mcp-tools.md`

The CLI and MCP references contain generated sections. When reporting a problem in a generated reference, include the command or tool and the version observed so maintainers can update the source as well as the public page. Changes here require a separate update to the hosted documentation.

Contributions to the documentation are made under the repository's MIT License.
