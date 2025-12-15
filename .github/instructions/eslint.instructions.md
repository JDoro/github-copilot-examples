---
title: ESLint Copilot Instructions
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
---
# ESLint Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate code that 
follows ESLint best practices and avoids common linting errors.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React and TypeScript Copilot Instructions.

## Coding Standards

### ESLint Configuration

- Always use a comprehensive ESLint configuration:
  ```json
  {
    "extends": [
      "eslint:recommended",
      "plugin:@typescript-eslint/recommended",
      "plugin:react/recommended",
      "plugin:react-hooks/recommended",
      "plugin:jsx-a11y/recommended",
      "prettier"
    ],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
      "ecmaVersion": "latest",
      "sourceType": "module",
      "ecmaFeatures": {
        "jsx": true
      }
    },
    "plugins": [
      "@typescript-eslint",
      "react",
      "react-hooks",
      "jsx-a11y",
      "import"
    ],
    "rules": {
      // Custom rules
    }
  }
  ```

### Common Rules

- **no-console**: Warn on console statements in production code:
  ```typescript
  // Avoid in production
  console.log('Debug message');
  
  // Use instead
  if (import.meta.env.DEV) {
    console.log('Debug message');
  }
  ```

- **no-unused-vars**: Remove unused variables and imports:
  ```typescript
  // Avoid
  import { useState, useEffect } from 'react';
  const [count] = useState(0);
  
  // Good - Remove unused imports and variables
  import { useState } from 'react';
  const [count, setCount] = useState(0);
  ```

- **no-var**: Always use const or let, never var:
  ```typescript
  // Avoid
  var count = 0;
  
  // Good
  const count = 0;
  let mutableCount = 0;
  ```

- **prefer-const**: Use const for variables that are never reassigned:
  ```typescript
  // Avoid
  let name = 'John';
  let age = 30;
  
  // Good
  const name = 'John';
  const age = 30;
  ```

- **no-explicit-any**: Avoid using 'any' type:
  ```typescript
  // Avoid
  function processData(data: any) {
    return data.value;
  }
  
  // Good
  function processData(data: { value: string }) {
    return data.value;
  }
  
  // Or use unknown and type guard
  function processData(data: unknown) {
    if (isValidData(data)) {
      return data.value;
    }
  }
  ```

### React Rules

- **react/prop-types**: Use TypeScript types instead of PropTypes:
  ```typescript
  // Avoid
  Component.propTypes = {
    name: PropTypes.string.isRequired,
  };
  
  // Good
  type Props = {
    name: string;
  };
  
  function Component({ name }: Props) {
    return <div>{name}</div>;
  }
  ```

- **react/jsx-key**: Always provide keys for list items:
  ```tsx
  // Avoid
  {users.map((user) => (
    <div>{user.name}</div>
  ))}
  
  // Good
  {users.map((user) => (
    <div key={user.id}>{user.name}</div>
  ))}
  ```

- **react/jsx-no-target-blank**: Use rel="noopener noreferrer" with target="_blank":
  ```tsx
  // Avoid
  <a href="https://example.com" target="_blank">Link</a>
  
  // Good
  <a href="https://example.com" target="_blank" rel="noopener noreferrer">
    Link
  </a>
  ```

- **react/self-closing-comp**: Self-close components without children:
  ```tsx
  // Avoid
  <Component></Component>
  <div></div>
  
  // Good
  <Component />
  <div />
  ```

### React Hooks Rules

- **react-hooks/rules-of-hooks**: Call hooks at the top level:
  ```typescript
  // Avoid
  function Component({ condition }) {
    if (condition) {
      const [state, setState] = useState(0); // Error!
    }
  }
  
  // Good
  function Component({ condition }) {
    const [state, setState] = useState(0);
    
    if (condition) {
      setState(1);
    }
  }
  ```

- **react-hooks/exhaustive-deps**: Include all dependencies in useEffect:
  ```typescript
  // Avoid - missing dependency
  useEffect(() => {
    fetchUser(userId);
  }, []); // userId is missing
  
  // Good
  useEffect(() => {
    fetchUser(userId);
  }, [userId]);
  
  // Or disable if intentional
  useEffect(() => {
    fetchUser(userId);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);
  ```

