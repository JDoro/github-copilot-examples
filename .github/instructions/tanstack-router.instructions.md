---
title: TanStack Router Copilot Instructions
applyTo: "**/*.js,**/*.jsx,**/*.ts,**/*.tsx"
---
# TanStack Router Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate code that 
uses TanStack Router following best practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

## Coding Standards

### Naming Conventions

- Name route files using kebab-case (e.g., `about.tsx`, `user-profile.tsx`, 
`blog-post.tsx`).
- Use PascalCase for route component names (e.g., `AboutRoute`, 
`UserProfileRoute`, `BlogPostRoute`).
- Prefix layout components with "Layout" (e.g., `RootLayout`, 
`DashboardLayout`, `AuthLayout`).
- Name loader functions with the resource name followed by "Loader" (e.g., 
`userLoader`, `postsLoader`, `productLoader`).
- Use descriptive names for search param schemas that reflect their purpose 
(e.g., `userSearchSchema`, `productsFilterSchema`).
- Name error components with "Error" suffix (e.g., `RouteError`, 
`NotFoundError`, `GenericError`).
- Use "Pending" suffix for pending/loading components (e.g., `RoutePending`, 
`DataPending`).

### File-Based Routing Structure

- Use file-based routing with the `routes` directory at the project root or 
within `src`.
- Follow the flat file structure convention: `routes/about.tsx`, 
`routes/posts.$postId.tsx`.
- Use underscore prefix for pathless layout routes (e.g., `_layout.tsx`, 
`_auth.tsx`).
- Use dollar sign for dynamic route parameters (e.g., `$userId.tsx`, 
`posts.$postId.tsx`).
- Use parentheses for route groups that don't affect the URL (e.g., 
`(dashboard)/settings.tsx`).
- Place the root route in `__root.tsx` at the routes directory root.
- Keep route files focused on route configuration; extract complex components 
to separate files.
- Organize related routes in subdirectories when they share common layouts or 
functionality.

### Route Configuration

- Always define routes using the `createFileRoute` or `createRootRoute` 
functions.
- Export route configuration as the default export from route files.
- Define route options (loader, component, errorComponent, pendingComponent) 
within the route configuration.
- Use `beforeLoad` for authentication checks and redirects.
- Implement `validateSearch` with Zod or similar schema validation for type-
safe search parameters.
- Set appropriate `staleTime` for loaders based on data volatility.
- Use `gcTime` (formerly `cacheTime`) to control how long inactive loader 
data stays in memory.
- Configure `shouldReload` to control when loaders re-execute on navigation.

Example:
```typescript
export const Route = createFileRoute('/posts/$postId')({
  component: PostRoute,
  loader: async ({ params }) => fetchPost(params.postId),
  errorComponent: PostError,
  pendingComponent: PostPending,
  validateSearch: (search) => postSearchSchema.parse(search),
  beforeLoad: async ({ context }) => {
    if (!context.user) {
      throw redirect({ to: '/login' });
    }
  },
});
```

### Type Safety

- Always use TypeScript for route definitions and loader functions.
- Define strict types for route parameters using generics.
- Create type-safe search parameter schemas using Zod or similar validation 
libraries.
- Use the router's type inference to ensure type safety across the 
application.
- Define context types for route-level context data.
- Use `RouteContext` generic to type the route context properly.
- Leverage `inferLoaderData` utility type to infer loader return types.
- Never use `any` type for route parameters, search params, or loader data.

Example:
```typescript
import { z } from 'zod';

const postSearchSchema = z.object({
  page: z.number().optional().default(1),
  sort: z.enum(['recent', 'popular']).optional().default('recent'),
});

type PostSearch = z.infer<typeof postSearchSchema>;
```

### Data Loading

