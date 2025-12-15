# Plan: Add Comprehensive Copilot Instruction Files for React UI Projects

## Overview

This plan outlines the creation of additional copilot instruction files to provide comprehensive guidance for different aspects of React UI development. While several instruction files already exist (React, TypeScript, Material UI, TailwindCSS, TanStack Query, and TanStack Router), there are many other common React UI development areas that would benefit from dedicated instruction files.

## Requirements

- Create instruction files for common React UI development tools and patterns
- Follow the existing instruction file format and structure
- Include frontmatter with `title` and `applyTo` fields
- Provide comprehensive coding standards, best practices, and examples
- Ensure instructions extend the base `.github/copilot-instructions.md` file
- Target commonly used libraries and patterns in modern React development
- Maintain consistency with existing instruction files

## Analysis

### Current State

The repository currently has instruction files for:
- **react.instructions.md** (79 lines) - Basic React patterns
- **typescript.instructions.md** (845 lines) - TypeScript best practices
- **material-ui.instructions.md** (1053 lines) - Material UI component library
- **tailwindcss.instructions.md** (635 lines) - Tailwind CSS utility framework
- **tanstack-query.instructions.md** (193 lines) - Data fetching and caching
- **tanstack-router.instructions.md** (369 lines) - Routing

### Missing Instruction Files

Based on common React UI development needs, the following instruction files should be added:

1. **React Testing Library / Vitest** - Testing patterns and best practices
2. **React Hook Form** - Form handling and validation
3. **Zustand / Redux Toolkit** - State management
4. **Axios / Fetch** - API client patterns
5. **Emotion / Styled Components** - CSS-in-JS styling (if not using Tailwind/MUI)
6. **React Context API** - Context patterns and best practices
7. **Performance Optimization** - React performance patterns
8. **Accessibility (a11y)** - WCAG compliance and accessible React patterns
9. **Component Patterns** - Advanced React patterns (HOCs, Render Props, Compound Components, etc.)
10. **ESLint / Prettier** - Code quality and formatting

### Files to Create

All files will be created in `.github/instructions/`:

1. `.github/instructions/react-testing-library.instructions.md` - Testing with React Testing Library and Vitest
2. `.github/instructions/react-hook-form.instructions.md` - Form handling patterns
3. `.github/instructions/zustand.instructions.md` - Zustand state management
4. `.github/instructions/redux-toolkit.instructions.md` - Redux Toolkit state management
5. `.github/instructions/axios.instructions.md` - API client patterns with Axios
6. `.github/instructions/styled-components.instructions.md` - CSS-in-JS with styled-components
7. `.github/instructions/emotion.instructions.md` - CSS-in-JS with Emotion
8. `.github/instructions/react-context.instructions.md` - Context API patterns
9. `.github/instructions/react-performance.instructions.md` - Performance optimization
10. `.github/instructions/react-accessibility.instructions.md` - Accessibility best practices
11. `.github/instructions/eslint.instructions.md` - ESLint configuration and patterns
12. `.github/instructions/vite.instructions.md` - Vite build tool configuration

### Priority Order

**High Priority** (Most commonly used):
1. React Testing Library - Essential for testing
2. React Hook Form - Most popular form library
3. Zustand - Increasingly popular state management
4. React Accessibility - Critical for production apps
5. Axios - Common API client

**Medium Priority** (Frequently used):
6. Redux Toolkit - Still widely used
7. Emotion/Styled Components - Popular CSS-in-JS solutions
8. React Performance - Important for larger apps
9. ESLint - Code quality

**Lower Priority** (Nice to have):
10. React Context - Often covered in React docs
11. Vite - Build tool specifics

## Implementation Steps

### Step 1: Create React Testing Library Instructions

**Description:** Create comprehensive instructions for testing React components with React Testing Library and Vitest

**Files:** `.github/instructions/react-testing-library.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.test.ts,**/*.test.tsx,**/*.spec.ts,**/*.spec.tsx"`
- Cover naming conventions for test files and test descriptions
- Explain query priorities (getByRole > getByLabelText > getByPlaceholderText > getByText)
- Document user-event vs fireEvent preferences
- Include async testing patterns with waitFor, findBy queries
- Cover mocking patterns (modules, timers, API calls)
- Explain testing-library principles (test user behavior, not implementation)
- Include accessibility testing with jest-axe
- Cover component testing vs integration testing
- Document snapshot testing best practices
- Include examples for common scenarios

