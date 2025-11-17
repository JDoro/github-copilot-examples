# GitHub Copilot Custom Agents

This directory contains custom GitHub Copilot agents for this repository.

## Available Agents

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

This agent is available in both VS Code and GitHub.com. You can invoke it in GitHub Copilot Chat using:

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
