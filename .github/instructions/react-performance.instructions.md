---
title: React Performance Copilot Instructions
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
---
# React Performance Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate performant 
React code following optimization best practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

## Coding Standards

### React.memo

- Use React.memo for expensive components that render often with same props:
  ```typescript
  // Without memo - re-renders on every parent render
  function ExpensiveComponent({ data }: Props) {
    return <div>{data.name}</div>;
  }
  
  // With memo - only re-renders when props change
  const ExpensiveComponent = React.memo(function ExpensiveComponent({ data }: Props) {
    return <div>{data.name}</div>;
  });
  ```

- Provide custom comparison function when needed:
  ```typescript
  const UserCard = React.memo(
    function UserCard({ user }: Props) {
      return <div>{user.name}</div>;
    },
    (prevProps, nextProps) => {
      // Return true if props are equal (skip re-render)
      return prevProps.user.id === nextProps.user.id;
    }
  );
  ```

- Avoid memoizing components that rarely re-render or are cheap to render:
  ```typescript
  // Don't memo simple components
  function SimpleText({ text }: { text: string }) {
    return <span>{text}</span>;
  }
  ```

### useMemo

- Use useMemo for expensive computations:
  ```typescript
  function ProductList({ products, filter }: Props) {
    // Expensive filtering operation
    const filteredProducts = useMemo(() => {
      return products.filter((product) => {
        return product.category === filter &&
               product.price > 0 &&
               product.inStock;
      });
    }, [products, filter]);
    
    return (
      <div>
        {filteredProducts.map((product) => (
          <ProductCard key={product.id} product={product} />
        ))}
      </div>
    );
  }
  ```

- Memoize derived data to avoid recalculation:
  ```typescript
  function Dashboard({ data }: Props) {
    const statistics = useMemo(() => {
      return {
        total: data.reduce((sum, item) => sum + item.value, 0),
        average: data.length > 0 ? data.reduce((sum, item) => sum + item.value, 0) / data.length : 0,
        max: Math.max(...data.map((item) => item.value)),
        min: Math.min(...data.map((item) => item.value)),
      };
    }, [data]);
    
    return <StatsDisplay stats={statistics} />;
  }
  ```

- Don't overuse useMemo for simple computations:
  ```typescript
  // Avoid - unnecessary memoization
  const fullName = useMemo(() => `${firstName} ${lastName}`, [firstName, lastName]);
  
  // Good - simple computation, no need to memoize
  const fullName = `${firstName} ${lastName}`;
  ```

### useCallback

- Use useCallback to prevent unnecessary re-renders of child components:
  ```typescript
  function ParentComponent() {
    const [count, setCount] = useState(0);
    const [filter, setFilter] = useState('');
    
    // Without useCallback - new function on every render
    const handleClick = () => {
      console.log('Clicked');
    };
    
    // With useCallback - stable function reference
    const handleClick = useCallback(() => {
      console.log('Clicked');
    }, []);
    
    return (
      <div>
        <ChildComponent onClick={handleClick} /> {/* Won't re-render */}
        <button onClick={() => setCount(count + 1)}>Count: {count}</button>
      </div>
    );
  }
  
  const ChildComponent = React.memo(function ChildComponent({ onClick }: Props) {
    return <button onClick={onClick}>Click me</button>;
  });
  ```

- Include dependencies in useCallback:
  ```typescript
  function SearchComponent({ onSearch }: Props) {
    const [query, setQuery] = useState('');
    
    const handleSearch = useCallback(() => {
      onSearch(query);
    }, [query, onSearch]);
    
    return (
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && handleSearch()}
      />
    );
  }
  ```

### Code Splitting

- Use React.lazy for route-based code splitting:
  ```typescript
  import { lazy, Suspense } from 'react';
  
  const Dashboard = lazy(() => import('./pages/Dashboard'));
  const Profile = lazy(() => import('./pages/Profile'));
  const Settings = lazy(() => import('./pages/Settings'));
  
  function App() {
    return (
      <Suspense fallback={<LoadingSpinner />}>
        <Routes>
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/profile" element={<Profile />} />
          <Route path="/settings" element={<Settings />} />
        </Routes>
      </Suspense>
    );
  }
  ```

- Split large components:
  ```typescript
  // Heavy component loaded only when needed
  const HeavyChart = lazy(() => import('./components/HeavyChart'));
  
  function Analytics() {
    const [showChart, setShowChart] = useState(false);
    
    return (
      <div>
        <button onClick={() => setShowChart(true)}>Show Chart</button>
        {showChart && (
          <Suspense fallback={<div>Loading chart...</div>}>
            <HeavyChart />
          </Suspense>
        )}
      </div>
    );
  }
  ```

