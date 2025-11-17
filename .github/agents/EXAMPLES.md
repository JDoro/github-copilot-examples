# Changelog Manager Agent - Usage Examples

This document demonstrates practical examples of how to use the changelog 
manager agent with GitHub Copilot. The agent is available in both VS Code 
and GitHub.com.

## Example 1: Creating a New Changelog

**User prompt (in VS Code or GitHub.com):**
```
@changelog-manager create a changelog from recent commits
```

**What the agent will do:**
1. Run `git log --pretty=format:"%h|%ai|%s|%an" --no-merges -n 50`
2. Parse and categorize each commit
3. Create CHANGELOG.md with proper structure
4. Organize commits into categories

**Sample output:**
```markdown
# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

### Added
- GitHub Copilot custom agent for changelog management
- Changelog manager agent with git commit parsing capabilities

### Changed
- Enhanced Copilot instructions for pull request reviews
- Added testing guidelines to repository documentation
```

## Example 2: Updating Existing Changelog

**User prompt:**
```
@changelog-manager update the changelog with commits from the last 2 weeks
```

**What the agent will do:**
1. Check if CHANGELOG.md exists
2. Run `git log --since="2 weeks ago" --pretty=format:"%h|%ai|%s|%an" --no-merges`
3. Parse new commits
4. Add entries to the [Unreleased] section
5. Preserve existing content

## Example 3: Using Conventional Commits

**Given these commits:**
```
feat: add user authentication system
fix: resolve memory leak in data processing
docs: update API documentation
chore: update dependencies
```

**Agent categorization:**
- `feat: add user authentication system` → **Added**
- `fix: resolve memory leak in data processing` → **Fixed**
- `docs: update API documentation` → **Changed** (documentation)
- `chore: update dependencies` → **Changed** (maintenance)

**Generated changelog entry:**
```markdown
## [Unreleased]

### Added
- User authentication system

### Changed
- Updated API documentation
- Updated dependencies

### Fixed
- Resolved memory leak in data processing
```

## Example 4: Filtering Commits

**User prompt:**
```
@changelog-manager add only feature commits from the last month
```

**What the agent will do:**
1. Run git log with date filter: `--since="1 month ago"`
2. Filter commits containing "feat:", "feature", "add", "new"
3. Create entries only in the "Added" section

## Example 5: Preparing a Release

**User prompt:**
```
@changelog-manager prepare changelog for version 1.2.0 release
```

**What the agent will do:**
1. Ask for confirmation of version number
2. Move [Unreleased] entries to new [1.2.0] section
3. Add release date
4. Create new empty [Unreleased] section

**Before:**
```markdown
## [Unreleased]

### Added
- New dashboard feature
- Export functionality

### Fixed
- Bug in user profile
```

**After:**
```markdown
## [Unreleased]

## [1.2.0] - 2025-11-16

### Added
- New dashboard feature
- Export functionality

### Fixed
- Bug in user profile
```

## Tips for Best Results

1. **Clear commit messages**: The agent works best with descriptive commits
   - Good: `feat: add user profile editing capability`
   - Poor: `update stuff`

2. **Use conventional commits**: Helps with automatic categorization
   - `feat:` for features
   - `fix:` for bug fixes
   - `docs:` for documentation
   - `chore:` for maintenance

3. **Provide context**: Tell the agent the scope you need
   - "Last 30 days"
   - "Since version 1.0.0"
   - "Only feature commits"

4. **Review generated content**: The agent creates a draft; you can refine it
   - Check for duplicates
   - Combine related entries
   - Add user-facing descriptions

## Advanced Usage

### Custom Date Range
```
@changelog-manager create changelog entries for commits between 2025-10-01 and 2025-11-01
```

### Specific Branch
```
@changelog-manager generate changelog from the feature/new-ui branch
```

### Exclude Patterns
```
@changelog-manager update changelog but exclude commits with "WIP" or "temp"
```

### Include Only Specific Files
```
@changelog-manager add changelog entries for commits affecting src/api directory
```

## Integration with CI/CD

The agent can be part of your release workflow:

1. **Pre-release checklist**:
   - Update version numbers
   - Run `@changelog-manager prepare changelog for release`
   - Review and edit generated entries
   - Commit updated CHANGELOG.md

2. **Automated checks**:
   - Ensure CHANGELOG.md is updated in pull requests
   - Verify format compliance
   - Check for [Unreleased] section

## Troubleshooting

**Issue:** Agent includes trivial commits
- **Solution:** Ask to exclude specific patterns or rephrase request

**Issue:** Commits are miscategorized
- **Solution:** Provide specific category mapping in your request

**Issue:** Generated format doesn't match existing style
- **Solution:** The agent preserves existing structure; specify format preferences

**Issue:** Need to exclude certain commits
- **Solution:** Specify exclusion criteria in your prompt

## References

- [Keep a Changelog](https://keepachangelog.com/)
- [Semantic Versioning](https://semver.org/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [GitHub Copilot Custom Agents](https://docs.github.com/en/copilot/reference/custom-agents-configuration)
