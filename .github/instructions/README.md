# Copilot Instruction Files

This directory contains specialized instruction files that provide GitHub Copilot with detailed guidance for generating code in different contexts within a React UI project.

## Overview

These instruction files extend the base `.github/copilot-instructions.md` and are automatically applied based on file patterns. Each file provides comprehensive coding standards, best practices, common patterns, and examples for specific technologies and tools.

## Available Instruction Files

### Core Technologies

#### TypeScript (`typescript.instructions.md`)
- **Applies to:** `**/*.ts`, `**/*.tsx`
- **Coverage:** Type safety, interfaces vs type aliases, generics, utility types, conditional types, template literals, TypeScript best practices
- **Lines:** ~845

#### React (`react.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** Component patterns, hooks, functional components, naming conventions, code quality standards
- **Lines:** ~79

### State Management

#### Zustand (`zustand.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** Store creation, TypeScript typing, selectors, middleware (persist, devtools, immer), async actions, slice pattern, testing
- **Lines:** ~660

### Form Handling

#### React Hook Form (`react-hook-form.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** Form setup, field registration, validation with Zod, error handling, controlled components, field arrays, file uploads, integration with UI libraries
- **Lines:** ~690

### Data Fetching & Routing

#### TanStack Query (`tanstack-query.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** Query hooks, mutations, caching, invalidation, optimistic updates, error handling, pagination, infinite queries
- **Lines:** ~193

#### TanStack Router (`tanstack-router.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** Route configuration, loaders, type-safe navigation, search params, error boundaries, layouts, code splitting
- **Lines:** ~369

### API Integration

#### Axios (`axios.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** Instance creation, TypeScript typing, interceptors (request/response), error handling, token refresh, request cancellation, retry logic, file uploads, pagination, integration with TanStack Query
- **Lines:** ~730

### Testing

#### React Testing Library (`react-testing-library.instructions.md`)
- **Applies to:** `**/*.test.ts`, `**/*.test.tsx`, `**/*.spec.ts`, `**/*.spec.tsx`
- **Coverage:** Query priorities, user interactions with userEvent, async testing, mocking, accessibility testing with jest-axe, form testing, component testing patterns
- **Lines:** ~620

### Styling

#### Tailwind CSS (`tailwindcss.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`, `**/*.html`, `**/*.vue`, `**/*.svelte`
- **Coverage:** Utility-first approach, responsive design, dark mode, custom configuration, component patterns, performance optimization
- **Lines:** ~635

#### Material UI (`material-ui.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** Component usage, sx prop styling, theming, responsive design, form components, accessibility, integration patterns
- **Lines:** ~1053

### Quality & Performance

#### ESLint (`eslint.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** ESLint configuration, React rules, React Hooks rules, TypeScript ESLint rules, import rules, accessibility rules, auto-fix, pre-commit hooks, CI/CD integration
- **Lines:** ~490

#### React Performance (`react-performance.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** React.memo, useMemo, useCallback, code splitting, virtualization, debouncing/throttling, avoiding re-renders, context optimization, image optimization, bundle size optimization, profiling
- **Lines:** ~580

### Accessibility

#### React Accessibility (`react-accessibility.instructions.md`)
- **Applies to:** `**/*.js`, `**/*.jsx`, `**/*.ts`, `**/*.tsx`
- **Coverage:** WCAG 2.1 AA compliance, semantic HTML, ARIA attributes, keyboard navigation, focus management, color contrast, screen reader support, accessible component patterns (modals, tabs, menus), testing
- **Lines:** ~740

## File Structure

Each instruction file follows this structure:

```markdown
---
title: [Technology] Copilot Instructions
applyTo: "[file pattern glob]"
---
# [Technology] Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate code that 
uses [Technology] following best practices.

This is an extension of the .github/copilot-instructions.md file.

## Coding Standards

### Naming Conventions
[Technology-specific naming conventions]

### [Additional Sections]
[Detailed guidance, patterns, and examples]

### Best Practices
[Summary of key best practices]
```

## How They Work

1. **Automatic Application:** GitHub Copilot automatically applies instruction files based on the `applyTo` pattern when you're working in matching files.

2. **Inheritance:** All instruction files extend the base `.github/copilot-instructions.md`, meaning they inherit general coding standards while adding technology-specific guidance.

3. **Context-Aware:** The more specific the file pattern, the more targeted the guidance. For example, test files get testing-specific instructions, while TypeScript files get type-safety guidance.

## Usage Guidelines

### For Developers

- **No manual action required:** These files work automatically when you use GitHub Copilot.
- **Review suggestions:** Always review Copilot's suggestions to ensure they align with your specific use case.
- **Update as needed:** As best practices evolve, update these files to reflect current standards.

### For Maintainers

- **Keep updated:** Regularly review and update instruction files to reflect latest best practices.
- **Add new files:** Create new instruction files for additional technologies as the project grows.
- **Test effectiveness:** Monitor Copilot's suggestions to ensure instructions are being followed.
- **Avoid conflicts:** Ensure instructions don't contradict each other across different files.

## Adding New Instruction Files

To add a new instruction file:

1. Create a new `.instructions.md` file in this directory
2. Add frontmatter with `title` and `applyTo` fields
3. Follow the established structure (see above)
4. Provide comprehensive coverage of the technology
5. Include plenty of code examples (good vs. bad patterns)
6. Add the file to the reference table in `.github/copilot-instructions.md`

## Statistics

- **Total Instruction Files:** 13
- **Total Lines:** ~7,768
- **Technologies Covered:** TypeScript, React, State Management, Forms, Data Fetching, Routing, API Integration, Testing, Styling (2 frameworks), Code Quality, Performance, Accessibility
- **File Patterns Supported:** JavaScript, TypeScript, JSX, TSX, Test files, HTML, Vue, Svelte

## Maintenance

Last Updated: 2025-12-15

### Recent Additions
- React Testing Library (2025-12-15)
- React Hook Form (2025-12-15)
- Zustand (2025-12-15)
- React Accessibility (2025-12-15)
- Axios (2025-12-15)
- ESLint (2025-12-15)
- React Performance (2025-12-15)

### Existing Files
- TypeScript (pre-existing)
- React (pre-existing)
- Material UI (pre-existing)
- Tailwind CSS (pre-existing)
- TanStack Query (pre-existing)
- TanStack Router (pre-existing)

## Contributing

When contributing to instruction files:

1. Ensure accuracy of all examples and guidance
2. Follow the established file structure
3. Include both good and bad examples
4. Keep content focused and practical
5. Test that Copilot recognizes and uses the instructions
6. Update this README with any new files

## References

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [Copilot Instructions Guide](https://docs.github.com/en/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot)
- Base instructions: `.github/copilot-instructions.md`
- Implementation plan: `plans/2025-12-15-add-react-ui-copilot-instructions.md`
