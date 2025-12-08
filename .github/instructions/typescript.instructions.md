---
title: TypeScript Copilot Instructions
applyTo: "**/*.ts,**/*.tsx"
---
# TypeScript Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate TypeScript 
code following best practices and modern TypeScript patterns.

This is an extension of the .github/copilot-instructions.md file.

## Coding Standards

### Naming Conventions

- Use PascalCase for type aliases, interfaces, classes, and enums (e.g., 
`UserProfile`, `ApiResponse`, `HttpClient`).
- Use camelCase for variables, functions, and methods (e.g., `userName`, 
`fetchData`, `calculateTotal`).
- Use UPPER_SNAKE_CASE for constants and enum values (e.g., `MAX_RETRY_COUNT`, 
`API_BASE_URL`).
- Prefix interface names with "I" only when necessary to distinguish from 
classes; generally avoid the prefix in modern TypeScript.
- Use descriptive names for generic type parameters: prefer `TData`, `TError`, 
`TContext` over single letters like `T`, `U`, `V` when the context is clear.
- Name type guard functions with "is" prefix (e.g., `isString`, `isUser`, 
`isApiError`).
- Suffix discriminated union types with their discriminant field (e.g., 
`type Shape = Circle | Square | Triangle`).

### Type Annotations

- Always use explicit return types for public functions and methods:
  ```typescript
  // Good
  export function calculateSum(a: number, b: number): number {
    return a + b;
  }
  
  // Avoid
  export function calculateSum(a: number, b: number) {
    return a + b;
  }
  ```
- Prefer type inference for local variables when the type is obvious from the 
initializer:
  ```typescript
  // Good
  const userName = 'John Doe';
  const count = users.length;
  
  // Avoid unnecessary annotations
  const userName: string = 'John Doe';
  ```
- Always annotate function parameters; never rely on implicit `any`:
  ```typescript
  // Good
  function greet(name: string): void {
    console.log(`Hello, ${name}`);
  }
  
  // Bad
  function greet(name) {
    console.log(`Hello, ${name}`);
  }
  ```
- Use type annotations for empty arrays and objects to prevent `any[]` or 
`{}`:
  ```typescript
  const users: User[] = [];
  const config: Record<string, string> = {};
  ```

### Interfaces vs Type Aliases

- Prefer interfaces for object shapes that may be extended or implemented:
  ```typescript
  interface User {
    id: string;
    name: string;
    email: string;
  }
  
  interface AdminUser extends User {
    permissions: string[];
  }
  ```
- Use type aliases for unions, intersections, primitives, and tuples:
  ```typescript
  type Status = 'pending' | 'approved' | 'rejected';
  type Point = [number, number];
  type ApiResponse<T> = { data: T } | { error: string };
  ```
- Use type aliases for mapped types and conditional types:
  ```typescript
  type Readonly<T> = {
    readonly [K in keyof T]: T[K];
  };
  
  type NonNullable<T> = T extends null | undefined ? never : T;
  ```
- For simple object types, prefer interfaces for better error messages and 
performance.

### Type Safety

- Enable strict mode in `tsconfig.json` and never disable strict checks:
  ```json
  {
    "compilerOptions": {
      "strict": true,
      "noUncheckedIndexedAccess": true,
      "noImplicitOverride": true,
      "noPropertyAccessFromIndexSignature": true
    }
  }
  ```
- Never use `any` type; use `unknown` if the type is truly dynamic and narrow 
it appropriately:
  ```typescript
  // Good
  function processValue(value: unknown): string {
    if (typeof value === 'string') {
      return value.toUpperCase();
    }
    return String(value);
  }
  
  // Avoid
  function processValue(value: any): string {
    return value.toUpperCase();
  }
  ```
- Avoid type assertions (`as`) unless absolutely necessary; prefer type guards:
  ```typescript
  // Good
  function isUser(obj: unknown): obj is User {
    return (
      typeof obj === 'object' &&
      obj !== null &&
      'id' in obj &&
      'name' in obj
    );
  }
  
  if (isUser(data)) {
    console.log(data.name);
  }
  
  // Avoid
  const user = data as User;
  ```
- Use const assertions for literal types and readonly arrays:
  ```typescript
  const config = {
    apiUrl: 'https://api.example.com',
    timeout: 5000,
  } as const;
  
  const colors = ['red', 'green', 'blue'] as const;
  type Color = typeof colors[number]; // 'red' | 'green' | 'blue'
  ```