- Use useCallback for stable function references:
  ```typescript
  // When callback is a dependency
  const handleClick = useCallback(() => {
    doSomething(value);
  }, [value]);
  
  useEffect(() => {
    handleClick();
  }, [handleClick]);
  ```

### TypeScript ESLint Rules

- **@typescript-eslint/no-unused-vars**: Remove unused variables:
  ```typescript
  // Configure to ignore variables starting with underscore
  {
    "@typescript-eslint/no-unused-vars": [
      "error",
      {
        "argsIgnorePattern": "^_",
        "varsIgnorePattern": "^_"
      }
    ]
  }
  
  // Usage
  function handleEvent(_event: Event, data: Data) {
    console.log(data);
  }
  ```

- **@typescript-eslint/explicit-function-return-type**: Specify return types for public APIs:
  ```typescript
  // Avoid
  export function getUser(id: string) {
    return fetchUser(id);
  }
  
  // Good
  export function getUser(id: string): Promise<User> {
    return fetchUser(id);
  }
  ```

- **@typescript-eslint/no-floating-promises**: Await or handle promises:
  ```typescript
  // Avoid
  function Component() {
    useEffect(() => {
      fetchData(); // Floating promise
    }, []);
  }
  
  // Good
  function Component() {
    useEffect(() => {
      void fetchData(); // Explicitly void
      // Or
      fetchData().catch(console.error);
    }, []);
  }
  ```

### Import Rules

- **import/order**: Order imports consistently:
  ```typescript
  // Good - Organized imports
  // 1. External libraries
  import React, { useState } from 'react';
  import { useQuery } from '@tanstack/react-query';
  
  // 2. Internal modules
  import { apiClient } from '@/lib/api';
  import { useAuth } from '@/hooks/useAuth';
  
  // 3. Components
  import { Button } from '@/components/Button';
  import { Input } from '@/components/Input';
  
  // 4. Types
  import type { User } from '@/types';
  
  // 5. Styles
  import styles from './Component.module.css';
  ```

- Configure import/order rule:
  ```json
  {
    "import/order": [
      "error",
      {
        "groups": [
          "builtin",
          "external",
          "internal",
          "parent",
          "sibling",
          "index"
        ],
        "pathGroups": [
          {
            "pattern": "react",
            "group": "external",
            "position": "before"
          },
          {
            "pattern": "@/**",
            "group": "internal"
          }
        ],
        "alphabetize": {
          "order": "asc",
          "caseInsensitive": true
        },
        "newlines-between": "always"
      }
    ]
  }
  ```

- **import/no-duplicates**: Combine imports from the same module:
  ```typescript
  // Avoid
  import { useState } from 'react';
  import { useEffect } from 'react';
  
  // Good
  import { useState, useEffect } from 'react';
  ```

### Accessibility Rules

- **jsx-a11y/alt-text**: Provide alt text for images:
  ```tsx
  // Avoid
  <img src="/logo.png" />
  
  // Good
  <img src="/logo.png" alt="Company Logo" />
  <img src="/decoration.png" alt="" aria-hidden="true" />
  ```

- **jsx-a11y/anchor-is-valid**: Use proper anchor elements:
  ```tsx
  // Avoid
  <a onClick={handleClick}>Click</a>
  
  // Good
  <a href="/page" onClick={handleClick}>Click</a>
  <button onClick={handleClick}>Click</button>
  ```

- **jsx-a11y/label-has-associated-control**: Associate labels with inputs:
  ```tsx
  // Avoid
  <label>Email</label>
  <input type="email" />
  
  // Good
  <label htmlFor="email">Email</label>
  <input id="email" type="email" />
  ```

### Disabling Rules

- Disable rules only when absolutely necessary:
  ```typescript
  // Disable for a single line
  // eslint-disable-next-line @typescript-eslint/no-explicit-any
  const data: any = externalLibraryCall();
  
  // Disable for a block
  /* eslint-disable @typescript-eslint/no-explicit-any */
  function legacyFunction(data: any) {
    return data;
  }
  /* eslint-enable @typescript-eslint/no-explicit-any */
  
  // Disable for entire file (avoid if possible)
  /* eslint-disable @typescript-eslint/no-explicit-any */
  ```

