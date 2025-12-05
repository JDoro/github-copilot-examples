# GitHub Copilot Custom Agents

This directory contains custom GitHub Copilot agents for this repository.

## Agent Workflow

The agents in this repository support a structured development workflow:

```
@planner → @developer → @changelog-manager
```

1. **Planner Agent** creates detailed implementation plans
2. **Developer Agent** implements the code based on plans
3. **Changelog Manager Agent** documents the changes

## Available Agents

### Planner (`planner.agent.md`)

A specialized agent for creating detailed implementation plans that are 
persisted as markdown files in the `plans/` folder.

**Capabilities:**
- Analyzes tasks, issues, and feature requests
- Researches the codebase for context
- Creates step-by-step implementation plans
- Persists plans as markdown files
- Hands off to the developer agent

**Usage:**

```
@planner Create a plan for adding user authentication
@planner Analyze the issue and create an implementation plan
@planner Plan the refactoring of the API client module
```

**How it works:**
1. Analyzes the task or requirement
2. Explores relevant files in the codebase
3. Creates a detailed implementation plan
4. Saves the plan to `plans/YYYY-MM-DD-[description].md`
5. Provides handoff instructions for the developer agent

---

### Developer (`developer.agent.md`)

A specialized agent for implementing code based on plans from the planner 
agent, then handing off to the changelog-manager agent.

**Capabilities:**
- Reads and understands implementation plans
- Writes code following plan specifications
- Follows repository coding standards
- Tests implementations
- Hands off to changelog-manager agent

**Usage:**

```
@developer Implement the plan in plans/2025-01-15-add-feature.md
@developer Please implement the latest plan
@developer Continue implementing from step 3
```

**How it works:**
1. Loads the implementation plan from `plans/` folder
2. Understands context and coding standards
3. Implements each step in the plan
4. Tests and verifies changes
5. Hands off to changelog-manager agent

---

### Changelog Manager (`changelog-manager.agent.md`)

A specialized agent for creating and maintaining CHANGELOG.md files based on 
git commit messages.

**Capabilities:**
- Creates new CHANGELOG.md files following Keep a Changelog format
- Updates existing changelogs with new entries from git history
- Parses and categorizes git commit messages
- Follows Semantic Versioning conventions
- Supports conventional commit format

**Usage:**

```
@changelog-manager create a changelog from recent commits
@changelog-manager update the changelog with commits from last month
@changelog-manager add the latest 20 commits to the changelog
```

**How it works:**
1. Retrieves git commit history using git commands
2. Parses commit messages and categorizes them (Added, Changed, Fixed, etc.)
3. Generates or updates CHANGELOG.md with properly formatted entries
4. Follows Keep a Changelog format and best practices

**Commit Categories:**
- **Added**: New features and capabilities
- **Changed**: Changes to existing functionality
- **Deprecated**: Soon-to-be removed features
- **Removed**: Removed features
- **Fixed**: Bug fixes
- **Security**: Security vulnerability fixes

**Best Practices:**
- The agent focuses exclusively on changelog management
- It preserves existing changelog structure
- Commit messages should be clear and descriptive for best results
- Version numbers should be specified when releasing

## Creating New Agents

To create a new custom agent:

1. Create a new `.agent.md` file in this directory
2. Add YAML frontmatter with agent configuration:
   ```yaml
   ---
   name: agent-name
   description: Brief description of the agent's purpose
   tools:
     - bash
     - view
     - edit
     - create
   ---
   ```
   Note: Omit the `target` field to make the agent available in both VS Code and GitHub.com. 
   Use `target: vscode` to restrict to VS Code only, or `target: github-copilot` for GitHub.com only.
3. Add detailed instructions in Markdown format
4. Test the agent in GitHub Copilot Chat

## Resources

- [GitHub Copilot Custom Agents Documentation](https://docs.github.com/en/copilot/reference/custom-agents-configuration)
- [Keep a Changelog](https://keepachangelog.com/)
- [Semantic Versioning](https://semver.org/)
