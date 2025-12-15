---
title: Zustand Copilot Instructions
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
---
# Zustand Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate state 
management code using Zustand following best practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

## Coding Standards

### Naming Conventions

- Name store files with "store" or "Store" suffix (e.g., `userStore.ts`, 
`authStore.ts`, `cartStore.ts`).
- Name store hooks with "use" prefix followed by store name (e.g., `useUserStore`, 
`useAuthStore`, `useCartStore`).
- Name store slices with descriptive names (e.g., `createUserSlice`, 
`createCartSlice`).
- Name actions as verbs (e.g., `setUser`, `addItem`, `removeItem`, `resetCart`).
- Name selectors as nouns or questions (e.g., `user`, `items`, `isAuthenticated`, 
`getTotalPrice`).

### Store Creation

- Create stores using the `create` function:
  ```typescript
  import { create } from 'zustand';
  
  type UserStore = {
    user: User | null;
    setUser: (user: User) => void;
    clearUser: () => void;
  };
  
  export const useUserStore = create<UserStore>((set) => ({
    user: null,
    setUser: (user) => set({ user }),
    clearUser: () => set({ user: null }),
  }));
  ```

- Use the `set` function to update state:
  ```typescript
  export const useCounterStore = create<CounterStore>((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
    decrement: () => set((state) => ({ count: state.count - 1 })),
    reset: () => set({ count: 0 }),
  }));
  ```

- Use the `get` function to access current state in actions:
  ```typescript
  export const useCartStore = create<CartStore>((set, get) => ({
    items: [],
    addItem: (item) => {
      const currentItems = get().items;
      const existingItem = currentItems.find((i) => i.id === item.id);
      
      if (existingItem) {
        set({
          items: currentItems.map((i) =>
            i.id === item.id ? { ...i, quantity: i.quantity + 1 } : i
          ),
        });
      } else {
        set({ items: [...currentItems, { ...item, quantity: 1 }] });
      }
    },
    removeItem: (id) => {
      set({ items: get().items.filter((item) => item.id !== id) });
    },
    clearCart: () => set({ items: [] }),
  }));
  ```

### TypeScript Typing

- Always define TypeScript types for stores:
  ```typescript
  type AuthStore = {
    user: User | null;
    token: string | null;
    isAuthenticated: boolean;
    login: (email: string, password: string) => Promise<void>;
    logout: () => void;
    refreshToken: () => Promise<void>;
  };
  
  export const useAuthStore = create<AuthStore>((set, get) => ({
    user: null,
    token: null,
    isAuthenticated: false,
    login: async (email, password) => {
      const { user, token } = await loginApi(email, password);
      set({ user, token, isAuthenticated: true });
    },
    logout: () => {
      set({ user: null, token: null, isAuthenticated: false });
    },
    refreshToken: async () => {
      const token = await refreshTokenApi();
      set({ token });
    },
  }));
  ```

- Use separate types for state and actions:
  ```typescript
  type TodoState = {
    todos: Todo[];
    filter: 'all' | 'active' | 'completed';
  };
  
  type TodoActions = {
    addTodo: (text: string) => void;
    toggleTodo: (id: string) => void;
    removeTodo: (id: string) => void;
    setFilter: (filter: TodoState['filter']) => void;
  };
  
  type TodoStore = TodoState & TodoActions;
  
  export const useTodoStore = create<TodoStore>((set) => ({
    todos: [],
    filter: 'all',
    addTodo: (text) =>
      set((state) => ({
        todos: [...state.todos, { id: generateId(), text, completed: false }],
      })),
    toggleTodo: (id) =>
      set((state) => ({
        todos: state.todos.map((todo) =>
          todo.id === id ? { ...todo, completed: !todo.completed } : todo
        ),
      })),
    removeTodo: (id) =>
      set((state) => ({
        todos: state.todos.filter((todo) => todo.id !== id),
      })),
    setFilter: (filter) => set({ filter }),
  }));
  ```

### Using Stores in Components