### Step 2: Create React Hook Form Instructions

**Description:** Create instructions for form handling with React Hook Form

**Files:** `.github/instructions/react-hook-form.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover form registration patterns
- Document validation approaches (built-in, Zod, Yup)
- Explain controlled vs uncontrolled components with RHF
- Cover error handling and display
- Document form submission patterns
- Include field array patterns
- Cover integration with UI libraries (MUI, custom components)
- Explain watch, setValue, reset patterns
- Include TypeScript typing best practices
- Cover performance optimization patterns
- Document testing form components

### Step 3: Create Zustand Instructions

**Description:** Create instructions for state management with Zustand

**Files:** `.github/instructions/zustand.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover store creation patterns
- Document slice patterns for organizing stores
- Explain selector patterns and optimization
- Cover middleware usage (persist, devtools, immer)
- Document TypeScript typing patterns
- Include async actions patterns
- Cover testing strategies for stores
- Explain when to use Zustand vs Context
- Include best practices for store organization
- Document common pitfalls and solutions

### Step 4: Create React Accessibility Instructions

**Description:** Create comprehensive accessibility instructions

**Files:** `.github/instructions/react-accessibility.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover WCAG 2.1 AA compliance requirements
- Document semantic HTML usage
- Explain ARIA attributes and when to use them
- Cover keyboard navigation patterns
- Document focus management
- Include screen reader considerations
- Cover color contrast requirements
- Explain skip links and landmarks
- Document form accessibility
- Include testing with axe-core
- Cover accessible component patterns (modals, dropdowns, tabs)
- Document mobile accessibility considerations

### Step 5: Create Axios Instructions

**Description:** Create instructions for API client patterns with Axios

**Files:** `.github/instructions/axios.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover axios instance creation and configuration
- Document interceptor patterns (auth, logging, error handling)
- Explain request/response typing with TypeScript
- Cover error handling patterns
- Document retry logic
- Include request cancellation patterns
- Cover timeout configuration
- Explain base URL and environment configuration
- Document testing strategies (mocking axios)
- Include integration with React Query/SWR
- Cover file upload patterns
- Document request transformation patterns

### Step 6: Create Redux Toolkit Instructions

**Description:** Create instructions for Redux Toolkit state management

**Files:** `.github/instructions/redux-toolkit.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover slice creation patterns
- Document reducer and action patterns
- Explain createAsyncThunk for async operations
- Cover store configuration
- Document selector patterns with createSelector
- Include TypeScript typing best practices
- Cover RTK Query for API integration
- Explain middleware usage
- Document testing strategies
- Include migration from legacy Redux
- Cover best practices for store organization
- Document common pitfalls

### Step 7: Create Emotion Instructions

**Description:** Create instructions for CSS-in-JS with Emotion

**Files:** `.github/instructions/emotion.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover styled components API
- Document css prop usage
- Explain theming patterns
- Cover responsive design patterns
- Document TypeScript typing
- Include composition patterns
- Cover performance optimization
- Explain SSR considerations
- Document testing strategies
- Include migration from styled-components
- Cover best practices

### Step 8: Create Styled Components Instructions

**Description:** Create instructions for CSS-in-JS with styled-components

**Files:** `.github/instructions/styled-components.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover component creation patterns
- Document props-based styling
- Explain theming with ThemeProvider
- Cover extending styles
- Document attrs method for default props
- Include animation patterns
- Cover helper functions (css, createGlobalStyle)
- Explain TypeScript typing
- Document testing strategies
- Include performance best practices
- Cover SSR setup

### Step 9: Create React Performance Instructions

**Description:** Create instructions for React performance optimization

**Files:** `.github/instructions/react-performance.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover React.memo usage and when to use it
- Document useMemo and useCallback patterns
- Explain code splitting with React.lazy and Suspense
- Cover virtualization for long lists
- Document bundle size optimization
- Include profiling techniques
- Cover preventing unnecessary re-renders
- Explain key prop optimization
- Document image optimization
- Include web vitals monitoring
- Cover production build optimization
- Document common performance pitfalls

### Step 10: Create ESLint Instructions

**Description:** Create instructions for ESLint configuration and patterns

