---
title: React Accessibility Copilot Instructions
applyTo: "**/*.ts,**/*.tsx,**/*.js,**/*.jsx"
---
# React Accessibility Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate accessible 
React code following WCAG 2.1 AA standards and best practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

## Coding Standards

### WCAG 2.1 Principles

Follow the four principles of accessibility (POUR):
- **Perceivable**: Information must be presentable to users in ways they can 
perceive
- **Operable**: Interface components must be operable by all users
- **Understandable**: Information and operation must be understandable
- **Robust**: Content must be robust enough to work with assistive technologies

### Semantic HTML

- Always use semantic HTML elements instead of generic divs:
  ```tsx
  // Good
  <header>
    <nav>
      <ul>
        <li><a href="/home">Home</a></li>
      </ul>
    </nav>
  </header>
  <main>
    <article>
      <h1>Title</h1>
      <p>Content</p>
    </article>
  </main>
  <footer>
    <p>Copyright 2025</p>
  </footer>
  
  // Avoid
  <div className="header">
    <div className="nav">
      <div className="menu">
        <div><a href="/home">Home</a></div>
      </div>
    </div>
  </div>
  ```

- Use appropriate heading hierarchy:
  ```tsx
  // Good - Logical heading hierarchy
  <h1>Page Title</h1>
  <h2>Section Title</h2>
  <h3>Subsection Title</h3>
  
  // Avoid - Skipping heading levels
  <h1>Page Title</h1>
  <h4>Section Title</h4>
  ```

### ARIA Attributes

- Use ARIA roles only when semantic HTML is insufficient:
  ```tsx
  // Good - Semantic HTML (preferred)
  <button onClick={handleClick}>Click me</button>
  
  // Use ARIA only when necessary
  <div role="button" tabIndex={0} onClick={handleClick} onKeyPress={handleKeyPress}>
    Custom Button
  </div>
  ```

- Provide accessible names for interactive elements:
  ```tsx
  // Using aria-label
  <button aria-label="Close dialog" onClick={onClose}>
    <CloseIcon />
  </button>
  
  // Using aria-labelledby
  <div role="dialog" aria-labelledby="dialog-title">
    <h2 id="dialog-title">Confirmation</h2>
    <p>Are you sure?</p>
  </div>
  
  // Using aria-describedby
  <input
    type="email"
    aria-describedby="email-hint"
  />
  <span id="email-hint">We'll never share your email</span>
  ```

- Use aria-live for dynamic content updates:
  ```tsx
  function Notification({ message }: { message: string }) {
    return (
      <div role="alert" aria-live="polite" aria-atomic="true">
        {message}
      </div>
    );
  }
  
  // For urgent updates
  <div role="alert" aria-live="assertive">
    Error: Form submission failed
  </div>
  ```

- Mark loading states appropriately:
  ```tsx
  function LoadingButton({ isLoading, children }: Props) {
    return (
      <button
        aria-busy={isLoading}
        aria-label={isLoading ? 'Loading...' : undefined}
        disabled={isLoading}
      >
        {isLoading ? <Spinner /> : children}
      </button>
    );
  }
  ```

### Keyboard Navigation

- Ensure all interactive elements are keyboard accessible:
  ```tsx
  function CustomButton({ onClick, children }: Props) {
    const handleKeyDown = (e: React.KeyboardEvent) => {
      if (e.key === 'Enter' || e.key === ' ') {
        e.preventDefault();
        onClick();
      }
    };
    
    return (
      <div
        role="button"
        tabIndex={0}
        onClick={onClick}
        onKeyDown={handleKeyDown}
      >
        {children}
      </div>
    );
  }
  ```

- Implement focus management for modals and dialogs:
  ```tsx
  function Dialog({ isOpen, onClose, children }: Props) {
    const dialogRef = useRef<HTMLDivElement>(null);
    const previousFocusRef = useRef<HTMLElement | null>(null);
    
    useEffect(() => {
      if (isOpen) {
        previousFocusRef.current = document.activeElement as HTMLElement;
        dialogRef.current?.focus();
      } else {
        previousFocusRef.current?.focus();
      }
    }, [isOpen]);
    
    const handleKeyDown = (e: React.KeyboardEvent) => {
      if (e.key === 'Escape') {
        onClose();
      }
    };
    
    if (!isOpen) return null;
    
    return (
      <div
        ref={dialogRef}
        role="dialog"
        aria-modal="true"
        tabIndex={-1}
        onKeyDown={handleKeyDown}
      >
        {children}
      </div>
    );
  }
  ```

