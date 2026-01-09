# Atlassian MCP Server Setup

Quick reference for enabling Confluence/Jira integration in Claude Code.

## First-Time Authentication

Run once to authenticate with Atlassian (opens browser for OAuth):

```bash
npx -y mcp-remote https://mcp.atlassian.com/v1/sse
```

## Install

```bash
# User scope (recommended - works reliably)
claude mcp add atlassian --scope user -- npx -y mcp-remote https://mcp.atlassian.com/v1/sse

# Project scope (add to .mcp.json - may require approval on restart)
claude mcp add atlassian --scope project -- npx -y mcp-remote https://mcp.atlassian.com/v1/sse
```

Restart Claude Code after adding.

## Remove

```bash
# Remove from user scope
claude mcp remove atlassian -s user

# Remove from project scope
claude mcp remove atlassian -s project
```

## Verify

```bash
claude mcp list
claude mcp get atlassian
```

## Available Tools

Once connected, you'll have access to Confluence tools:
- Search pages
- Read/summarize pages
- Create new pages
- Update existing pages
- Navigate spaces
