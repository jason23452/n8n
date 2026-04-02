# MCP server (OpenCode) - Local setup

This folder contains configuration and environment examples for running a local OpenCode MCP server alongside this repo's `n8n` service.

Files:
- .env.example  — example env file. Copy to `.opencode/mcp/.env` and fill values.
- config.yaml   — optional runtime config for the MCP server (example)

Quick start (local development)

1. Copy the example env file:

   cp .opencode/mcp/.env.example .opencode/mcp/.env

2. Edit `.opencode/mcp/.env` and set `MCP_API_TOKEN` to a secure random value.

3. Start services:

   docker compose up -d mcp n8n

4. View logs:

   docker compose logs -f mcp

Security notes

- Do NOT commit `.opencode/mcp/.env` to git (add to .gitignore)
- For production, enable TLS and use an external stable storage backend
