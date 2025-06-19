# CLAUDE.md

This file provides guidance to Claude Code when working with code in this repository.

## Development Tools

- Runtime: Bun 1.2.11

## Common Development Tasks

### Available npm/bun scripts from package.json:

```bash
# Test
bun test

# Formatting
bun run format          # Format code with prettier
bun run format:check    # Check code formatting

# Type checking
bun run typecheck       # Run TypeScript type checking
```

## Current Development - PR Creation Feature

We're adding a new `create_pull_request` tool to the GitHub MCP server that allows Claude to open pull requests. This feature:

1. **Implementation**: Added to `src/mcp/github-file-ops-server.ts`
2. **Tool Schema**: Accepts title, body, base branch, head branch (optional), and draft status
3. **Tests**: Unit tests added to `test/install-mcp-server.test.ts`
4. **Testing Strategy**: Fork this repo and test on external repositories using `uses: your-username/claude-code-action@branch`

### Testing the PR Creation Feature

To test this feature:
1. Fork this repository
2. Push changes to your fork
3. In your test repo's workflow, use: `uses: stets/claude-code-action@main`
4. Create an issue and ask Claude to create files and open a PR
5. Ensure the workflow includes: `allowed_tools: "mcp__github_file_ops__create_pull_request"`

## Architecture Overview

This is a GitHub Action that enables Claude to interact with GitHub PRs and issues. The action:

1. **Trigger Detection**: Uses `check-trigger.ts` to determine if Claude should respond based on comment/issue content
2. **Context Gathering**: Fetches GitHub data (PRs, issues, comments) via `github-data-fetcher.ts` and formats it using `github-data-formatter.ts`
3. **AI Integration**: Supports multiple Claude providers (Anthropic API, AWS Bedrock, Google Vertex AI)
4. **Prompt Creation**: Generates context-rich prompts using `create-prompt.ts`
5. **MCP Server Integration**: Installs and configures GitHub MCP server for extended functionality

### Key Components

- **Trigger System**: Responds to `/claude` comments or issue assignments
- **Authentication**: OIDC-based token exchange for secure GitHub interactions
- **Cloud Integration**: Supports direct Anthropic API, AWS Bedrock, and Google Vertex AI
- **GitHub Operations**: Creates branches, posts comments, and manages PRs/issues

### Project Structure

```
src/
├── check-trigger.ts        # Determines if Claude should respond
├── create-prompt.ts        # Generates contextual prompts
├── github-data-fetcher.ts  # Retrieves GitHub data
├── github-data-formatter.ts # Formats GitHub data for prompts
├── install-mcp-server.ts  # Sets up GitHub MCP server
├── update-comment-with-link.ts # Updates comments with job links
└── types/
    └── github.ts          # TypeScript types for GitHub data
```

## Important Notes

- Actions are triggered by `@claude` comments or issue assignment unless a different trigger_phrase is specified
- The action creates branches for issues and pushes to PR branches directly
- All actions create OIDC tokens for secure authentication
- Progress is tracked through dynamic comment updates with checkboxes
