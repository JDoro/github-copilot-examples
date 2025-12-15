---
title: React Testing Library Copilot Instructions
applyTo: "**/*.test.ts,**/*.test.tsx,**/*.spec.ts,**/*.spec.tsx"
---
# React Testing Library Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate tests for 
React components using React Testing Library and Vitest following best 
practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

## Coding Standards

### Naming Conventions

- Name test files with `.test.tsx` or `.spec.tsx` extension (e.g., 
`Button.test.tsx`, `UserForm.spec.tsx`).
- Use `describe` blocks to group related tests by component or feature.
- Use descriptive test names that explain the expected behavior (e.g., "should 
display error message when form validation fails").
- Prefix test utilities with "render" (e.g., `renderWithProviders`, 
`renderWithRouter`).
- Name custom queries with "find", "get", or "query" prefix based on React 
Testing Library conventions.
- Use "mock" prefix for mock functions and data (e.g., `mockUser`, 
`mockOnClick`, `mockApiResponse`).

### Testing Library Principles

- **Test user behavior, not implementation details**: Focus on what the user 
sees and does, not internal component state or methods.
- **Query by accessibility**: Prefer queries that reflect how users and 
assistive technologies interact with the page.
- **Avoid testing implementation details**: Don't test state, props, or 
internal methods directly.
- **Write tests that give you confidence**: Focus on critical user paths and 
edge cases.
- **Avoid shallow rendering**: Always render the full component tree to catch 
integration issues.

### Query Priority

Always follow this query priority order (from most to least preferred):

1. **getByRole** - Best for accessibility and reflects how users interact
   ```typescript
   screen.getByRole('button', { name: /submit/i });
   screen.getByRole('textbox', { name: /email/i });
   screen.getByRole('heading', { level: 1 });
   ```

2. **getByLabelText** - Good for form fields with labels
   ```typescript
   screen.getByLabelText(/email address/i);
   screen.getByLabelText(/password/i);
   ```

3. **getByPlaceholderText** - Use when labels are not available
   ```typescript
   screen.getByPlaceholderText(/enter your email/i);
   ```

4. **getByText** - Good for non-interactive elements
   ```typescript
   screen.getByText(/welcome back/i);
   screen.getByText(/error: invalid credentials/i);
   ```

5. **getByDisplayValue** - For form inputs with current values
   ```typescript
   screen.getByDisplayValue(/john doe/i);
   ```

6. **getByAltText** - For images with alt attributes
   ```typescript
   screen.getByAltText(/user avatar/i);
   ```

7. **getByTitle** - For elements with title attributes
   ```typescript
   screen.getByTitle(/close dialog/i);
   ```

8. **getByTestId** - Last resort only when other queries are not practical
   ```typescript
   screen.getByTestId('custom-element');
   ```

### Query Variants

- Use **getBy** for elements that should exist immediately:
  ```typescript
  const button = screen.getByRole('button', { name: /submit/i });
  expect(button).toBeInTheDocument();
  ```

- Use **queryBy** for elements that might not exist (for negative assertions):
  ```typescript
  expect(screen.queryByText(/loading/i)).not.toBeInTheDocument();
  ```

- Use **findBy** for elements that will appear asynchronously:
  ```typescript
  const message = await screen.findByText(/success/i);
  expect(message).toBeInTheDocument();
  ```

- Use **getAllBy**, **queryAllBy**, **findAllBy** for multiple elements:
  ```typescript
  const items = screen.getAllByRole('listitem');
  expect(items).toHaveLength(5);
  ```

### User Interactions

- **Always use `userEvent` over `fireEvent`** for more realistic user 
interactions:
  ```typescript
  import { userEvent } from '@testing-library/user-event';
  
  test('handles button click', async () => {
    const user = userEvent.setup();
    render(<Button onClick={mockOnClick}>Click me</Button>);
    
    await user.click(screen.getByRole('button', { name: /click me/i }));
    expect(mockOnClick).toHaveBeenCalledTimes(1);
  });
  ```

- Common `userEvent` actions:
  ```typescript
  // Clicking
  await user.click(element);
  await user.dblClick(element);
  await user.tripleClick(element);
  
  // Typing
  await user.type(input, 'Hello, World!');
  await user.clear(input);
  
  // Keyboard
  await user.keyboard('{Enter}');
  await user.keyboard('{Escape}');
  await user.tab();
  
  // Selection
  await user.selectOptions(select, 'option1');
  await user.deselectOptions(select, 'option1');
  
  // Upload
  await user.upload(fileInput, file);
  
  // Hover
  await user.hover(element);
  await user.unhover(element);
  ```

### Async Testing

- Use `waitFor` for assertions on elements that appear asynchronously:
  ```typescript
  await waitFor(() => {
    expect(screen.getByText(/success/i)).toBeInTheDocument();
  });
  ```

- Use `findBy` queries instead of `waitFor` + `getBy`:
  ```typescript
  // Good
  const element = await screen.findByText(/success/i);
  
  // Avoid
  await waitFor(() => {
    expect(screen.getByText(/success/i)).toBeInTheDocument();
  });
  ```

- Configure timeout for slow operations:
  ```typescript
  const element = await screen.findByText(/success/i, {}, { timeout: 3000 });
  
  await waitFor(() => {
    expect(mockFunction).toHaveBeenCalled();
  }, { timeout: 3000 });
  ```

- Use `waitForElementToBeRemoved` for elements that disappear:
  ```typescript
  await waitForElementToBeRemoved(() => screen.queryByText(/loading/i));
  ```

### Test Structure

- Organize tests with clear describe blocks and descriptive test names:
  ```typescript
  describe('LoginForm', () => {
    describe('Form Validation', () => {
      test('displays error when email is invalid', async () => {
        // Test implementation
      });
      
      test('displays error when password is too short', async () => {
        // Test implementation
      });
    });
    
    describe('Form Submission', () => {
      test('submits form with valid credentials', async () => {
        // Test implementation
      });
      
      test('handles submission errors gracefully', async () => {
        // Test implementation
      });
    });
  });
  ```

- Follow the Arrange-Act-Assert pattern:
  ```typescript
  test('increments counter when button is clicked', async () => {
    // Arrange
    const user = userEvent.setup();
    render(<Counter />);
    
    // Act
    await user.click(screen.getByRole('button', { name: /increment/i }));
    
    // Assert
    expect(screen.getByText(/count: 1/i)).toBeInTheDocument();
  });
  ```

### Custom Render Functions

- Create custom render functions for components that require providers:
  ```typescript
  import { render } from '@testing-library/react';
  import { ThemeProvider } from '@mui/material/styles';
  import { BrowserRouter } from 'react-router-dom';
  
  export function renderWithProviders(
    ui: React.ReactElement,
    options?: RenderOptions
  ) {
    return render(ui, {
      wrapper: ({ children }) => (
        <BrowserRouter>
          <ThemeProvider theme={theme}>
            {children}
          </ThemeProvider>
        </BrowserRouter>
      ),
      ...options,
    });
  }
  ```

- Use custom render in tests:
  ```typescript
  test('renders with theme and router', () => {
    renderWithProviders(<MyComponent />);
    expect(screen.getByText(/hello/i)).toBeInTheDocument();
  });
  ```

### Mocking

- Mock modules with Vitest:
  ```typescript
  import { vi } from 'vitest';
  
  vi.mock('axios');
  vi.mock('@/hooks/useAuth');
  ```

- Mock functions for callbacks and event handlers:
  ```typescript
  const mockOnClick = vi.fn();
  const mockOnSubmit = vi.fn();
  
  render(<Button onClick={mockOnClick}>Click</Button>);
  await user.click(screen.getByRole('button'));
  
  expect(mockOnClick).toHaveBeenCalledTimes(1);
  expect(mockOnClick).toHaveBeenCalledWith(expect.any(Object));
  ```

- Mock API calls:
  ```typescript
  import axios from 'axios';
  
  vi.mocked(axios.get).mockResolvedValue({
    data: { users: [{ id: 1, name: 'John' }] },
  });
  ```

- Mock custom hooks:
  ```typescript
  import { useAuth } from '@/hooks/useAuth';
  
  vi.mocked(useAuth).mockReturnValue({
    user: { id: 1, name: 'John' },
    isAuthenticated: true,
    login: vi.fn(),
    logout: vi.fn(),
  });
  ```

- Mock timers for time-dependent logic:
  ```typescript
  beforeEach(() => {
    vi.useFakeTimers();
  });
  
  afterEach(() => {
    vi.restoreAllMocks();
    vi.useRealTimers();
  });
  
  test('shows notification after delay', () => {
    render(<Notification />);
    
    vi.advanceTimersByTime(1000);
    
    expect(screen.getByText(/notification/i)).toBeInTheDocument();
  });
  ```

### Accessibility Testing

- Use `jest-axe` for automated accessibility testing:
  ```typescript
  import { axe, toHaveNoViolations } from 'jest-axe';
  
  expect.extend(toHaveNoViolations);
  
  test('has no accessibility violations', async () => {
    const { container } = render(<MyComponent />);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });
  ```

- Test keyboard navigation:
  ```typescript
  test('navigates menu with keyboard', async () => {
    const user = userEvent.setup();
    render(<Menu />);
    
    const firstItem = screen.getByRole('menuitem', { name: /first/i });
    firstItem.focus();
    
    await user.keyboard('{ArrowDown}');
    expect(screen.getByRole('menuitem', { name: /second/i })).toHaveFocus();
  });
  ```

- Test ARIA attributes:
  ```typescript
  test('has correct ARIA attributes', () => {
    render(<Dialog open />);
    
    const dialog = screen.getByRole('dialog');
    expect(dialog).toHaveAttribute('aria-modal', 'true');
    expect(dialog).toHaveAttribute('aria-labelledby');
  });
  ```

### Form Testing

- Test form validation:
  ```typescript
  test('validates email format', async () => {
    const user = userEvent.setup();
    render(<SignupForm />);
    
    const emailInput = screen.getByLabelText(/email/i);
    await user.type(emailInput, 'invalid-email');
    await user.tab();
    
    expect(await screen.findByText(/invalid email/i)).toBeInTheDocument();
  });
  ```

- Test form submission:
  ```typescript
  test('submits form with valid data', async () => {
    const user = userEvent.setup();
    const mockOnSubmit = vi.fn();
    render(<ContactForm onSubmit={mockOnSubmit} />);
    
    await user.type(screen.getByLabelText(/name/i), 'John Doe');
    await user.type(screen.getByLabelText(/email/i), 'john@example.com');
    await user.click(screen.getByRole('button', { name: /submit/i }));
    
    await waitFor(() => {
      expect(mockOnSubmit).toHaveBeenCalledWith({
        name: 'John Doe',
        email: 'john@example.com',
      });
    });
  });
  ```

### Component State Testing

- Test component state indirectly through the UI:
  ```typescript
  // Good - Testing state through user interactions
  test('toggles visibility when button is clicked', async () => {
    const user = userEvent.setup();
    render(<Collapsible />);
    
    expect(screen.queryByText(/content/i)).not.toBeInTheDocument();
    
    await user.click(screen.getByRole('button', { name: /show/i }));
    
    expect(screen.getByText(/content/i)).toBeInTheDocument();
  });
  
  // Avoid - Testing state directly
  test('updates state when clicked', () => {
    const { result } = renderHook(() => useState(false));
    // Don't do this
  });
  ```

### Snapshot Testing

- Use snapshot tests sparingly and purposefully:
  ```typescript
  test('matches snapshot', () => {
    const { container } = render(<Card title="Test" />);
    expect(container).toMatchSnapshot();
  });
  ```

- Prefer specific assertions over snapshots:
  ```typescript
  // Good
  test('renders title', () => {
    render(<Card title="Test Title" />);
    expect(screen.getByText(/test title/i)).toBeInTheDocument();
  });
  
  // Less preferred
  test('renders correctly', () => {
    const { container } = render(<Card title="Test Title" />);
    expect(container).toMatchSnapshot();
  });
  ```

### Error Handling

- Test error boundaries:
  ```typescript
  test('error boundary catches errors', () => {
    const consoleError = vi.spyOn(console, 'error').mockImplementation(() => {});
    
    render(
      <ErrorBoundary fallback={<div>Error occurred</div>}>
        <ComponentThatThrows />
      </ErrorBoundary>
    );
    
    expect(screen.getByText(/error occurred/i)).toBeInTheDocument();
    consoleError.mockRestore();
  });
  ```

- Test error states:
  ```typescript
  test('displays error message on API failure', async () => {
    vi.mocked(axios.get).mockRejectedValue(new Error('API Error'));
    
    render(<UserList />);
    
    expect(await screen.findByText(/failed to load/i)).toBeInTheDocument();
  });
  ```

### Performance Testing

- Avoid unnecessary re-renders in tests:
  ```typescript
  test('does not re-render unnecessarily', async () => {
    const renderCount = vi.fn();
    const Component = () => {
      renderCount();
      return <div>Content</div>;
    };
    
    const { rerender } = render(<Component />);
    expect(renderCount).toHaveBeenCalledTimes(1);
    
    rerender(<Component />);
    expect(renderCount).toHaveBeenCalledTimes(1); // Should not re-render
  });
  ```

### Test Organization

- Group tests logically:
  ```typescript
  describe('UserProfile', () => {
    describe('when user is logged in', () => {
      beforeEach(() => {
        vi.mocked(useAuth).mockReturnValue({
          user: mockUser,
          isAuthenticated: true,
        });
      });
      
      test('displays user information', () => {
        // Test
      });
      
      test('allows editing profile', async () => {
        // Test
      });
    });
    
    describe('when user is not logged in', () => {
      beforeEach(() => {
        vi.mocked(useAuth).mockReturnValue({
          user: null,
          isAuthenticated: false,
        });
      });
      
      test('redirects to login', () => {
        // Test
      });
    });
  });
  ```

### Cleanup

- Always clean up after tests:
  ```typescript
  afterEach(() => {
    vi.clearAllMocks();
    vi.restoreAllMocks();
  });
  ```

- Use `cleanup` for manual cleanup (automatic in most setups):
  ```typescript
  import { cleanup } from '@testing-library/react';
  
  afterEach(() => {
    cleanup();
  });
  ```

### Best Practices

- Test critical user flows and edge cases, not every component detail.
- Use semantic queries that reflect how users interact with the app.
- Avoid testing implementation details like state or props.
- Prefer integration tests over isolated unit tests for components.
- Mock external dependencies (APIs, third-party libraries).
- Keep tests fast by avoiding unnecessary delays or timeouts.
- Write descriptive test names that explain the expected behavior.
- Use custom render functions for components with providers.
- Test accessibility with `jest-axe` and keyboard navigation.
- Avoid snapshot tests for dynamic content; use them for static UI only.
- Clean up mocks and timers after each test.
- Test error states and loading states.
- Use `userEvent` for more realistic user interactions.
- Organize tests with clear describe blocks.
- Follow the Arrange-Act-Assert pattern for clarity.