- Use dynamic imports for conditional features:
  ```typescript
  async function loadModule() {
    if (userHasAccess) {
      const { AdminPanel } = await import('./AdminPanel');
      return <AdminPanel />;
    }
  }
  ```

### Virtualization

- Use virtualization for long lists (react-window or react-virtualized):
  ```typescript
  import { FixedSizeList } from 'react-window';
  
  function LargeList({ items }: Props) {
    const Row = ({ index, style }: { index: number; style: React.CSSProperties }) => (
      <div style={style}>
        {items[index].name}
      </div>
    );
    
    return (
      <FixedSizeList
        height={600}
        itemCount={items.length}
        itemSize={50}
        width="100%"
      >
        {Row}
      </FixedSizeList>
    );
  }
  ```

- Use variable size lists for dynamic heights:
  ```typescript
  import { VariableSizeList } from 'react-window';
  
  function DynamicList({ items }: Props) {
    const getItemSize = (index: number) => {
      return items[index].height || 50;
    };
    
    return (
      <VariableSizeList
        height={600}
        itemCount={items.length}
        itemSize={getItemSize}
        width="100%"
      >
        {Row}
      </VariableSizeList>
    );
  }
  ```

### Debouncing and Throttling

- Debounce expensive operations like search:
  ```typescript
  import { useMemo } from 'react';
  import { debounce } from 'lodash';
  
  function SearchInput({ onSearch }: Props) {
    const debouncedSearch = useMemo(
      () => debounce((query: string) => {
        onSearch(query);
      }, 300),
      [onSearch]
    );
    
    return (
      <input
        onChange={(e) => debouncedSearch(e.target.value)}
        placeholder="Search..."
      />
    );
  }
  ```

- Throttle scroll and resize handlers:
  ```typescript
  import { useEffect } from 'react';
  import { throttle } from 'lodash';
  
  function ScrollTracker() {
    useEffect(() => {
      const handleScroll = throttle(() => {
        console.log('Scroll position:', window.scrollY);
      }, 100);
      
      window.addEventListener('scroll', handleScroll);
      return () => {
        window.removeEventListener('scroll', handleScroll);
        handleScroll.cancel();
      };
    }, []);
  }
  ```

### Lazy Initial State

- Use function for expensive initial state calculations:
  ```typescript
  // Avoid - runs on every render
  const [state, setState] = useState(expensiveComputation());
  
  // Good - runs only once
  const [state, setState] = useState(() => expensiveComputation());
  
  // Example
  const [data, setData] = useState(() => {
    const stored = localStorage.getItem('data');
    return stored ? JSON.parse(stored) : defaultData;
  });
  ```

### Avoiding Unnecessary Re-renders

- Use proper key props in lists:
  ```typescript
  // Avoid - index as key (causes issues on reorder/filter)
  {items.map((item, index) => (
    <Item key={index} data={item} />
  ))}
  
  // Good - stable unique identifier
  {items.map((item) => (
    <Item key={item.id} data={item} />
  ))}
  ```

- Avoid inline object/array creation in props:
  ```typescript
  // Avoid - new object on every render
  <Component style={{ margin: 10 }} />
  
  // Good - stable reference
  const style = { margin: 10 };
  <Component style={style} />
  
  // Or use useMemo
  const style = useMemo(() => ({ margin: 10 }), []);
  <Component style={style} />
  ```

- Avoid inline function definitions in props:
  ```typescript
  // Avoid - new function on every render
  <button onClick={() => handleClick(id)}>Click</button>
  
  // Good - use useCallback
  const handleButtonClick = useCallback(() => {
    handleClick(id);
  }, [id, handleClick]);
  
  <button onClick={handleButtonClick}>Click</button>
  ```

### State Management Optimization

- Colocate state close to where it's used:
  ```typescript
  // Avoid - global state for local UI
  function Parent() {
    const [isOpen, setIsOpen] = useState(false);
    
    return (
      <div>
        <Child1 />
        <Child2 isOpen={isOpen} setIsOpen={setIsOpen} />
      </div>
    );
  }
  
  // Good - state in the component that uses it
  function Child2() {
    const [isOpen, setIsOpen] = useState(false);
    return <Modal isOpen={isOpen} />;
  }
  ```

- Split state to prevent unnecessary re-renders:
  ```typescript
  // Avoid - single state object
  const [state, setState] = useState({ name: '', email: '', age: 0 });
  
  // Good - separate state for independent values
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);
  ```