**Files:** `.github/instructions/eslint.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover recommended ESLint configurations
- Document React-specific rules
- Explain TypeScript ESLint integration
- Cover accessibility linting (eslint-plugin-jsx-a11y)
- Document import ordering rules
- Include custom rule configuration
- Cover hooks rules (exhaustive-deps)
- Explain disabling rules (when and how)
- Document integration with Prettier
- Include pre-commit hooks setup
- Cover CI/CD integration

### Step 11: Create Vite Instructions

**Description:** Create instructions for Vite build tool

**Files:** `.github/instructions/vite.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/vite.config.ts,**/vite.config.js"`
- Cover basic configuration
- Document environment variables
- Explain build optimization
- Cover plugin ecosystem
- Document proxy configuration for APIs
- Include TypeScript configuration
- Cover CSS preprocessing
- Explain asset handling
- Document production build optimization
- Include testing integration
- Cover migration from Create React App

### Step 12: Create React Context Instructions

**Description:** Create instructions for React Context API patterns

**Files:** `.github/instructions/react-context.instructions.md`

**Details:**
- Add frontmatter: `applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"`
- Cover Context creation patterns
- Document provider patterns
- Explain custom hook patterns for consuming context
- Cover TypeScript typing
- Document performance optimization (splitting contexts)
- Include composition patterns
- Cover when to use Context vs state management libraries
- Explain avoiding prop drilling
- Document testing context
- Include common pitfalls
- Cover SSR considerations

## Testing Strategy

### Verification Steps

1. **Validate File Format**
   - Confirm all files have proper frontmatter
   - Verify markdown formatting is consistent
   - Check that file naming follows conventions

2. **Content Review**
   - Ensure each file extends base copilot-instructions.md
   - Verify comprehensive coverage of each topic
   - Check for accurate code examples
   - Confirm TypeScript examples are type-safe
   - Validate best practices align with community standards

3. **Integration Testing**
   - Test that GitHub Copilot recognizes the instruction files
   - Verify applyTo glob patterns match intended file types
   - Confirm instructions don't conflict with each other
   - Test that more specific instructions properly extend general ones

4. **Manual Review**
   - Review each file for completeness
   - Check for consistent tone and style
   - Verify examples are practical and realistic
   - Ensure no outdated patterns or deprecated APIs

## Handoff Notes

### Implementation Priority

Start with the **High Priority** files first, as these are the most commonly used in React projects:
1. React Testing Library
2. React Hook Form
3. Zustand
4. React Accessibility
5. Axios

Then proceed to **Medium Priority** files, and finally **Lower Priority** if time permits.

### File Structure Pattern

Each instruction file should follow this structure:

```markdown
---
title: [Library Name] Copilot Instructions
applyTo: "[glob pattern]"
---
# [Library Name] Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate code that 
uses [Library Name] following best practices.

This is an extension of the .github/copilot-instructions.md file.

## Coding Standards

### Naming Conventions
### [Other relevant sections]

## Best Practices

[Comprehensive coverage of the topic]
```

### Content Guidelines

- Include plenty of code examples (both good and bad patterns)
- Use TypeScript examples where applicable
- Reference official documentation for each library
- Include common pitfalls and how to avoid them
- Keep examples practical and realistic
- Use consistent formatting and style
- Include links to official resources when helpful

### Dependencies and Relationships

Some instruction files may reference others:
- React Hook Form may reference React Context
- Redux Toolkit may reference React Performance
- Testing Library will reference all component libraries
- Accessibility should be considered across all files

### Considerations

- Not all files are necessary for every project (e.g., don't need both Zustand and Redux Toolkit)
- Some files may overlap (e.g., performance tips in React instructions vs dedicated performance file)
- The applyTo patterns should be specific enough to only apply where relevant
- Consider future additions: Storybook, Playwright, Next.js, etc.

### Quality Standards

Each instruction file should:
- Be at least 200 lines (substantive content)
- Include minimum 10 code examples
- Cover naming conventions
- Include TypeScript patterns
- Address testing strategies
- List common pitfalls
- Reference best practices from official docs
- Maintain consistency with existing instruction files

### Success Criteria

The implementation is successful when:
- All high-priority instruction files are created
- Each file follows the established format
- Content is comprehensive and accurate
- Examples are practical and demonstrate best practices
- Files integrate well with existing instructions
- GitHub Copilot can utilize the instructions effectively