- Access store state and actions:
  ```typescript
  function UserProfile() {
    const user = useUserStore((state) => state.user);
    const setUser = useUserStore((state) => state.setUser);
    
    return (
      <div>
        <h1>{user?.name}</h1>
        <button onClick={() => setUser(newUser)}>Update</button>
      </div>
    );
  }
  ```

- Use selectors to prevent unnecessary re-renders:
  ```typescript
  // Good - Only re-renders when user.name changes
  const userName = useUserStore((state) => state.user?.name);
  
  // Avoid - Re-renders on any state change
  const { user } = useUserStore();
  ```

- Access multiple values efficiently:
  ```typescript
  function Cart() {
    const { items, addItem, removeItem } = useCartStore((state) => ({
      items: state.items,
      addItem: state.addItem,
      removeItem: state.removeItem,
    }));
    
    return (
      <div>
        {items.map((item) => (
          <div key={item.id}>
            {item.name}
            <button onClick={() => removeItem(item.id)}>Remove</button>
          </div>
        ))}
      </div>
    );
  }
  ```

### Selectors

- Create reusable selectors:
  ```typescript
  // In store file
  export const selectUser = (state: UserStore) => state.user;
  export const selectIsAuthenticated = (state: AuthStore) => state.isAuthenticated;
  
  // In component
  const user = useUserStore(selectUser);
  const isAuthenticated = useAuthStore(selectIsAuthenticated);
  ```

- Create computed selectors:
  ```typescript
  export const selectTotalPrice = (state: CartStore) =>
    state.items.reduce((total, item) => total + item.price * item.quantity, 0);
  
  export const selectItemCount = (state: CartStore) =>
    state.items.reduce((count, item) => count + item.quantity, 0);
  
  // In component
  const totalPrice = useCartStore(selectTotalPrice);
  const itemCount = useCartStore(selectItemCount);
  ```

- Use shallow equality for object selectors:
  ```typescript
  import { shallow } from 'zustand/shallow';
  
  const { items, addItem } = useCartStore(
    (state) => ({ items: state.items, addItem: state.addItem }),
    shallow
  );
  ```

### Middleware

- Use the `persist` middleware for persistence:
  ```typescript
  import { create } from 'zustand';
  import { persist } from 'zustand/middleware';
  
  export const useAuthStore = create<AuthStore>()(
    persist(
      (set) => ({
        user: null,
        token: null,
        login: async (email, password) => {
          const { user, token } = await loginApi(email, password);
          set({ user, token });
        },
        logout: () => set({ user: null, token: null }),
      }),
      {
        name: 'auth-storage',
        partialize: (state) => ({ user: state.user, token: state.token }),
      }
    )
  );
  ```

- Use the `devtools` middleware for debugging:
  ```typescript
  import { devtools } from 'zustand/middleware';
  
  export const useUserStore = create<UserStore>()(
    devtools(
      (set) => ({
        user: null,
        setUser: (user) => set({ user }, false, 'setUser'),
        clearUser: () => set({ user: null }, false, 'clearUser'),
      }),
      { name: 'UserStore' }
    )
  );
  ```

- Combine multiple middleware:
  ```typescript
  import { create } from 'zustand';
  import { devtools, persist } from 'zustand/middleware';
  
  export const useCartStore = create<CartStore>()(
    devtools(
      persist(
        (set) => ({
          items: [],
          addItem: (item) => set((state) => ({ items: [...state.items, item] })),
        }),
        { name: 'cart-storage' }
      ),
      { name: 'CartStore' }
    )
  );
  ```

- Use the `immer` middleware for immutable updates:
  ```typescript
  import { immer } from 'zustand/middleware/immer';
  
  export const useTodoStore = create<TodoStore>()(
    immer((set) => ({
      todos: [],
      addTodo: (text) =>
        set((state) => {
          state.todos.push({ id: generateId(), text, completed: false });
        }),
      toggleTodo: (id) =>
        set((state) => {
          const todo = state.todos.find((t) => t.id === id);
          if (todo) {
            todo.completed = !todo.completed;
          }
        }),
    }))
  );
  ```

