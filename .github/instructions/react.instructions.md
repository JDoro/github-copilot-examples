---
title: React Copilot Instructions
applyTo: "**/*.js,**/*.jsx,**/*.ts,**/*.tsx"
---
# React Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate code for a 
React web application or library.

This is an extension of the .github/copilot-instructions.md file.

## Coding Standards

### Naming Conventions

- Use singular nouns for component names (e.g., Button, InputField).
- Use plural nouns for collections (e.g., UserList, ProductArray).
- Use verbs for function names that perform actions (e.g., fetchData, 
handleClick).
- Use nouns for function names that return values (e.g., getUser, calculateTotal).
- Prefix event handler functions with "handle" (e.g., handleSubmit, handleChange).
- Prefix event names with "on" (e.g., onClick, onSubmit).
- Use "use" prefix for custom hooks (e.g., useFetch, useAuth).

### Comments

- Use JSDoc style comments for functions, classes, and components to describe
their purpose, parameters, and return values.
- Use inline comments sparingly to explain complex logic or decisions in the code.

### Strings

- Use template literals for string interpolation and multi-line strings.
- Prefer single quotes for strings unless the string contains a single quote that
needs to be escaped.
- Use descriptive variable names for string constants to improve readability.
- Use double quotes for JSX attributes.

### Style

- Follow the Airbnb JavaScript Style Guide for React projects.
- Use Prettier for code formatting to ensure consistent style across the codebase.
- Use ESLint with the React plugin to enforce coding standards and catch potential
issues.
- Use arrow functions `=>` over anonymous functions
- Prefer destructuring assignment for props and state objects in components.
- Use React Fragments (`<>...</>`) to group multiple elements without adding extra
nodes to the DOM.
- Whenever possible, use in top-level scopes `export function x(…) {…}` instead 
of `export const x = (…) => {…}`. One advantage of using the `function` keyword 
is that the stack-trace shows a good name when debugging.

### Code Quality

- Prefer `async/await` syntax for handling asynchronous operations over `.then()` and
`.catch()`.
- If you create any temporary new files, scripts, or helper files for iteration,
clean up these files by removing them at the end of the task
- Do not use `any` or `unknown` as the type for variables, parameters, or return
values unless absolutely necessary. If they need type annotations, they should 
have proper types or interfaces defined.
- Never duplicate imports. Always reuse existing imports if they are present.
- When creating a new component, create a new directory for it that includes the
component file, its styles (if any)`*.styles.(ts|js|css|scss)`, and its 
tests `*.test.(ts|js|tsx|jsx)`.
- Prefer const declarations for variables that are not reassigned.
- Always handle potential errors in asynchronous operations using try/catch blocks.

### Components

- Ensure all React components are properly typed using TypeScript interfaces or
  types.
- Ensure all components are functional components using React Hooks instead of
  class components, unless there is a specific reason to use a class component.
- Avoid duplicating state across components by lifting state up to a common ancestor or
  using context or state management libraries or composing new values with 
  `useMemo`.
- Encapsulate reusable logic in custom hooks to promote code reuse and 
separation of concerns.
