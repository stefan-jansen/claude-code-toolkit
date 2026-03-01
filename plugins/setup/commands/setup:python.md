---
allowed-tools: [Read, Write, Bash]
argument-hint: "[project-name] [--minimal|--standard|--full]"
description: Set up a new Python project with quality tools and Claude framework integration
---

# Python Project Setup

I'll set up a new Python project with modern tooling and the Claude Code Framework.

## Setup Levels

**`--minimal`** - Just the essentials (pyproject.toml, basic .gitignore)
**`--standard`** (default) - Production-ready (pytest, ruff, mypy, pre-commit, Makefile)
**`--full`** - Enterprise-grade (docs, CI/CD, security scanning)

```bash
# Constants
readonly CLAUDE_DIR=".claude"
readonly SETUP_LEVEL="${2:-standard}"  # Default to standard
PROJECT_NAME="${1}"

# If no project name provided, use current directory name
if [ -z "$PROJECT_NAME" ]; then
    PROJECT_NAME=$(basename "$PWD")
fi

echo "🐍 Setting up Python project: $PROJECT_NAME"
echo "📋 Setup level: $SETUP_LEVEL"
echo ""

# Create directory structure
mkdir -p src/$PROJECT_NAME
mkdir -p tests
mkdir -p docs

# Generate configuration based on level
case "$SETUP_LEVEL" in
    --minimal|minimal)
        echo "Creating minimal Python project..."

        # Basic pyproject.toml
        cat > pyproject.toml << 'EOF'
[project]
name = "$PROJECT_NAME"
version = "0.1.0"
description = "A Python package"
requires-python = ">=3.9"
dependencies = []

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
EOF
        sed -i '' "s/\$PROJECT_NAME/$PROJECT_NAME/g" pyproject.toml

        # Minimal .gitignore
        cat > .gitignore << 'EOF'
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
dist/
*.egg-info/
.pytest_cache/
.mypy_cache/
.ruff_cache/
venv/
.venv/
*.log
.DS_Store
EOF
        echo "✅ Minimal Python setup complete"
        ;;

    --full|full)
        echo "Creating comprehensive Python project..."

        # Full includes everything from standard, plus extras
        # Start with standard pyproject.toml but add extra deps
        cat > pyproject.toml << 'EOF'
[project]
name = "$PROJECT_NAME"
version = "0.1.0"
description = "A Python package"
requires-python = ">=3.9"
dependencies = []

[project.optional-dependencies]
dev = [
    "pytest>=8.0",
    "pytest-cov>=5.0",
    "ruff>=0.5.0",
    "mypy>=1.10",
    "pre-commit>=3.7",
]
docs = [
    "mkdocs>=1.6",
    "mkdocs-material>=9.5",
]
security = [
    "bandit>=1.7",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.ruff]
line-length = 88
target-version = "py39"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP", "S"]
ignore = ["E501"]

[tool.mypy]
python_version = "3.9"
warn_return_any = true
warn_unused_configs = true
strict = true

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "--cov=src --cov-report=term-missing"

[tool.coverage.run]
source = ["src"]

[tool.bandit]
exclude_dirs = ["tests"]
EOF
        sed -i '' "s/\$PROJECT_NAME/$PROJECT_NAME/g" pyproject.toml

        # Makefile with full targets
        cat > Makefile << 'EOF'
.PHONY: help install dev test lint format type-check clean docs security

help:
	@echo "Available commands:"
	@echo "  make install       Install package"
	@echo "  make dev           Install with dev + docs + security deps"
	@echo "  make test          Run tests"
	@echo "  make lint          Run linting"
	@echo "  make format        Format code"
	@echo "  make type-check    Run type checking"
	@echo "  make docs          Build documentation"
	@echo "  make security      Run security scan"
	@echo "  make clean         Clean build artifacts"

install:
	pip install -e .

dev:
	pip install -e ".[dev,docs,security]"
	pre-commit install

test:
	pytest

lint:
	ruff check src/ tests/

format:
	ruff format src/ tests/

type-check:
	mypy src/

docs:
	mkdocs build

security:
	bandit -r src/

clean:
	rm -rf build/ dist/ *.egg-info site/
	find . -type d -name __pycache__ -exec rm -rf {} + 2>/dev/null || true
	find . -type f -name "*.pyc" -delete
EOF

        # Standard .gitignore
        cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
dist/
*.egg-info/
.pytest_cache/
.mypy_cache/
.ruff_cache/
venv/
.venv/
*.log
.DS_Store
.env
site/
EOF

        echo "✅ Full Python setup complete"
        ;;

    --standard|standard|*)
        echo "Creating standard Python project with quality tools..."

        # Standard pyproject.toml
        cat > pyproject.toml << 'EOF'
[project]
name = "$PROJECT_NAME"
version = "0.1.0"
description = "A Python package"
requires-python = ">=3.9"
dependencies = []

[project.optional-dependencies]
dev = [
    "pytest>=8.0",
    "pytest-cov>=5.0",
    "ruff>=0.5.0",
    "mypy>=1.10",
    "pre-commit>=3.7",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.ruff]
line-length = 88
target-version = "py39"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP"]
ignore = ["E501"]  # Line length handled by formatter

[tool.mypy]
python_version = "3.9"
warn_return_any = true
warn_unused_configs = true

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "--cov=src --cov-report=term-missing"

[tool.coverage.run]
source = ["src"]
EOF
        sed -i '' "s/\$PROJECT_NAME/$PROJECT_NAME/g" pyproject.toml

        # Pre-commit configuration
        cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.5.0
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.10.0
    hooks:
      - id: mypy
        files: ^src/
EOF

        # Makefile
        cat > Makefile << 'EOF'
.PHONY: help install dev test lint format type-check clean

help:
	@echo "Available commands:"
	@echo "  make install    Install package"
	@echo "  make dev        Install with dev dependencies"
	@echo "  make test       Run tests"
	@echo "  make lint       Run linting"
	@echo "  make format     Format code"
	@echo "  make type-check Run type checking"
	@echo "  make clean      Clean build artifacts"

install:
	pip install -e .

dev:
	pip install -e ".[dev]"
	pre-commit install

test:
	pytest

lint:
	ruff check src/ tests/

format:
	ruff format src/ tests/

type-check:
	mypy src/

clean:
	rm -rf build/ dist/ *.egg-info
	find . -type d -name __pycache__ -exec rm -rf {} + 2>/dev/null || true
	find . -type f -name "*.pyc" -delete
EOF

        # Standard .gitignore
        cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST

# Testing
.pytest_cache/
.coverage
htmlcov/
.tox/
.mypy_cache/
.ruff_cache/

# Virtual Environment
venv/
.venv/
env/
ENV/

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Project
*.log
.env
EOF
        echo "✅ Standard Python setup complete"
        ;;
esac

# Create basic Python files
touch src/$PROJECT_NAME/__init__.py
cat > src/$PROJECT_NAME/main.py << EOF
"""Main module for $PROJECT_NAME."""

def main():
    """Main entry point."""
    print("Hello from $PROJECT_NAME!")

if __name__ == "__main__":
    main()
EOF

# Create basic test
cat > tests/test_main.py << EOF
"""Tests for main module."""
import pytest
from $PROJECT_NAME.main import main

def test_main():
    """Test main function."""
    # Add your tests here
    assert True
EOF

echo ""
echo "🔧 Adding Claude Code Framework..."

# Create .claude directory structure
mkdir -p $CLAUDE_DIR/work
mkdir -p $CLAUDE_DIR/memory
mkdir -p $CLAUDE_DIR/reference
mkdir -p $CLAUDE_DIR/hooks

# Create project CLAUDE.md
cat > CLAUDE.md << EOF
# $PROJECT_NAME

Python project using modern development tools.

## Project Knowledge
@.claude/memory/project_state.md
@.claude/memory/dependencies.md
@.claude/memory/conventions.md
@.claude/memory/decisions.md

## Current Work
@.claude/work/README.md
EOF

# Create work README
cat > $CLAUDE_DIR/work/README.md << 'EOF'
# Work Units

Active development work units are organized here with date-prefixed directories:
- YYYY-MM-DD_NN_topic/ - Work unit format with date and running counter

Each work unit contains:
- metadata.json - Work unit metadata and status
- state.json - Task tracking and implementation plan

See workflow plugin commands (/explore, /plan, /next, /ship) for managing work units.
EOF

echo ""
echo "✅ Python project setup complete!"
echo ""
echo "Next steps:"
echo "  1. cd into project directory (if not already there)"
echo "  2. python -m venv .venv"
echo "  3. source .venv/bin/activate"
echo "  4. make dev  (or: pip install -e '.[dev]')"
echo "  5. pre-commit install"
echo "  6. make test"
echo ""
echo "Project structure:"
echo "  src/$PROJECT_NAME/  - Source code"
echo "  tests/              - Test files"
echo "  .claude/            - Claude framework"
```