- Always use loader functions for data fetching rather than `useEffect` in 
components.
- Keep loader functions async and return serializable data.
- Throw errors from loaders to trigger error boundaries.
- Use loader context to access shared data like authentication state.
- Leverage loader caching to prevent redundant data fetching.
- Implement proper error handling in loaders with meaningful error messages.
- Use `defer` for streaming data when appropriate to improve perceived 
performance.
- Preload data on link hover using `preload="intent"` for better UX.
- Access loader data in components using `Route.useLoaderData()`.

Example:
```typescript
loader: async ({ params, context, abortController }) => {
  const data = await fetchData(params.id, {
    signal: abortController.signal,
  });
  
  if (!data) {
    throw new Error('Data not found');
  }
  
  return data;
}
```

### Navigation and Linking

- Use `<Link>` component for internal navigation instead of `<a>` tags.
- Leverage type-safe navigation with `to` prop using route paths.
- Pass search params and hash using the `search` and `hash` props.
- Use `params` prop for dynamic route parameters in links.
- Implement active link styling using `activeProps` and `inactiveProps`.
- Use `useNavigate` hook for programmatic navigation.
- Use `navigate` function returned from `useNavigate` with type-safe options.
- Implement `preload` prop on links to prefetch data on hover or mount.
- Use `resetScroll` option to control scroll behavior on navigation.

Example:
```typescript
<Link
  to="/posts/$postId"
  params={{ postId: '123' }}
  search={{ page: 1 }}
  activeProps={{ className: 'active-link' }}
  preload="intent"
>
  View Post
</Link>
```

### Search Parameters

- Always validate search parameters using schema validation (Zod, Yup, etc.).
- Define search param schemas at the route level using `validateSearch`.
- Use `useSearch` hook to access type-safe search parameters in components.
- Update search params using `useNavigate` with the `search` option.
- Implement search param defaults in the validation schema.
- Keep search params serializable (strings, numbers, booleans, arrays).
- Use search params for filters, pagination, and UI state that should be 
shareable via URL.
- Avoid storing complex objects or functions in search parameters.

### Error Handling

- Always implement `errorComponent` for routes to handle loader and component 
errors.
- Use `useRouteError` hook in error components to access error information.
- Create reusable error components for common error scenarios (404, 403, 500).
- Implement proper error boundaries at appropriate levels (route, layout, 
root).
- Display user-friendly error messages while logging technical details.
- Use `isNotFound` utility to check for 404 errors specifically.
- Implement retry mechanisms for transient errors when appropriate.
- Throw `notFound()` from loaders for missing resources to trigger 404 
handling.

Example:
```typescript
function RouteError() {
  const error = useRouteError();
  
  if (isNotFound(error)) {
    return <NotFound />;
  }
  
  return (
    <div>
      <h1>Error</h1>
      <p>{error.message}</p>
    </div>
  );
}
```

### Pending States

- Implement `pendingComponent` for routes to show loading states during 
navigation.
- Use `useNavigation` hook to access navigation state and show global loading 
indicators.
- Display skeleton screens or loading spinners in pending components.
- Keep pending components lightweight to render quickly.
- Use `pendingMs` and `pendingMinMs` options to control when pending UI shows.
- Implement optimistic UI updates for better perceived performance.
- Use `Outlet` with proper loading states for nested route transitions.

### Layouts and Nested Routes

- Use layout routes (pathless routes with `_` prefix) for shared UI across 
multiple routes.
- Implement the `<Outlet />` component in layouts to render child routes.
- Keep layout components focused on shared UI concerns (navigation, sidebars, 
footers).
- Pass layout-specific context down through route context.
- Use nested routes for hierarchical UI structures.
- Implement proper error boundaries at layout levels.
- Use layout routes to avoid repeating route configuration.

Example:
```typescript
// _layout.tsx
export const Route = createFileRoute('/_layout')({
  component: LayoutComponent,
});

function LayoutComponent() {
  return (
    <div>
      <nav>Navigation</nav>
      <Outlet />
      <footer>Footer</footer>
    </div>
  );
}
```

### Route Guards and Authentication

