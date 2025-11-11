---
title: TanStack Query Copilot Instructions
applyTo: "**/*.js,**/*.jsx,**/*.ts,**/*.tsx"
---
# TanStack Query Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate code that 
uses TanStack Query (formerly React Query) following best practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

## Coding Standards

### Naming Conventions

- Prefix query hooks with "use" followed by descriptive action (e.g., 
`useUserData`, `useProducts`, `usePostComments`).
- Use descriptive query keys that reflect the data structure (e.g., 
`['users', userId]`, `['posts', 'list', filters]`).
- Name mutation hooks with "use" prefix and action verb (e.g., `useCreateUser`, 
`useUpdatePost`, `useDeleteComment`).
- Use camelCase for query and mutation function names (e.g., `fetchUserData`, 
`createPost`, `updateProfile`).
- Name query key factory functions with the resource name followed by "Keys" 
(e.g., `userKeys`, `postKeys`, `productKeys`).

### Query Keys

- Always use array format for query keys to enable hierarchical invalidation.
- Structure query keys from general to specific: `[resource, scope, ...params]`.
- Create query key factories for related queries to ensure consistency:
  ```typescript
  const userKeys = {
    all: ['users'] as const,
    lists: () => [...userKeys.all, 'list'] as const,
    list: (filters: string) => [...userKeys.lists(), { filters }] as const,
    details: () => [...userKeys.all, 'detail'] as const,
    detail: (id: number) => [...userKeys.details(), id] as const,
  };
  ```
- Never use string-only keys; always use arrays for better type safety and 
invalidation control.
- Keep query keys serializable (avoid functions, class instances, or 
non-serializable objects).

### Query Hooks

- Always specify explicit return types for custom query hooks.
- Use `queryFn` to keep the query function close to the hook definition.
- Enable `staleTime` appropriately based on data volatility (e.g., user 
profiles can have longer stale time than real-time data).
- Set `gcTime` (formerly `cacheTime`) based on how long you want to keep 
unused data in cache.
- Use `enabled` option to conditionally execute queries based on dependencies.
- Leverage `select` option to transform or derive data from queries rather 
than transforming in components.
- Always handle loading and error states appropriately in components using 
the query.
- Use `placeholderData` or `initialData` when appropriate to improve perceived 
performance.
- Never call hooks conditionally; use the `enabled` option instead.

### Mutation Hooks

- Always use `useMutation` for operations that create, update, or delete data.
- Implement optimistic updates for better user experience when appropriate.
- Use `onSuccess`, `onError`, and `onSettled` callbacks for side effects.
- Invalidate or update related queries after successful mutations using 
`queryClient.invalidateQueries()` or `queryClient.setQueryData()`.
- Handle errors gracefully with proper error messages and rollback logic for 
optimistic updates.
- Use `mutateAsync` only when you need promise-based control flow; otherwise 
prefer `mutate`.
- Group related invalidations together for efficiency.

### Query Client Configuration

- Create a single QueryClient instance and provide it through 
`QueryClientProvider` at the app root.
- Configure default options in the QueryClient constructor:
  ```typescript
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: {
        staleTime: 1000 * 60 * 5, // 5 minutes
        gcTime: 1000 * 60 * 10, // 10 minutes
        retry: 1,
        refetchOnWindowFocus: false,
      },
    },
  });
  ```
- Override defaults per-query only when necessary.
- Enable React Query DevTools in development for debugging.

### Data Fetching Functions

- Keep data fetching functions separate from hooks for better testability and 
reusability.
- Always throw errors in fetch functions rather than returning error objects; 
TanStack Query handles errors through its error state.
- Use proper TypeScript types for request parameters and response data.
- Create an API client module to centralize fetch logic and configuration.
- Include proper error handling and transformation in fetch functions.

### Infinite Queries

- Use `useInfiniteQuery` for paginated or infinite scroll data.
- Always provide `getNextPageParam` and `getPreviousPageParam` functions.
- Use `hasNextPage` and `fetchNextPage` to implement load more functionality.
- Structure data to work efficiently with `pages` array structure.
- Consider using `select` to flatten paginated data if needed by components.

### Dependent Queries

- Use the `enabled` option to create dependent queries that wait for required 
data.
- Pass data from one query to another through the `enabled` and `queryFn` 
parameters.
- Avoid running queries when their dependencies are not yet available.

### Prefetching

- Use `queryClient.prefetchQuery()` to load data before it's needed (e.g., on 
hover, route transitions).
- Prefetch next pages in infinite queries to improve perceived performance.
- Use `queryClient.setQueryData()` to seed cache with data from other sources 
(e.g., list to detail transitions).

### Error Handling

- Always provide meaningful error types and messages from API functions.
- Use error boundaries or global error handlers for critical query failures.
- Implement retry logic appropriately based on error types (avoid retrying on 
4xx errors).
- Display user-friendly error messages in the UI based on query error states.
- Log errors appropriately for debugging and monitoring.

### Performance Optimization

- Use `select` option to subscribe only to specific parts of query data to 
minimize re-renders.
- Implement proper TypeScript types to catch errors at compile time.
- Use structural sharing (enabled by default) to prevent unnecessary re-renders.
- Avoid over-fetching by keeping query functions focused and specific.
- Use `refetchOnMount`, `refetchOnWindowFocus`, and `refetchOnReconnect` 
judiciously based on data requirements.
- Consider using `keepPreviousData` (now `placeholderData: 
keepPreviousData`) for paginated queries to prevent loading states between 
pages.

### Code Organization

- Group related query hooks in dedicated files (e.g., `useUserQueries.ts`, 
`useProductQueries.ts`).
- Keep query key factories, hooks, and fetch functions co-located for the 
same resource.
- Export query hooks from a central queries/hooks directory for easy imports.
- Separate query logic from component logic for better separation of concerns.

### Testing

- Test query hooks in isolation using testing libraries like 
`@testing-library/react`.
- Mock API responses rather than the query hooks themselves.
- Use `QueryClientProvider` with a new `QueryClient` instance for each test.
- Test loading, success, and error states for queries.
- Verify that mutations trigger appropriate invalidations and updates.

### Type Safety

- Always define TypeScript interfaces or types for query data and errors.
- Use generics properly with `useQuery<TData, TError>` and 
`useMutation<TData, TError, TVariables>`.
- Avoid using `any` type; use `unknown` if the type is truly dynamic and 
narrow it appropriately.
- Define separate types for API responses and application data models when 
transformation is needed.

### Best Practices

- Never fetch data in `useEffect`; use `useQuery` instead.
- Avoid storing server state in local state (useState); let TanStack Query 
manage it.
- Use background refetching to keep data fresh without showing loading states.
- Implement proper loading skeletons or placeholders during initial data fetch.
- Use suspense mode for queries when using React Suspense (via `suspense: 
true` option or `useSuspenseQuery`).
- Keep mutations as close to the user action as possible for better UX.
- Document complex query configurations with comments explaining the rationale.
- Avoid premature optimization; start with sensible defaults and optimize 
based on actual performance metrics.