### Async Actions

- Handle async operations in actions:
  ```typescript
  type UserStore = {
    users: User[];
    isLoading: boolean;
    error: string | null;
    fetchUsers: () => Promise<void>;
  };
  
  export const useUserStore = create<UserStore>((set) => ({
    users: [],
    isLoading: false,
    error: null,
    fetchUsers: async () => {
      set({ isLoading: true, error: null });
      try {
        const users = await fetchUsersApi();
        set({ users, isLoading: false });
      } catch (error) {
        set({
          error: error instanceof Error ? error.message : 'Unknown error',
          isLoading: false,
        });
      }
    },
  }));
  ```

- Use async actions in components:
  ```typescript
  function UserList() {
    const { users, isLoading, error, fetchUsers } = useUserStore();
    
    useEffect(() => {
      fetchUsers();
    }, [fetchUsers]);
    
    if (isLoading) return <div>Loading...</div>;
    if (error) return <div>Error: {error}</div>;
    
    return (
      <div>
        {users.map((user) => (
          <div key={user.id}>{user.name}</div>
        ))}
      </div>
    );
  }
  ```

### Slice Pattern

- Split large stores into slices:
  ```typescript
  type UserSlice = {
    user: User | null;
    setUser: (user: User) => void;
  };
  
  type SettingsSlice = {
    theme: 'light' | 'dark';
    language: string;
    setTheme: (theme: 'light' | 'dark') => void;
    setLanguage: (language: string) => void;
  };
  
  const createUserSlice: StateCreator<UserSlice & SettingsSlice, [], [], UserSlice> = (
    set
  ) => ({
    user: null,
    setUser: (user) => set({ user }),
  });
  
  const createSettingsSlice: StateCreator<
    UserSlice & SettingsSlice,
    [],
    [],
    SettingsSlice
  > = (set) => ({
    theme: 'light',
    language: 'en',
    setTheme: (theme) => set({ theme }),
    setLanguage: (language) => set({ language }),
  });
  
  export const useAppStore = create<UserSlice & SettingsSlice>()((...a) => ({
    ...createUserSlice(...a),
    ...createSettingsSlice(...a),
  }));
  ```

### Resetting State

- Implement reset functionality:
  ```typescript
  type Store = {
    count: number;
    user: User | null;
    increment: () => void;
    setUser: (user: User) => void;
    reset: () => void;
  };
  
  const initialState = {
    count: 0,
    user: null,
  };
  
  export const useStore = create<Store>((set) => ({
    ...initialState,
    increment: () => set((state) => ({ count: state.count + 1 })),
    setUser: (user) => set({ user }),
    reset: () => set(initialState),
  }));
  ```

### Subscriptions

- Subscribe to store changes outside components:
  ```typescript
  const unsubscribe = useUserStore.subscribe(
    (state) => state.user,
    (user) => {
      console.log('User changed:', user);
    }
  );
  
  // Cleanup
  unsubscribe();
  ```

- Subscribe to entire store:
  ```typescript
  const unsubscribe = useUserStore.subscribe((state) => {
    console.log('State changed:', state);
  });
  ```

### Accessing Store Outside Components

- Get store state outside React components:
  ```typescript
  // Get current state
  const currentUser = useUserStore.getState().user;
  
  // Update state
  useUserStore.getState().setUser(newUser);
  ```

- Use in utility functions:
  ```typescript
  export async function fetchProtectedData() {
    const token = useAuthStore.getState().token;
    return axios.get('/api/data', {
      headers: { Authorization: `Bearer ${token}` },
    });
  }
  ```

### Testing

- Test store actions:
  ```typescript
  import { renderHook, act } from '@testing-library/react';
  
  test('increments counter', () => {
    const { result } = renderHook(() => useCounterStore());
    
    expect(result.current.count).toBe(0);
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
  ```