- Trap focus within modals:
  ```tsx
  import { useRef, useEffect } from 'react';
  
  function useFocusTrap(isActive: boolean) {
    const containerRef = useRef<HTMLDivElement>(null);
    
    useEffect(() => {
      if (!isActive) return;
      
      const container = containerRef.current;
      if (!container) return;
      
      const focusableElements = container.querySelectorAll(
        'a[href], button:not([disabled]), textarea, input, select, [tabindex]:not([tabindex="-1"])'
      );
      
      const firstElement = focusableElements[0] as HTMLElement;
      const lastElement = focusableElements[focusableElements.length - 1] as HTMLElement;
      
      const handleTabKey = (e: KeyboardEvent) => {
        if (e.key !== 'Tab') return;
        
        if (e.shiftKey) {
          if (document.activeElement === firstElement) {
            lastElement.focus();
            e.preventDefault();
          }
        } else {
          if (document.activeElement === lastElement) {
            firstElement.focus();
            e.preventDefault();
          }
        }
      };
      
      container.addEventListener('keydown', handleTabKey);
      firstElement.focus();
      
      return () => {
        container.removeEventListener('keydown', handleTabKey);
      };
    }, [isActive]);
    
    return containerRef;
  }
  ```

- Implement skip links for navigation:
  ```tsx
  function SkipLink() {
    return (
      <a
        href="#main-content"
        className="skip-link"
        style={{
          position: 'absolute',
          top: '-40px',
          left: 0,
          background: '#000',
          color: '#fff',
          padding: '8px',
          textDecoration: 'none',
          zIndex: 100,
        }}
        onFocus={(e) => {
          e.currentTarget.style.top = '0';
        }}
        onBlur={(e) => {
          e.currentTarget.style.top = '-40px';
        }}
      >
        Skip to main content
      </a>
    );
  }
  ```

### Form Accessibility

- Always associate labels with form inputs:
  ```tsx
  // Good - Using htmlFor
  <label htmlFor="email">Email Address</label>
  <input id="email" type="email" name="email" />
  
  // Good - Nested label
  <label>
    Email Address
    <input type="email" name="email" />
  </label>
  
  // Avoid - No association
  <div>Email Address</div>
  <input type="email" name="email" />
  ```

- Provide helpful error messages:
  ```tsx
  function FormField({ id, label, error, ...props }: Props) {
    const errorId = `${id}-error`;
    
    return (
      <div>
        <label htmlFor={id}>{label}</label>
        <input
          id={id}
          aria-invalid={!!error}
          aria-describedby={error ? errorId : undefined}
          {...props}
        />
        {error && (
          <span id={errorId} role="alert" className="error">
            {error}
          </span>
        )}
      </div>
    );
  }
  ```

- Mark required fields:
  ```tsx
  <label htmlFor="email">
    Email Address
    <abbr title="required" aria-label="required">*</abbr>
  </label>
  <input
    id="email"
    type="email"
    required
    aria-required="true"
  />
  ```

- Group related form fields:
  ```tsx
  <fieldset>
    <legend>Shipping Address</legend>
    <label htmlFor="street">Street</label>
    <input id="street" type="text" />
    
    <label htmlFor="city">City</label>
    <input id="city" type="text" />
  </fieldset>
  ```

### Focus Indicators

- Always provide visible focus indicators:
  ```css
  /* Good - Clear focus indicator */
  button:focus-visible {
    outline: 2px solid #0066cc;
    outline-offset: 2px;
  }
  
  /* Avoid - Removing focus outline without replacement */
  button:focus {
    outline: none;
  }
  ```

- Use focus-visible for better UX:
  ```tsx
  <button
    style={{
      outline: 'none',
    }}
    onFocus={(e) => {
      // Only show focus for keyboard navigation
      if (e.target.matches(':focus-visible')) {
        e.target.style.outline = '2px solid blue';
      }
    }}
  >
    Click me
  </button>
  ```

### Color Contrast

- Ensure sufficient color contrast (WCAG AA):
  - Normal text: 4.5:1 contrast ratio
  - Large text (18pt+ or 14pt+ bold): 3:1 contrast ratio
  - UI components and graphics: 3:1 contrast ratio

  ```tsx
  // Good - High contrast
  <button style={{ background: '#0066cc', color: '#ffffff' }}>
    Submit
  </button>
  
  // Avoid - Low contrast
  <button style={{ background: '#cccccc', color: '#999999' }}>
    Submit
  </button>
  ```