- Prefer `strictNullChecks` to catch null and undefined errors at compile time.

### Generics

- Use generics to create reusable, type-safe functions and components:
  ```typescript
  function identity<T>(value: T): T {
    return value;
  }
  
  function mapArray<T, U>(arr: T[], fn: (item: T) => U): U[] {
    return arr.map(fn);
  }
  ```
- Add constraints to generics when necessary:
  ```typescript
  function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
  }
  
  function merge<T extends object, U extends object>(obj1: T, obj2: U): T & U {
    return { ...obj1, ...obj2 };
  }
  ```
- Use default type parameters when appropriate:
  ```typescript
  interface ApiResponse<T = unknown> {
    data: T;
    status: number;
  }
  ```
- Avoid over-constraining generics; keep them as general as possible while 
still being type-safe.

### Utility Types

- Leverage built-in utility types instead of creating custom ones:
  - `Partial<T>` - makes all properties optional
  - `Required<T>` - makes all properties required
  - `Readonly<T>` - makes all properties readonly
  - `Pick<T, K>` - picks specific properties
  - `Omit<T, K>` - omits specific properties
  - `Record<K, T>` - creates an object type with specific keys
  - `Exclude<T, U>` - excludes types from a union
  - `Extract<T, U>` - extracts types from a union
  - `NonNullable<T>` - removes null and undefined
  - `ReturnType<T>` - gets function return type
  - `Parameters<T>` - gets function parameter types
  ```typescript
  type UserUpdate = Partial<User>;
  type UserKeys = keyof User;
  type UserName = Pick<User, 'name' | 'email'>;
  type UserWithoutId = Omit<User, 'id'>;
  ```
- Create custom utility types for project-specific patterns:
  ```typescript
  type DeepPartial<T> = {
    [K in keyof T]?: T[K] extends object ? DeepPartial<T[K]> : T[K];
  };
  
  type Nullable<T> = T | null;
  type AsyncReturnType<T extends (...args: any) => Promise<any>> = 
    T extends (...args: any) => Promise<infer R> ? R : never;
  ```

### Enums vs Union Types

- Prefer const string unions over enums for better type safety and tree-
shaking:
  ```typescript
  // Good
  type Status = 'pending' | 'approved' | 'rejected';
  
  // Use enums only when you need reverse mapping or specific use cases
  enum Direction {
    Up = 'UP',
    Down = 'DOWN',
    Left = 'LEFT',
    Right = 'RIGHT',
  }
  ```
- Use const enums sparingly and only when performance is critical:
  ```typescript
  const enum LogLevel {
    Debug = 0,
    Info = 1,
    Warning = 2,
    Error = 3,
  }
  ```
- When using enums, always use string enums for better debugging:
  ```typescript
  enum HttpMethod {
    Get = 'GET',
    Post = 'POST',
    Put = 'PUT',
    Delete = 'DELETE',
  }
  ```

### Type Guards

- Always create type guards for runtime type checking:
  ```typescript
  function isString(value: unknown): value is string {
    return typeof value === 'string';
  }
  
  function isUser(obj: unknown): obj is User {
    return (
      typeof obj === 'object' &&
      obj !== null &&
      'id' in obj &&
      'name' in obj &&
      'email' in obj
    );
  }
  
  function isArrayOf<T>(
    arr: unknown,
    guard: (item: unknown) => item is T
  ): arr is T[] {
    return Array.isArray(arr) && arr.every(guard);
  }
  ```
- Use the `in` operator for discriminated unions:
  ```typescript
  type Success = { success: true; data: string };
  type Failure = { success: false; error: string };
  type Result = Success | Failure;
  
  function handleResult(result: Result): void {
    if ('data' in result) {
      console.log(result.data);
    } else {
      console.error(result.error);
    }
  }
  ```
- Prefer discriminated unions with a common property:
  ```typescript
  type Shape =
    | { kind: 'circle'; radius: number }
    | { kind: 'square'; sideLength: number }
    | { kind: 'rectangle'; width: number; height: number };
  
  function getArea(shape: Shape): number {
    switch (shape.kind) {
      case 'circle':
        return Math.PI * shape.radius ** 2;
      case 'square':
        return shape.sideLength ** 2;
      case 'rectangle':
        return shape.width * shape.height;
    }
  }
  ```