- Reset store between tests:
  ```typescript
  beforeEach(() => {
    useCounterStore.setState({ count: 0 });
  });
  ```

- Mock stores in component tests:
  ```typescript
  import { render, screen } from '@testing-library/react';
  
  vi.mock('@/stores/userStore', () => ({
    useUserStore: vi.fn(() => ({
      user: { id: '1', name: 'John Doe' },
      setUser: vi.fn(),
    })),
  }));
  
  test('displays user name', () => {
    render(<UserProfile />);
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  });
  ```

### Performance Optimization

- Use selective subscriptions to prevent unnecessary re-renders:
  ```typescript
  // Good - Only re-renders when userName changes
  const userName = useUserStore((state) => state.user?.name);
  
  // Avoid - Re-renders on any state change
  const state = useUserStore();
  const userName = state.user?.name;
  ```

- Use `shallow` for object selectors:
  ```typescript
  import { shallow } from 'zustand/shallow';
  
  const { user, settings } = useAppStore(
    (state) => ({ user: state.user, settings: state.settings }),
    shallow
  );
  ```

- Memoize computed values:
  ```typescript
  const filteredTodos = useUserStore((state) => {
    return state.todos.filter((todo) => {
      if (state.filter === 'active') return !todo.completed;
      if (state.filter === 'completed') return todo.completed;
      return true;
    });
  });
  ```

### When to Use Zustand vs Context

- **Use Zustand when:**
  - You need global state accessible across many components
  - State updates are frequent and performance is critical
  - You want to access state outside React components
  - You need middleware like persistence or devtools
  - State logic is complex with many actions

- **Use Context when:**
  - State is only needed in a specific component subtree
  - State updates are infrequent
  - You want to leverage React's built-in tools
  - State is simple and doesn't need complex logic

### Common Patterns

- Loading and error states:
  ```typescript
  type AsyncStore<T> = {
    data: T | null;
    isLoading: boolean;
    error: string | null;
    fetch: () => Promise<void>;
  };
  
  export const useDataStore = create<AsyncStore<Data>>((set) => ({
    data: null,
    isLoading: false,
    error: null,
    fetch: async () => {
      set({ isLoading: true, error: null });
      try {
        const data = await fetchData();
        set({ data, isLoading: false });
      } catch (error) {
        set({
          error: error instanceof Error ? error.message : 'Unknown error',
          isLoading: false,
        });
      }
    },
  }));
  ```

- Optimistic updates:
  ```typescript
  type TodoStore = {
    todos: Todo[];
    addTodo: (text: string) => Promise<void>;
  };
  
  export const useTodoStore = create<TodoStore>((set, get) => ({
    todos: [],
    addTodo: async (text) => {
      const optimisticTodo = { id: generateId(), text, completed: false };
      
      // Optimistically add todo
      set((state) => ({ todos: [...state.todos, optimisticTodo] }));
      
      try {
        const todo = await createTodoApi(text);
        // Replace optimistic todo with real one
        set((state) => ({
          todos: state.todos.map((t) =>
            t.id === optimisticTodo.id ? todo : t
          ),
        }));
      } catch (error) {
        // Rollback on error
        set((state) => ({
          todos: state.todos.filter((t) => t.id !== optimisticTodo.id),
        }));
      }
    },
  }));
  ```

### Best Practices

- Always define TypeScript types for stores.
- Use selective subscriptions to prevent unnecessary re-renders.
- Keep actions in the store, not in components.
- Use middleware (persist, devtools) for common functionality.
- Split large stores into slices for better organization.
- Use shallow equality when selecting multiple values.
- Implement proper loading and error states for async operations.
- Reset store state when needed (logout, navigation).
- Subscribe to store changes outside components when necessary.
- Test stores independently from components.
- Use descriptive names for stores, actions, and selectors.
- Avoid storing derived state; compute it in selectors.
- Use `get()` to access current state in actions.
- Persist only necessary data to avoid bloated storage.
- Document complex store logic with comments.
