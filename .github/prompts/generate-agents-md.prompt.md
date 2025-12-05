# Generate AGENTS.md

Create an `AGENTS.md` file in the root of the repository that provides context 
for all AI agents working with this codebase. The file should reference and 
summarize all GitHub Copilot instruction files in the project.

## Instructions

1. **Scan for instruction files** in the following locations:
   - `.github/copilot-instructions.md` (main instructions)
   - `.github/instructions/*.instructions.md` (language/framework specific)

2. **Create AGENTS.md** at the repository root with the following structure:
   - A header explaining the purpose of the file
   - A section listing all available instruction files with brief descriptions
   - A summary of key coding standards from the main instructions
   - References to framework-specific guidelines

3. **Format the file** using proper Markdown:
   - Use clear headings and sections
   - Include relative links to instruction files
   - Keep descriptions concise but informative

## Expected Output Structure

```markdown
# AGENTS.md

This file provides context for AI agents working with this repository.

## Available Copilot Instructions

| Instruction File | Description |
|------------------|-------------|
| [copilot-instructions.md](.github/copilot-instructions.md) | Main coding standards |
| ... | ... |

## Key Coding Standards

Brief summary of main standards...

## Framework-Specific Guidelines

Links and summaries of framework instructions...
```

## Notes

- The AGENTS.md file is scoped to the folder it's in and all subfolders
- It provides context that all agents can use when working in this repository
- Keep the content focused on what agents need to know to work effectively
