# AGENTS.md

This repository is a minimal Docker Compose deployment for `n8n`.

Use this file as the operating guide for agentic edits in this workspace.

## Scope

- Current tracked project file: `docker-compose.yaml`
- No `package.json`, lockfile, source tree, or test suite was present in the repo root at inspection time
- No repo-local Cursor rules were found in `.cursor/rules/` or `.cursorrules`
- No repo-local Copilot instructions were found in `.github/copilot-instructions.md`

## What This Repo Does

- Runs the official `docker.n8n.io/n8nio/n8n` image
- Exposes the service on port `5678`
- Persists data in the named Docker volume `n8n_data`
- Sets timezone and execution-related environment variables for the container

## Primary Commands

Use Docker Compose v2 syntax.

- Validate compose file: `docker compose config`
- Start the stack: `docker compose up -d`
- Start only the `n8n` service: `docker compose up -d n8n`
- View service status: `docker compose ps`
- Follow logs: `docker compose logs -f n8n`
- Restart the service: `docker compose restart n8n`
- Stop the stack: `docker compose down`
- Remove volumes too: `docker compose down -v`

Added MCP server helper commands (this repo now includes a local MCP service):

- Start only the MCP service: `docker compose up -d mcp`
- Start MCP and n8n together: `docker compose up -d mcp n8n`
- Follow MCP logs: `docker compose logs -f mcp`

## Single-Test / Single-Check Workflow

There is no automated test suite in this repo.

When you need a fast, focused validation for a change:

- For compose syntax and interpolation: `docker compose config`
- For runtime verification after a change: `docker compose up -d n8n` then `docker compose logs -f n8n`
- For config-only review, inspect the generated output of `docker compose config`

If a future application layer is added, prefer adding explicit per-file or per-module test commands to this section.

## Editing Principles

- Make the smallest correct change
- Preserve the current deployment model unless the user asks for a broader refactor
- Do not add new services, volumes, or env vars unless they are required by the request
- Keep container-level changes reversible and easy to reason about

## YAML Style

- Use 2-space indentation only
- Never use tabs
- Keep keys aligned and grouped logically
- Prefer list-style `environment` entries when matching existing file style
- Quote port mappings such as `"5678:5678"`
- Keep comments short and purposeful
- Preserve section dividers only if they still add clarity
- Do not reformat unrelated lines while editing

## Naming Conventions

- Service names: lowercase, short, descriptive
- Volume names: lowercase with underscores when consistent with existing usage
- Environment variables: uppercase with underscores
- Comments: concise, descriptive, and action-oriented

## Configuration Conventions

- Keep timezone-related settings together
- Keep execution/security-related flags together
- Group optional or dangerous settings behind commented lines when possible
- Prefer explicit values over implicit defaults when they affect runtime behavior
- If you change a container flag, note the operational impact in a comment when helpful

## Error Handling / Safety

- Fail fast when validating compose changes
- Do not silence container errors without a reason
- Avoid broad permission relaxations unless explicitly requested
- Be careful with options that enable arbitrary code execution or external packages
- Treat changes to filesystem access, runners, and external package loading as high-risk

## Agent Workflow

- Inspect the current file before editing
- Prefer `apply_patch` for all manual file changes
- Do not overwrite user changes outside the task scope
- Verify the result after editing with `docker compose config`
- If you need to confirm runtime behavior, use logs instead of guessing

## When You Need More Context

- Search for additional repo files before introducing conventions from memory
- If new code files appear later, derive style from the nearest existing examples
- If a future `AGENTS.md` is added higher in the tree, follow the nearest applicable one first

## Practical Review Checklist

- Does `docker compose config` still succeed?
- Are new env vars necessary and documented?
- Did any change affect data persistence?
- Did any change increase runtime permissions?
- Are comments still accurate after the edit?

## Current Repo Notes

- The current compose file runs `n8n` as `root`
- The file enables `N8N_RUNNERS_ENABLED=true`
- The file disables filesystem write blocking with `N8N_BLOCK_FS_WRITE_ACCESS=false`
- The file enables built-in and external function access with wildcard values
- The file enables unverified packages
- These settings are operationally sensitive and should only be changed with clear intent

## If You Add Application Code Later

- Add package-manager commands next to the compose commands
- Document a real test runner and a single-test invocation
- Add import, formatting, type, and lint rules from the actual toolchain
- Add any Cursor or Copilot instructions discovered in the repository


# mcp server
 # {
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["@tarquinen/opencode-dcp@latest"],
   "mcp": {
    "notion": {
      "type": "local",
      "command": [
        "npx",
        "-y",
        "@notionhq/notion-mcp-server"
      ],
      "enabled": true,
      "environment": {
        "NOTION_TOKEN": "ntn_"
      }
    },
    "n8n-mcp": {
      "type": "local",
      "command": [
        "npx",
        "-y",
        "n8n-mcp@latest"
      ],
      "environment": {
        "MCP_MODE": "stdio",
        "N8N_API_URL": "http://localhost:5678",
        "N8N_API_KEY": "",
        "WEBHOOK_SECURITY_MODE": "moderate",
        "LOG_LEVEL": "error",
        "DISABLE_CONSOLE_OUTPUT": "true"
      }
    }
  }
}

## agent set
"agent": {
    "n8n-operator": {
      "description": "Handle n8n workflows, nodes, health checks, and version queries",
      "prompt": "{file:./prompts/n8n-operator.txt}",
      "permission": {
        "*": "ask",
        "n8n-mcp_*": "allow",
        "notion_*": "deny"
      }
    }
  }