- Don't rely solely on color to convey information:
  ```tsx
  // Good - Color + icon + text
  function StatusIndicator({ status }: { status: 'success' | 'error' }) {
    return (
      <div>
        {status === 'success' ? (
          <>
            <CheckIcon aria-hidden="true" />
            <span style={{ color: 'green' }}>Success</span>
          </>
        ) : (
          <>
            <ErrorIcon aria-hidden="true" />
            <span style={{ color: 'red' }}>Error</span>
          </>
        )}
      </div>
    );
  }
  
  // Avoid - Color only
  <div style={{ color: status === 'success' ? 'green' : 'red' }}>
    Status
  </div>
  ```

### Images and Media

- Always provide alt text for images:
  ```tsx
  // Good - Descriptive alt text
  <img src="/logo.png" alt="Company Logo" />
  
  // For decorative images
  <img src="/decoration.png" alt="" aria-hidden="true" />
  
  // Avoid - Missing alt text
  <img src="/logo.png" />
  ```

- Provide captions and transcripts for media:
  ```tsx
  <video controls>
    <source src="video.mp4" type="video/mp4" />
    <track kind="captions" src="captions.vtt" srclang="en" label="English" />
    <track kind="descriptions" src="descriptions.vtt" srclang="en" />
  </video>
  ```

### Landmarks and Regions

- Use landmark roles for page structure:
  ```tsx
  <header role="banner">
    <nav role="navigation" aria-label="Main navigation">
      {/* Navigation items */}
    </nav>
  </header>
  
  <main role="main" id="main-content">
    <article>
      <h1>Article Title</h1>
    </article>
  </main>
  
  <aside role="complementary" aria-label="Related articles">
    {/* Sidebar content */}
  </aside>
  
  <footer role="contentinfo">
    {/* Footer content */}
  </footer>
  ```

- Label multiple landmarks of the same type:
  ```tsx
  <nav aria-label="Primary navigation">
    {/* Main navigation */}
  </nav>
  
  <nav aria-label="Footer navigation">
    {/* Footer navigation */}
  </nav>
  ```

### Interactive Component Patterns

- Accessible dropdown menu:
  ```tsx
  function DropdownMenu({ trigger, items }: Props) {
    const [isOpen, setIsOpen] = useState(false);
    const [focusedIndex, setFocusedIndex] = useState(0);
    
    const handleKeyDown = (e: React.KeyboardEvent) => {
      switch (e.key) {
        case 'ArrowDown':
          e.preventDefault();
          setFocusedIndex((i) => (i + 1) % items.length);
          break;
        case 'ArrowUp':
          e.preventDefault();
          setFocusedIndex((i) => (i - 1 + items.length) % items.length);
          break;
        case 'Escape':
          setIsOpen(false);
          break;
        case 'Enter':
        case ' ':
          e.preventDefault();
          items[focusedIndex].onClick();
          setIsOpen(false);
          break;
      }
    };
    
    return (
      <div>
        <button
          aria-haspopup="menu"
          aria-expanded={isOpen}
          onClick={() => setIsOpen(!isOpen)}
        >
          {trigger}
        </button>
        
        {isOpen && (
          <ul role="menu" onKeyDown={handleKeyDown}>
            {items.map((item, index) => (
              <li
                key={index}
                role="menuitem"
                tabIndex={index === focusedIndex ? 0 : -1}
                onClick={item.onClick}
              >
                {item.label}
              </li>
            ))}
          </ul>
        )}
      </div>
    );
  }
  ```

- Accessible tabs:
  ```tsx
  function Tabs({ tabs }: Props) {
    const [activeTab, setActiveTab] = useState(0);
    
    const handleKeyDown = (e: React.KeyboardEvent, index: number) => {
      switch (e.key) {
        case 'ArrowLeft':
          e.preventDefault();
          setActiveTab((index - 1 + tabs.length) % tabs.length);
          break;
        case 'ArrowRight':
          e.preventDefault();
          setActiveTab((index + 1) % tabs.length);
          break;
        case 'Home':
          e.preventDefault();
          setActiveTab(0);
          break;
        case 'End':
          e.preventDefault();
          setActiveTab(tabs.length - 1);
          break;
      }
    };
    
    return (
      <div>
        <div role="tablist">
          {tabs.map((tab, index) => (
            <button
              key={index}
              role="tab"
              aria-selected={activeTab === index}
              aria-controls={`panel-${index}`}
              id={`tab-${index}`}
              tabIndex={activeTab === index ? 0 : -1}
              onClick={() => setActiveTab(index)}
              onKeyDown={(e) => handleKeyDown(e, index)}
            >
              {tab.label}
            </button>
          ))}
        </div>
        
        {tabs.map((tab, index) => (
          <div
            key={index}
            role="tabpanel"
            id={`panel-${index}`}
            aria-labelledby={`tab-${index}`}
            hidden={activeTab !== index}
          >
            {tab.content}
          </div>
        ))}
      </div>
    );
  }
  ```

