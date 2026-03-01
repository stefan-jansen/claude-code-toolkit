# Setup Plugin

**Version**: 1.0.0
**Purpose**: Project initialization and Claude Code configuration

## Overview

The setup plugin provides commands for initializing Claude Code in new and existing projects, configuring user-level settings, and setting up project-specific configurations.

## Commands

### `/setup:python [project-name]`
Set up a new Python project with modern tooling and Claude framework integration.

**Features**:
- Poetry or pip + venv support
- pytest, black, ruff, mypy
- .claude/ framework structure
- .gitignore, pyproject.toml
- Optional MCP integration

**Usage**:
```bash
/setup:python my-api
/setup:python my-ml-project --minimal
```

---

### `/setup:javascript [project-name]`
Set up a new JavaScript/Node.js project with modern tooling and Claude framework integration.

**Features**:
- npm/yarn support
- TypeScript configuration
- ESLint, Prettier
- .claude/ framework structure
- package.json, tsconfig.json

**Usage**:
```bash
/setup:javascript my-app
/setup:javascript my-frontend
```

---

### `/setup:existing`
Add Claude Code framework to an existing project with auto-detection.

**Features**:
- Auto-detects project language and framework
- Adds .claude/ structure without disrupting existing setup
- Configures plugins based on project type
- Optional MCP server setup (Serena, Context7)

**Usage**:
```bash
cd ~/my-existing-project
/setup:existing
```

---

### `/setup:user [--force]`
Initialize user-level Claude Code configuration (`~/.claude/CLAUDE.md`).

**Features**:
- Global settings and preferences
- User-level commands
- Shared memory files
- Plugin marketplace configuration

**Usage**:
```bash
/setup:user
/setup:user --force  # Overwrite existing
```

---

### `/setup:statusline`
Configure Claude Code statusline to show framework context.

**Features**:
- Shows current work unit
- Displays active memory context
- Token usage indicator
- Project state summary

**Usage**:
```bash
/setup:statusline
```

---

## Installation

### Enable in Project

Add to `.claude/settings.json`:
```json
{
  "extraKnownMarketplaces": {
    "local": {
      "source": {
        "source": "directory",
        "path": "~/agents/plugins"
      }
    }
  },
  "enabledPlugins": {
    "setup@local": true
  }
}
```

### Quick Start

```bash
# New Python project
/setup:python my-project

# New JavaScript project
/setup:javascript my-app

# Add to existing project
cd ~/my-project && /setup:existing

# Configure user settings
/setup:user
```

---

## Token Efficiency

**Design Pattern**: Subcommand pattern

- Each setup mode loads independently (~2,500-4,000 tokens)
- Only the requested command is loaded per invocation

**Example**:
- Old `/setup` (monolithic): 9,700 tokens always loaded
- New `/setup:python`: 2,900 tokens (70% reduction)

---

## Related Plugins

- **system** - Health monitoring (`/system:audit`, `/system:status`)
- **workflow** - Development workflow (`/explore`, `/plan`, `/next`)
- **memory** - Knowledge management (`/index`, `/handoff`)

---

**Part of Claude Code Framework Core Plugins** (v1.0.0)