### Async/Await and Promises

- Always type Promise return values explicitly:
  ```typescript
  async function fetchUser(id: string): Promise<User> {
    const response = await fetch(`/api/users/${id}`);
    return response.json();
  }
  ```
- Use generic Promise types for better type inference:
  ```typescript
  function delay<T>(ms: number, value: T): Promise<T> {
    return new Promise((resolve) => setTimeout(() => resolve(value), ms));
  }
  ```
- Handle errors with proper typing:
  ```typescript
  async function fetchData(): Promise<Data> {
    try {
      const response = await fetch('/api/data');
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      return await response.json();
    } catch (error) {
      if (error instanceof Error) {
        console.error('Fetch error:', error.message);
      }
      throw error;
    }
  }
  ```

### Error Handling

- Create custom error types for better error handling:
  ```typescript
  class ApiError extends Error {
    constructor(
      message: string,
      public statusCode: number,
      public response?: unknown
    ) {
      super(message);
      this.name = 'ApiError';
    }
  }
  
  class ValidationError extends Error {
    constructor(message: string, public fields: Record<string, string>) {
      super(message);
      this.name = 'ValidationError';
    }
  }
  ```
- Use type guards for error handling:
  ```typescript
  function isApiError(error: unknown): error is ApiError {
    return error instanceof ApiError;
  }
  
  try {
    await fetchData();
  } catch (error) {
    if (isApiError(error)) {
      console.error(`API Error ${error.statusCode}: ${error.message}`);
    } else if (error instanceof Error) {
      console.error(error.message);
    } else {
      console.error('Unknown error:', error);
    }
  }
  ```
- Never use empty catch blocks; always handle or log errors:
  ```typescript
  // Good
  try {
    await riskyOperation();
  } catch (error) {
    console.error('Operation failed:', error);
    throw error; // Re-throw if needed
  }
  
  // Bad
  try {
    await riskyOperation();
  } catch {
    // Silent failure
  }
  ```

### Function Overloads

- Use function overloads for better type inference with multiple signatures:
  ```typescript
  function createElement(tag: 'div'): HTMLDivElement;
  function createElement(tag: 'span'): HTMLSpanElement;
  function createElement(tag: string): HTMLElement;
  function createElement(tag: string): HTMLElement {
    return document.createElement(tag);
  }
  
  const div = createElement('div'); // Type: HTMLDivElement
  ```
- Keep overload signatures before the implementation signature.
- Ensure the implementation signature is compatible with all overload 
signatures.

### Readonly and Immutability

- Use `readonly` modifier for properties that should not be modified:
  ```typescript
  interface Config {
    readonly apiUrl: string;
    readonly timeout: number;
  }
  
  class User {
    constructor(
      public readonly id: string,
      public name: string
    ) {}
  }
  ```
- Use `ReadonlyArray<T>` or `readonly T[]` for arrays that should not be 
modified:
  ```typescript
  function sum(numbers: readonly number[]): number {
    return numbers.reduce((acc, n) => acc + n, 0);
  }
  ```
- Use `Readonly<T>` utility type for readonly objects:
  ```typescript
  type ReadonlyUser = Readonly<User>;
  ```
- Prefer immutable data structures and operations:
  ```typescript
  // Good
  const newArray = [...oldArray, newItem];
  const newObject = { ...oldObject, newProp: value };
  
  // Avoid
  oldArray.push(newItem);
  oldObject.newProp = value;
  ```

### Index Signatures and Mapped Types

- Use index signatures for dynamic object keys:
  ```typescript
  interface StringMap {
    [key: string]: string;
  }
  
  interface NumberDictionary {
    [index: string]: number;
    length: number; // OK, length is a number
  }
  ```
- Prefer `Record<K, V>` utility type over index signatures:
  ```typescript
  type UserMap = Record<string, User>;
  ```
- Use mapped types for transforming object types:
  ```typescript
  type Optional<T> = {
    [K in keyof T]?: T[K];
  };
  
  type Getters<T> = {
    [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
  };
  ```
- Enable `noUncheckedIndexedAccess` to make index signatures return 
`T | undefined`:
  ```typescript
  const map: Record<string, string> = {};
  const value = map['key']; // Type: string | undefined
  ```

### Conditional Types

