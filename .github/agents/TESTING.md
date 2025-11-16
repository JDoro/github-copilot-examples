# Testing the Changelog Manager Agent

This document provides examples and test cases for the changelog manager agent.

## Test Case 1: Agent File Format Validation

**Expected:** The agent file should have valid YAML frontmatter and proper structure.

**Validation:**
- ✅ File extension is `.agent.md`
- ✅ YAML frontmatter is present with required fields (name, description)
- ✅ Tools are properly listed
- ✅ Instructions are in Markdown format

## Test Case 2: Git Commit Parsing

**Command to test:**
```bash
git log --pretty=format:"%h|%ai|%s|%an" --no-merges -n 10
```

**Expected categories:**
- `fix:` or "fix" → Fixed
- `feat:` or "add" → Added
- `chore:` or "update" → Changed
- `security:` → Security

## Test Case 3: CHANGELOG.md Format

**Expected format:**
```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Feature entries

### Changed
- Change entries

### Fixed
- Bug fix entries
```

## Test Case 4: Usage Examples

**Example 1:** Create new changelog
```
@changelog-manager create a changelog from the last 20 commits
```

**Example 2:** Update existing changelog
```
@changelog-manager update the changelog with commits since last week
```

**Example 3:** Categorize specific commits
```
@changelog-manager add commits from the feature branch to changelog
```

## Verification Steps

1. **Agent Discovery:**
   - GitHub Copilot should recognize the agent in `.github/agents/`
   - Agent should appear in Copilot Chat interface
   - Agent name should be `changelog-manager`

2. **Functionality:**
   - Agent can read git commit history
   - Agent can parse and categorize commits
   - Agent can create/update CHANGELOG.md
   - Agent follows Keep a Changelog format

3. **Output Quality:**
   - Generated changelog has proper Markdown syntax
   - Entries are categorized correctly
   - Chronological order is maintained
   - Format is consistent with standards

## Known Limitations

- Agent requires git repository with commit history
- Best results with clear, descriptive commit messages
- Version numbers require user input
- Cannot modify historical changelog entries automatically

## Success Criteria

✅ Agent file is properly formatted with valid YAML frontmatter
✅ Agent includes comprehensive instructions for changelog management
✅ Sample CHANGELOG.md demonstrates the expected format
✅ Documentation explains usage and capabilities
✅ Repository structure supports custom agents (.github/agents/)