- Use `beforeLoad` hook for authentication checks and redirects.
- Implement route-level authorization by checking user permissions in 
`beforeLoad`.
- Throw `redirect()` to navigate users to login or unauthorized pages.
- Store user authentication state in router context for access in all routes.
- Use context to share authentication data between routes and components.
- Implement proper session management and token refresh logic.
- Avoid client-side only authentication; validate on the server when possible.

Example:
```typescript
beforeLoad: async ({ context, location }) => {
  if (!context.auth.isAuthenticated) {
    throw redirect({
      to: '/login',
      search: {
        redirect: location.href,
      },
    });
  }
}
```

### Code Splitting and Lazy Loading

- Use `lazyRouteComponent` for code splitting route components.
- Lazy load large components that aren't needed immediately.
- Split routes into separate chunks to reduce initial bundle size.
- Use dynamic imports for heavy dependencies or conditional features.
- Implement proper loading states while lazy components load.
- Prefetch critical routes on app initialization for better UX.
- Use `pendingComponent` to show loading UI during lazy component loading.

Example:
```typescript
export const Route = createFileRoute('/dashboard')({
  component: lazyRouteComponent(() => import('./Dashboard')),
  pendingComponent: DashboardPending,
});
```

### Router Configuration

- Create a single router instance using `createRouter` with route tree.
- Configure router options (default preload, default error component, etc.) 
at router creation.
- Use `RouterProvider` to provide the router to the React application.
- Set up router context with shared data (auth, theme, etc.).
- Enable strict mode for better error detection during development.
- Configure `defaultPreload` to control link preloading behavior globally.
- Set appropriate `defaultPendingMs` and `defaultPendingMinMs` for loading 
states.

Example:
```typescript
const router = createRouter({
  routeTree,
  context: {
    auth: undefined!,
  },
  defaultPreload: 'intent',
  defaultPendingMs: 1000,
  defaultPendingMinMs: 500,
});
```

### Testing

- Test route components in isolation using testing libraries like `@testing-
library/react`.
- Mock loader functions and route context in tests.
- Test navigation flows and route transitions.
- Verify error handling and error components render correctly.
- Test search parameter validation and updates.
- Use `createMemoryHistory` for testing navigation without browser 
dependencies.
- Test route guards and authentication flows.
- Verify pending states and loading indicators work as expected.

### Performance Optimization

- Use `staleTime` to prevent unnecessary loader re-executions.
- Implement proper code splitting to reduce initial bundle size.
- Leverage link preloading to prefetch data before navigation.
- Use `defer` for streaming non-critical data.
- Implement virtualization for long lists in route components.
- Avoid expensive computations in render; move to loaders when possible.
- Use React.memo and useMemo appropriately for expensive components.
- Monitor bundle size and lazy load routes that aren't frequently accessed.

### Code Organization

- Keep route files in a dedicated `routes` directory.
- Separate route configuration from component implementation for complex 
routes.
- Group related utilities (loaders, validators, types) near their routes.
- Create shared route utilities in a common directory (e.g., `lib/routing`).
- Export route-specific types from route files for use in components.
- Keep components that are only used in one route co-located with that route.
- Use barrel exports (index files) for cleaner imports of route-related code.

### Best Practices

- Never navigate using `window.location` or `history.pushState`; use router 
navigation.
- Avoid storing router state in external state management; use router's built-
in state.
- Use search params for shareable UI state; use local state for ephemeral UI 
state.
- Implement proper loading and error states for all routes.
- Keep loaders focused and single-purpose; compose multiple loaders if 
needed.
- Use type-safe routing throughout the application for better DX and fewer 
bugs.
- Implement proper SEO metadata in route components (title, meta tags).
- Use meaningful route paths that reflect the content and hierarchy.
- Document complex routing logic and route configuration with comments.
- Validate and sanitize route parameters and search params to prevent 
security issues.
- Use the router's built-in caching; avoid implementing separate caching 
layers.
- Prefer declarative routing configuration over imperative navigation logic.