### Context Optimization

- Split contexts to prevent unnecessary re-renders:
  ```typescript
  // Avoid - single context for all state
  const AppContext = createContext({ user: null, theme: 'light', ... });
  
  // Good - separate contexts
  const UserContext = createContext(null);
  const ThemeContext = createContext('light');
  ```

- Memoize context values:
  ```typescript
  function AppProvider({ children }: Props) {
    const [user, setUser] = useState(null);
    
    const value = useMemo(() => ({
      user,
      setUser,
    }), [user]);
    
    return (
      <UserContext.Provider value={value}>
        {children}
      </UserContext.Provider>
    );
  }
  ```

### Image Optimization

- Use appropriate image formats and sizes:
  ```tsx
  // Good - responsive images
  <img
    src="/image-800.jpg"
    srcSet="/image-400.jpg 400w, /image-800.jpg 800w, /image-1200.jpg 1200w"
    sizes="(max-width: 600px) 400px, (max-width: 900px) 800px, 1200px"
    alt="Description"
    loading="lazy"
  />
  ```

- Use lazy loading for images below the fold:
  ```tsx
  <img src="/image.jpg" alt="Description" loading="lazy" />
  ```

### Bundle Size Optimization

- Import only what you need from libraries:
  ```typescript
  // Avoid - imports entire library
  import _ from 'lodash';
  _.debounce(fn, 300);
  
  // Good - imports only what's needed
  import debounce from 'lodash/debounce';
  debounce(fn, 300);
  ```

- Use tree-shakeable libraries:
  ```typescript
  // Good - tree-shakeable
  import { debounce } from 'lodash-es';
  
  // Avoid - not tree-shakeable
  import { debounce } from 'lodash';
  ```

### Profiling and Monitoring

- Use React DevTools Profiler to identify performance issues:
  ```tsx
  import { Profiler } from 'react';
  
  function onRenderCallback(
    id: string,
    phase: 'mount' | 'update',
    actualDuration: number,
    baseDuration: number,
    startTime: number,
    commitTime: number
  ) {
    console.log(`${id} (${phase}) took ${actualDuration}ms`);
  }
  
  <Profiler id="App" onRender={onRenderCallback}>
    <App />
  </Profiler>
  ```

- Monitor Core Web Vitals:
  ```typescript
  import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';
  
  getCLS(console.log);
  getFID(console.log);
  getFCP(console.log);
  getLCP(console.log);
  getTTFB(console.log);
  ```

### Transition and startTransition

- Use startTransition for non-urgent updates:
  ```typescript
  import { startTransition, useState } from 'react';
  
  function SearchResults() {
    const [query, setQuery] = useState('');
    const [results, setResults] = useState([]);
    
    const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
      // Urgent: update input value
      setQuery(e.target.value);
      
      // Non-urgent: update search results
      startTransition(() => {
        setResults(performExpensiveSearch(e.target.value));
      });
    };
    
    return (
      <div>
        <input value={query} onChange={handleChange} />
        <ResultsList results={results} />
      </div>
    );
  }
  ```

- Use useTransition hook:
  ```typescript
  import { useTransition } from 'react';
  
  function TabContainer() {
    const [isPending, startTransition] = useTransition();
    const [tab, setTab] = useState('home');
    
    const selectTab = (nextTab: string) => {
      startTransition(() => {
        setTab(nextTab);
      });
    };
    
    return (
      <div>
        <TabButton onClick={() => selectTab('home')}>Home</TabButton>
        <TabButton onClick={() => selectTab('profile')}>Profile</TabButton>
        {isPending && <Spinner />}
        <TabContent tab={tab} />
      </div>
    );
  }
  ```

### Best Practices

- Use React.memo for components that render often with same props.
- Use useMemo for expensive calculations and derived data.
- Use useCallback for functions passed to memoized child components.
- Implement code splitting for routes and large components.
- Use virtualization for lists with many items (>100).
- Debounce expensive operations like search and API calls.
- Throttle scroll and resize event handlers.
- Use lazy initial state for expensive computations.
- Avoid inline object/array creation in props.
- Use stable keys in lists (not indexes).
- Colocate state close to where it's used.
- Split large contexts to prevent unnecessary re-renders.
- Memoize context values to prevent consumer re-renders.
- Use lazy loading for images and components.
- Import only what you need from libraries.
- Profile your app with React DevTools to find bottlenecks.
- Monitor Core Web Vitals for production performance.
- Use startTransition for non-urgent state updates.
- Avoid premature optimization; measure first.
- Keep bundle size small with code splitting and tree shaking.
