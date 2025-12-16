# Copilot Instructions

## Overview
These instructions are designed to help GitHub Copilot understand the context 
and requirements for generating code in this repository.

### Repository Purpose

### Key Features

### Languages

### Frameworks

### Tools

### Folder Structure

## Workflows

### Creating Pull Requests

- Ensure all code changes are made through pull requests.
- Include a clear description of the changes made in the pull request.
- Link related issues or tasks in the pull request description.
- Ensure that all tests pass before creating a pull request or committing 
  changes to a pull request.
- Ensure all coding standards are followed and any tool enforcing those standards 
  (e.g., ESLint, Prettier) report no issues.
- If the changes involve a web application or user interface, include 
  screenshots or screen recordings of the changes made.
- If the changes involve a web application run the application and
  verify that the chrome devtools console shows no errors or warnings.
- If the changes involve bug fixes, include steps to reproduce the issue and 
  verify the fix.
- If the changes involve performance improvements, include before-and-after 
  benchmarks or metrics.

### Reviewing Pull Requests

- Review code for adherence to coding standards.
- If the changes involve a web application or user interface, verify the 
  functionality by running the application and testing the changes. Ensuring 
  that the chrome devtools console shows no errors or warnings.
- Provide constructive feedback and suggestions for improvement.

## Coding Standards

### Indentation

Use 2 spaces for indentation.

### Line Length

Limit lines to a maximum of 80 characters.

### Naming Conventions

- Use camelCase for variable and function names.
- Use PascalCase for component and class names.
- Use UPPER_SNAKE_CASE for constants.
- Use kebab-case for file and folder names.
- Use descriptive names that clearly indicate the purpose of the variable, 
function, or component.
- Avoid abbreviations unless they are widely recognized.
- Prefix boolean variables and functions with "is", "has", "can", or "should".
- Avoid using reserved words or names that may conflict with existing libraries.
- Ensure consistency in naming conventions throughout the codebase.

### Comments

- Use comments to explain the purpose of complex code blocks or functions.
- Avoid redundant comments that do not add value or clarity to the code.
- Keep comments up-to-date with code changes to prevent misinformation.
- Use comments to indicate TODOs, FIXMEs, and other notes for future improvements or
  refactoring.

### Style
- Always use semicolons at the end of statements.
- Always surround loop and conditional blocks with curly braces `{}`.
- Open braces `{` should be on the same line as the control statement.

## Specialized Instructions

The following specialized instruction files provide detailed guidance for specific technologies and patterns used in React UI projects. These instructions extend the base coding standards defined above.

| Pattern | File Path | Description |
| ------- | --------- | ----------- |
| **/*.ts,**/*.tsx | `.github/instructions/typescript.instructions.md` | TypeScript best practices, type safety, utility types, and advanced patterns |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/react.instructions.md` | React component patterns, hooks, and best practices |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/react-hook-form.instructions.md` | Form handling with React Hook Form, validation with Zod, and form patterns |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/tanstack-form.instructions.md` | Form handling with TanStack Form, type-safe validation, and reactive form state |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/zustand.instructions.md` | State management with Zustand, store patterns, and middleware |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/react-accessibility.instructions.md` | WCAG 2.1 AA compliance, semantic HTML, ARIA, and accessible component patterns |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/axios.instructions.md` | API client patterns with Axios, interceptors, error handling, and TypeScript types |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/eslint.instructions.md` | ESLint configuration, rules, and code quality standards |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/react-performance.instructions.md` | React performance optimization, memoization, code splitting, and virtualization |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/tanstack-query.instructions.md` | Data fetching and caching with TanStack Query (React Query) |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/tanstack-router.instructions.md` | Routing with TanStack Router, type-safe navigation, and loaders |
| **/*.test.ts,**/*.test.tsx,**/*.spec.ts,**/*.spec.tsx | `.github/instructions/react-testing-library.instructions.md` | Testing with React Testing Library and Vitest, accessibility testing, and best practices |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx,**/*.html,**/*.vue,**/*.svelte | `.github/instructions/tailwindcss.instructions.md` | Utility-first CSS with Tailwind, responsive design, and component styling |
| **/*.js,**/*.jsx,**/*.ts,**/*.tsx | `.github/instructions/material-ui.instructions.md` | Material UI component library, theming, and styling patterns |

These instruction files are automatically applied based on the file patterns specified in their `applyTo` field. They provide comprehensive guidance on naming conventions, best practices, common patterns, and examples for each technology.

