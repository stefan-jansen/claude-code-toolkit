---
allowed-tools: [Read, Write, Bash]
argument-hint: "[project-name]"
description: Set up a new JavaScript/Node.js project with modern tooling and Claude framework integration
---

# JavaScript Project Setup

I'll set up a new JavaScript/Node.js project with Jest, ESLint, Prettier, and the Claude Code Framework.

```bash
# Constants
readonly CLAUDE_DIR=".claude"
PROJECT_NAME="${1}"

# If no project name provided, use current directory name
if [ -z "$PROJECT_NAME" ]; then
    PROJECT_NAME=$(basename "$PWD")
fi

echo "🌐 Setting up JavaScript/Node.js project: $PROJECT_NAME"
echo ""

# Create directory structure
mkdir -p src
mkdir -p tests

# Create package.json
cat > package.json << EOF
{
  "name": "$PROJECT_NAME",
  "version": "0.1.0",
  "description": "A JavaScript project",
  "main": "src/index.js",
  "scripts": {
    "start": "node src/index.js",
    "test": "jest",
    "lint": "eslint src/",
    "format": "prettier --write src/"
  },
  "devDependencies": {
    "jest": "^29.0.0",
    "eslint": "^9.0.0",
    "prettier": "^3.0.0",
    "@types/node": "^20.0.0"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
EOF

# Create main source file
cat > src/index.js << EOF
/**
 * Main entry point for $PROJECT_NAME
 */

function main() {
    console.log("Hello from $PROJECT_NAME!");
}

if (require.main === module) {
    main();
}

module.exports = { main };
EOF

# Create test file
cat > tests/index.test.js << EOF
const { main } = require('../src/index');

describe('main function', () => {
    test('should run without errors', () => {
        expect(() => main()).not.toThrow();
    });
});
EOF

# Create .gitignore
cat > .gitignore << 'EOF'
# Node
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
# Uncomment for library packages (applications should commit lockfiles):
# package-lock.json
# yarn.lock

# Testing
coverage/
.nyc_output/

# Build
dist/
build/
*.tgz

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Environment
.env
.env.local
.env.*.local

# Logs
logs/
*.log
EOF

echo "✅ JavaScript project structure created"
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

JavaScript/Node.js project with modern tooling.

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
echo "✅ JavaScript project setup complete!"
echo ""
echo "Next steps:"
echo "  1. cd into project directory (if not already there)"
echo "  2. npm install  (or: yarn install, pnpm install)"
echo "  3. npm test"
echo "  4. npm start"
echo ""
echo "Project structure:"
echo "  src/         - Source code"
echo "  tests/       - Test files"
echo "  .claude/     - Claude framework"
```