- Accessible modal:
  ```tsx
  function Modal({ isOpen, onClose, title, children }: Props) {
    const modalRef = useRef<HTMLDivElement>(null);
    
    useEffect(() => {
      if (isOpen) {
        const previousFocus = document.activeElement as HTMLElement;
        modalRef.current?.focus();
        
        return () => {
          previousFocus?.focus();
        };
      }
    }, [isOpen]);
    
    if (!isOpen) return null;
    
    return (
      <>
        <div
          className="modal-overlay"
          onClick={onClose}
          aria-hidden="true"
        />
        <div
          ref={modalRef}
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-title"
          tabIndex={-1}
        >
          <h2 id="modal-title">{title}</h2>
          {children}
          <button onClick={onClose} aria-label="Close dialog">
            Close
          </button>
        </div>
      </>
    );
  }
  ```

### Touch Targets

- Ensure minimum touch target size (44x44 pixels):
  ```tsx
  <button
    style={{
      minWidth: '44px',
      minHeight: '44px',
      padding: '12px',
    }}
  >
    <Icon />
  </button>
  ```

- Provide adequate spacing between interactive elements:
  ```tsx
  <div style={{ display: 'flex', gap: '8px' }}>
    <button>Button 1</button>
    <button>Button 2</button>
  </div>
  ```

### Screen Reader Support

- Hide decorative elements from screen readers:
  ```tsx
  <div aria-hidden="true">
    <DecorativeIcon />
  </div>
  ```

- Provide screen reader-only text:
  ```tsx
  function VisuallyHidden({ children }: Props) {
    return (
      <span
        style={{
          position: 'absolute',
          width: '1px',
          height: '1px',
          padding: 0,
          margin: '-1px',
          overflow: 'hidden',
          clip: 'rect(0, 0, 0, 0)',
          whiteSpace: 'nowrap',
          border: 0,
        }}
      >
        {children}
      </span>
    );
  }
  
  // Usage
  <button>
    <Icon aria-hidden="true" />
    <VisuallyHidden>Settings</VisuallyHidden>
  </button>
  ```

- Announce dynamic content changes:
  ```tsx
  function LiveRegion({ message, priority = 'polite' }: Props) {
    return (
      <div
        role="status"
        aria-live={priority}
        aria-atomic="true"
        className="sr-only"
      >
        {message}
      </div>
    );
  }
  ```

### Testing Accessibility

- Use automated testing tools:
  ```tsx
  import { axe, toHaveNoViolations } from 'jest-axe';
  
  expect.extend(toHaveNoViolations);
  
  test('has no accessibility violations', async () => {
    const { container } = render(<MyComponent />);
    const results = await axe(container);
    expect(results).toHaveNoViolations();
  });
  ```

- Test keyboard navigation:
  ```tsx
  test('can navigate with keyboard', async () => {
    const user = userEvent.setup();
    render(<Menu />);
    
    const firstItem = screen.getByRole('menuitem', { name: /first/i });
    firstItem.focus();
    
    await user.keyboard('{ArrowDown}');
    expect(screen.getByRole('menuitem', { name: /second/i })).toHaveFocus();
    
    await user.keyboard('{Enter}');
    expect(mockOnSelect).toHaveBeenCalled();
  });
  ```

- Test screen reader announcements:
  ```tsx
  test('announces form submission', async () => {
    const user = userEvent.setup();
    render(<Form />);
    
    await user.click(screen.getByRole('button', { name: /submit/i }));
    
    expect(await screen.findByRole('status')).toHaveTextContent(
      'Form submitted successfully'
    );
  });
  ```

### Best Practices

- Use semantic HTML elements whenever possible.
- Provide text alternatives for non-text content.
- Ensure all functionality is keyboard accessible.
- Maintain a logical heading hierarchy (h1, h2, h3...).
- Associate labels with form inputs.
- Provide clear focus indicators.
- Ensure sufficient color contrast (4.5:1 for normal text).
- Make touch targets at least 44x44 pixels.
- Manage focus for dynamic content (modals, tooltips).
- Use ARIA attributes only when semantic HTML is insufficient.
- Test with keyboard navigation and screen readers.
- Use automated accessibility testing tools (axe, WAVE).
- Don't rely solely on color to convey information.
- Provide captions and transcripts for media content.
- Implement proper error handling and validation messages.
- Use landmarks to define page structure.
- Hide decorative content from assistive technologies.
- Announce dynamic content changes with aria-live.
- Test with actual assistive technologies when possible.
- Follow WCAG 2.1 AA standards at minimum.