- Use conditional types for advanced type transformations:
  ```typescript
  type IsString<T> = T extends string ? true : false;
  type StringOrNumber<T> = T extends string ? string : number;
  
  type Flatten<T> = T extends Array<infer U> ? U : T;
  type ElementType = Flatten<string[]>; // string
  ```
- Leverage `infer` keyword for extracting types:
  ```typescript
  type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;
  type PromiseType<T> = T extends Promise<infer U> ? U : T;
  ```
- Use conditional types with mapped types:
  ```typescript
  type NonFunctionPropertyNames<T> = {
    [K in keyof T]: T[K] extends Function ? never : K;
  }[keyof T];
  
  type NonFunctionProperties<T> = Pick<T, NonFunctionPropertyNames<T>>;
  ```

### Template Literal Types

- Use template literal types for string manipulation:
  ```typescript
  type EventName<T extends string> = `on${Capitalize<T>}`;
  type ClickEvent = EventName<'click'>; // 'onClick'
  
  type HttpMethod = 'GET' | 'POST' | 'PUT' | 'DELETE';
  type ApiEndpoint<T extends string> = `/api/${T}`;
  type UserEndpoint = ApiEndpoint<'users'>; // '/api/users'
  ```
- Combine with mapped types for powerful transformations:
  ```typescript
  type Getters<T> = {
    [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
  };
  
  type Setters<T> = {
    [K in keyof T as `set${Capitalize<string & K>}`]: (value: T[K]) => void;
  };
  ```

### Type Narrowing

- Use control flow analysis for type narrowing:
  ```typescript
  function processValue(value: string | number): void {
    if (typeof value === 'string') {
      // value is string here
      console.log(value.toUpperCase());
    } else {
      // value is number here
      console.log(value.toFixed(2));
    }
  }
  ```
- Use truthiness narrowing carefully:
  ```typescript
  function printLength(str: string | null): void {
    if (str) {
      console.log(str.length);
    }
  }
  ```
- Use equality narrowing for literal types:
  ```typescript
  type Status = 'success' | 'error' | 'pending';
  
  function handleStatus(status: Status): void {
    if (status === 'success') {
      // status is 'success' here
    }
  }
  ```
- Use `instanceof` for class narrowing:
  ```typescript
  if (error instanceof ApiError) {
    console.log(error.statusCode);
  }
  ```

### Module Organization

- Use ES modules (`import`/`export`) instead of namespaces:
  ```typescript
  // Good
  export interface User {
    id: string;
    name: string;
  }
  
  export function getUser(id: string): User {
    // ...
  }
  
  // Avoid
  namespace UserModule {
    export interface User {
      id: string;
      name: string;
    }
  }
  ```
- Export types and values separately when needed:
  ```typescript
  export type { User, UserProfile } from './types';
  export { getUser, updateUser } from './api';
  ```
- Use barrel exports (`index.ts`) to simplify imports:
  ```typescript
  // types/index.ts
  export * from './user';
  export * from './product';
  export * from './order';
  ```
- Prefer named exports over default exports for better refactoring and 
tooling support:
  ```typescript
  // Good
  export function fetchUser(id: string): Promise<User> { }
  
  // Avoid
  export default function fetchUser(id: string): Promise<User> { }
  ```

### Declaration Merging

- Use declaration merging to extend interfaces:
  ```typescript
  interface User {
    id: string;
    name: string;
  }
  
  interface User {
    email: string;
  }
  
  // Merged: User has id, name, and email
  ```
- Use module augmentation to extend third-party types:
  ```typescript
  declare module 'express' {
    interface Request {
      user?: User;
    }
  }
  ```
- Avoid overusing declaration merging; prefer composition when possible.

### Type Assertions vs Type Casting

- Avoid using type assertions (`as`) when possible:
  ```typescript
  // Bad
  const input = document.getElementById('input') as HTMLInputElement;
  
  // Good
  const input = document.getElementById('input');
  if (input instanceof HTMLInputElement) {
    console.log(input.value);
  }
  ```
- Use non-null assertion operator (`!`) only when you're certain:
  ```typescript
  // Use sparingly
  const element = document.getElementById('root')!;
  
  // Better
  const element = document.getElementById('root');
  if (!element) throw new Error('Root element not found');
  ```