- Always provide a reason when disabling:
  ```typescript
  // eslint-disable-next-line @typescript-eslint/no-explicit-any -- Legacy API requires any
  const result: any = legacyApi();
  ```

### Auto-fix Configuration

- Configure auto-fix on save in VS Code:
  ```json
  {
    "editor.codeActionsOnSave": {
      "source.fixAll.eslint": true
    },
    "eslint.validate": [
      "javascript",
      "javascriptreact",
      "typescript",
      "typescriptreact"
    ]
  }
  ```

### Integration with Prettier

- Ensure ESLint and Prettier work together:
  ```json
  {
    "extends": [
      "eslint:recommended",
      "plugin:@typescript-eslint/recommended",
      "plugin:react/recommended",
      "prettier" // Must be last
    ]
  }
  ```

- Use eslint-config-prettier to disable conflicting rules.

### Pre-commit Hooks

- Use lint-staged for pre-commit linting:
  ```json
  {
    "lint-staged": {
      "*.{ts,tsx,js,jsx}": [
        "eslint --fix",
        "prettier --write"
      ]
    }
  }
  ```

- Configure husky for git hooks:
  ```bash
  npx husky-init
  npx husky add .husky/pre-commit "npx lint-staged"
  ```

### CI/CD Integration

- Add linting to CI pipeline:
  ```yaml
  # .github/workflows/lint.yml
  name: Lint
  on: [push, pull_request]
  
  jobs:
    lint:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - uses: actions/setup-node@v3
        - run: npm ci
        - run: npm run lint
  ```

- Fail builds on linting errors:
  ```json
  {
    "scripts": {
      "lint": "eslint . --ext .ts,.tsx,.js,.jsx --max-warnings 0"
    }
  }
  ```

### Custom Rules

- Create custom ESLint rules for project-specific patterns:
  ```javascript
  // eslint-rules/no-console-log.js
  module.exports = {
    create(context) {
      return {
        CallExpression(node) {
          if (
            node.callee.type === 'MemberExpression' &&
            node.callee.object.name === 'console' &&
            node.callee.property.name === 'log'
          ) {
            context.report({
              node,
              message: 'Unexpected console.log statement',
            });
          }
        },
      };
    },
  };
  ```

### Performance Rules

- **no-await-in-loop**: Avoid await inside loops:
  ```typescript
  // Avoid - Sequential execution
  for (const id of userIds) {
    await fetchUser(id);
  }
  
  // Good - Parallel execution
  const users = await Promise.all(userIds.map((id) => fetchUser(id)));
  ```

- **prefer-template**: Use template literals over string concatenation:
  ```typescript
  // Avoid
  const message = 'Hello, ' + name + '!';
  
  // Good
  const message = `Hello, ${name}!`;
  ```

### Security Rules

- **no-eval**: Never use eval():
  ```typescript
  // Avoid
  eval('console.log("Hello")');
  
  // Use safer alternatives
  const fn = new Function('return console.log("Hello")');
  ```

- **no-implied-eval**: Avoid setTimeout/setInterval with strings:
  ```typescript
  // Avoid
  setTimeout('alert("Hello")', 1000);
  
  // Good
  setTimeout(() => alert('Hello'), 1000);
  ```

### Best Practices

- Run ESLint on all TypeScript/JavaScript files.
- Configure auto-fix on save in your editor.
- Use extends to inherit recommended configurations.
- Disable rules only when necessary with explanations.
- Keep dependencies updated (ESLint, plugins, configs).
- Use lint-staged for pre-commit linting.
- Include linting in CI/CD pipelines.
- Fail builds on linting errors in production.
- Document custom rules and configurations.
- Integrate ESLint with Prettier for consistent formatting.
- Use TypeScript ESLint for TypeScript projects.
- Enable React and React Hooks plugins for React projects.
- Enable jsx-a11y plugin for accessibility checking.
- Organize imports consistently with import/order.
- Remove unused imports and variables.
- Use explicit return types for public functions.
- Handle promises properly (await or void).
- Follow React Hooks rules strictly.
- Provide keys for list items.
- Use semantic HTML and proper accessibility attributes.