- Use `as const` for literal types and readonly assertions:
  ```typescript
  const config = {
    api: 'https://api.example.com',
    timeout: 5000,
  } as const;
  ```

### Comments and Documentation

- Use JSDoc comments for public APIs and exported functions:
  ```typescript
  /**
   * Fetches a user by their ID.
   * 
   * @param id - The unique identifier of the user
   * @returns A promise that resolves to the user object
   * @throws {ApiError} When the API request fails
   * 
   * @example
   * ```typescript
   * const user = await fetchUser('123');
   * console.log(user.name);
   * ```
   */
  export async function fetchUser(id: string): Promise<User> {
    // Implementation
  }
  ```
- Use `@deprecated` JSDoc tag for deprecated APIs:
  ```typescript
  /**
   * @deprecated Use `fetchUser` instead.
   */
  export function getUser(id: string): User {
    // Old implementation
  }
  ```
- Document complex types with JSDoc:
  ```typescript
  /**
   * Represents a user in the system.
   */
  export interface User {
    /** Unique identifier for the user */
    id: string;
    /** Full name of the user */
    name: string;
    /** Email address of the user */
    email: string;
  }
  ```

### Testing

- Use type-safe testing utilities and assertions:
  ```typescript
  import { expect, test } from 'vitest';
  
  test('fetchUser returns a user', async () => {
    const user = await fetchUser('123');
    expect(user).toMatchObject({
      id: expect.any(String),
      name: expect.any(String),
      email: expect.any(String),
    });
  });
  ```
- Create test-specific types when needed:
  ```typescript
  type MockUser = Pick<User, 'id' | 'name'>;
  
  function createMockUser(overrides?: Partial<MockUser>): MockUser {
    return {
      id: '123',
      name: 'Test User',
      ...overrides,
    };
  }
  ```
- Use type guards in tests to verify runtime types:
  ```typescript
  test('isUser type guard works correctly', () => {
    expect(isUser({ id: '1', name: 'John', email: 'john@example.com' })).toBe(true);
    expect(isUser({ id: '1' })).toBe(false);
    expect(isUser(null)).toBe(false);
  });
  ```

### Configuration

- Always use a `tsconfig.json` file with strict settings:
  ```json
  {
    "compilerOptions": {
      "target": "ES2022",
      "module": "ESNext",
      "lib": ["ES2022", "DOM", "DOM.Iterable"],
      "moduleResolution": "bundler",
      "strict": true,
      "noUncheckedIndexedAccess": true,
      "noImplicitOverride": true,
      "noPropertyAccessFromIndexSignature": true,
      "esModuleInterop": true,
      "skipLibCheck": true,
      "forceConsistentCasingInFileNames": true,
      "resolveJsonModule": true,
      "isolatedModules": true,
      "declaration": true,
      "declarationMap": true,
      "sourceMap": true,
      "outDir": "./dist",
      "rootDir": "./src"
    },
    "include": ["src/**/*"],
    "exclude": ["node_modules", "dist"]
  }
  ```
- Use project references for monorepos:
  ```json
  {
    "references": [
      { "path": "./packages/core" },
      { "path": "./packages/utils" }
    ]
  }
  ```

### Performance Considerations

- Avoid complex type computations that slow down the compiler.
- Use type aliases to cache complex type computations:
  ```typescript
  // Good
  type ComplexType = SomeComplexComputation<T>;
  type Result = ComplexType & OtherType;
  
  // Avoid recomputing the same type multiple times
  ```
- Use `skipLibCheck` in tsconfig.json to speed up compilation in large 
projects.
- Prefer interfaces over type intersections for object types (better 
performance).

### Best Practices Summary

- Always enable strict mode and recommended strict compiler options.
- Never use `any`; use `unknown` and narrow the type appropriately.
- Prefer interfaces for object shapes; use type aliases for unions and 
complex types.
- Use const assertions for literal types and readonly values.
- Create type guards for runtime type checking.
- Leverage utility types instead of creating custom ones when possible.
- Use generics for reusable, type-safe functions and components.
- Prefer discriminated unions over simple unions for better type narrowing.
- Document public APIs with JSDoc comments.
- Use explicit return types for public functions.
- Prefer readonly and immutable patterns.
- Use template literal types and mapped types for advanced type 
transformations.
- Organize code with ES modules and barrel exports.
- Test types and type guards to ensure runtime safety.
