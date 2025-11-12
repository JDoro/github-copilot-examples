---
title: Tailwind CSS Copilot Instructions
applyTo: "**/*.js,**/*.jsx,**/*.ts,**/*.tsx,**/*.html,**/*.vue,**/*.svelte"
---
# Tailwind CSS Copilot Instructions

This instruction set is tailored to help GitHub Copilot generate code that 
uses Tailwind CSS following best practices.

This is an extension of the .github/copilot-instructions.md file and works 
alongside the React Copilot Instructions.

## Coding Standards

### Class Organization and Ordering

- Always order Tailwind classes in a consistent, logical manner for better 
readability and maintainability.
- Follow this recommended ordering: layout → spacing → sizing → typography → 
visual → effects → interactivity → transitions.
- Example ordering:
  1. Layout: `flex`, `grid`, `block`, `inline`, `absolute`, `relative`, `fixed`
  2. Spacing: `m-*`, `p-*`, `space-*`, `gap-*`
  3. Sizing: `w-*`, `h-*`, `min-w-*`, `max-w-*`, `min-h-*`, `max-h-*`
  4. Typography: `text-*`, `font-*`, `leading-*`, `tracking-*`
  5. Visual: `bg-*`, `border-*`, `rounded-*`, `shadow-*`, `opacity-*`
  6. Effects: `blur-*`, `brightness-*`, `contrast-*`, `filter`
  7. Interactivity: `cursor-*`, `select-*`, `pointer-events-*`
  8. Transitions: `transition-*`, `duration-*`, `ease-*`, `animate-*`
- Use Prettier with the `prettier-plugin-tailwindcss` plugin to automatically 
sort classes.
- Keep responsive modifiers (`sm:`, `md:`, `lg:`, `xl:`, `2xl:`) after base 
classes.
- Keep state modifiers (`hover:`, `focus:`, `active:`, `disabled:`) after 
responsive modifiers.
- Example:
  ```jsx
  <div className="flex items-center justify-between px-4 py-2 bg-white 
rounded-lg shadow-md hover:shadow-lg transition-shadow duration-200 md:px-6 
md:py-3">
  ```

### Responsive Design

- Always design mobile-first using Tailwind's default (unprefixed) classes for 
mobile, then add responsive modifiers.
- Use Tailwind's breakpoint system: `sm` (640px), `md` (768px), `lg` 
(1024px), `xl` (1280px), `2xl` (1536px).
- Apply responsive classes in ascending order: base → `sm:` → `md:` → `lg:` 
→ `xl:` → `2xl:`.
- Use `min-width` thinking: classes apply at that breakpoint and above unless 
overridden.
- Avoid excessive breakpoint usage; use only the breakpoints necessary for 
your design.
- Example:
  ```jsx
  <div className="w-full md:w-1/2 lg:w-1/3 xl:w-1/4">
    <p className="text-sm md:text-base lg:text-lg">Responsive text</p>
  </div>
  ```
- Use container utilities with responsive breakpoints for consistent max-
widths:
  ```jsx
  <div className="container mx-auto px-4 sm:px-6 lg:px-8">
  ```

### Color and Theme Usage

- Always use Tailwind's color palette rather than arbitrary color values 
unless absolutely necessary.
- Use semantic color naming from your custom theme when available (e.g., 
`primary`, `secondary`, `accent`).
- Prefer color scale notation: `gray-50` (lightest) to `gray-950` (darkest).
- Use opacity utilities with colors when needed: `bg-blue-500/50` for 50% 
opacity.
- Define custom colors in `tailwind.config.js` rather than using arbitrary 
values:
  ```javascript
  // tailwind.config.js
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#f0f9ff',
          500: '#3b82f6',
          900: '#1e3a8a',
        },
        brand: '#ff6b6b',
      },
    },
  }
  ```
- Avoid using arbitrary values like `text-[#abc123]`; extend the theme 
instead.
- Use CSS variables with Tailwind for dynamic theming:
  ```css
  @layer base {
    :root {
      --color-primary: 59 130 246; /* RGB values */
    }
  }
  ```
  ```jsx
  <div className="bg-[rgb(var(--color-primary))]">
  ```

### Dark Mode Implementation

- Always implement dark mode using Tailwind's `dark:` variant.
- Configure dark mode strategy in `tailwind.config.js`:
  ```javascript
  module.exports = {
    darkMode: 'class', // or 'media' for system preference
  }
  ```
- Apply dark mode classes alongside regular classes:
  ```jsx
  <div className="bg-white text-gray-900 dark:bg-gray-900 dark:text-gray-
100">
  ```
- Use semantic background and text colors that work well in both modes:
  ```jsx
  <div className="bg-gray-50 text-gray-900 dark:bg-gray-800 dark:text-gray-
50">
  ```
- Consider using CSS custom properties for complex dark mode implementations.
- Test all components in both light and dark modes during development.
- Provide proper contrast ratios in both modes for accessibility (WCAG AA 
minimum).

### Spacing and Sizing

- Use Tailwind's spacing scale (0, 0.5, 1, 1.5, 2, 2.5, 3, 3.5, 4, 5, 6, 7, 
8, 9, 10, 11, 12, 14, 16, 20, 24, 28, 32, 36, 40, 44, 48, 52, 56, 60, 64, 
72, 80, 96).
- Each unit represents 0.25rem (4px): `p-4` = 1rem = 16px.
- Use consistent spacing multiples for visual rhythm (e.g., 4, 8, 12, 16, 24, 
32).
- Prefer spacing utilities over arbitrary values:
  - Good: `mt-4 mb-8 px-6`
  - Avoid: `mt-[17px] mb-[33px] px-[25px]`
- Use `space-x-*` and `space-y-*` for consistent spacing between child 
elements:
  ```jsx
  <div className="flex space-x-4">
    <button>Button 1</button>
    <button>Button 2</button>
  </div>
  ```
- Use `gap-*` utilities with flexbox and grid for cleaner spacing:
  ```jsx
  <div className="flex gap-4">
  <div className="grid grid-cols-3 gap-6">
  ```
- Extend the spacing scale in config for project-specific needs:
  ```javascript
  theme: {
    extend: {
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
      },
    },
  }
  ```

### Typography

- Always use Tailwind's typography utilities for consistent text styling.
- Use the typography scale: `text-xs`, `text-sm`, `text-base`, `text-lg`, 
`text-xl`, `text-2xl`, `text-3xl`, `text-4xl`, `text-5xl`, `text-6xl`, 
`text-7xl`, `text-8xl`, `text-9xl`.
- Set font weights using: `font-thin`, `font-extralight`, `font-light`, 
`font-normal`, `font-medium`, `font-semibold`, `font-bold`, `font-extrabold`, 
`font-black`.
- Use line height utilities for readability: `leading-none`, `leading-tight`, 
`leading-snug`, `leading-normal`, `leading-relaxed`, `leading-loose`.
- Apply letter spacing when appropriate: `tracking-tighter`, `tracking-tight`, 
`tracking-normal`, `tracking-wide`, `tracking-wider`, `tracking-widest`.
- Use the `@tailwindcss/typography` plugin for rich text content:
  ```jsx
  <article className="prose prose-lg dark:prose-invert">
    {/* Markdown or rich content */}
  </article>
  ```
- Define custom font families in config:
  ```javascript
  theme: {
    extend: {
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
        serif: ['Merriweather', 'serif'],
        mono: ['Fira Code', 'monospace'],
      },
    },
  }
  ```

### Component Styling Patterns

- Extract repeated utility combinations into reusable components rather than 
using `@apply` in CSS.
- Use `@apply` sparingly and only for base styles or third-party component 
integration:
  ```css
  /* Use only when necessary */
  .btn-primary {
    @apply px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 
transition-colors;
  }
  ```
- Prefer component composition over `@apply`:
  ```jsx
  // Good: Component-based approach
  const Button = ({ children, variant = 'primary' }) => {
    const baseClasses = "px-4 py-2 rounded-lg font-medium transition-colors";
    const variantClasses = {
      primary: "bg-blue-500 text-white hover:bg-blue-600",
      secondary: "bg-gray-200 text-gray-900 hover:bg-gray-300",
    };
    return (
      <button className={`${baseClasses} ${variantClasses[variant]}`}>
        {children}
      </button>
    );
  };
  ```
- Use `clsx` or `classnames` libraries for conditional class application:
  ```jsx
  import clsx from 'clsx';
  
  <button className={clsx(
    'px-4 py-2 rounded-lg',
    isActive && 'bg-blue-500 text-white',
    isDisabled && 'opacity-50 cursor-not-allowed'
  )}>
  ```
- Create variant-based components using object notation:
  ```jsx
  const variants = {
    size: {
      sm: 'text-sm px-3 py-1.5',
      md: 'text-base px-4 py-2',
      lg: 'text-lg px-6 py-3',
    },
    color: {
      blue: 'bg-blue-500 hover:bg-blue-600',
      red: 'bg-red-500 hover:bg-red-600',
    },
  };
  ```

### Layout Patterns

- Use Flexbox for one-dimensional layouts (rows or columns):
  ```jsx
  <div className="flex items-center justify-between gap-4">
  ```
- Use Grid for two-dimensional layouts:
  ```jsx
  <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  ```
- Use `container` utility for centered, max-width layouts:
  ```jsx
  <div className="container mx-auto px-4">
  ```
- Implement common layout patterns:
  - **Centered content**: `flex items-center justify-center`
  - **Sticky header**: `sticky top-0 z-50`
  - **Full height**: `min-h-screen`
  - **Aspect ratio**: `aspect-video`, `aspect-square`
  - **Sidebar layout**: 
    ```jsx
    <div className="flex h-screen">
      <aside className="w-64 bg-gray-100">Sidebar</aside>
      <main className="flex-1 overflow-auto">Content</main>
    </div>
    ```
- Use `space-y-*` or `gap-*` for consistent vertical rhythm in content.

### State Variants and Interactivity

- Always use state variants for interactive elements: `hover:`, `focus:`, 
`active:`, `disabled:`, `focus-visible:`.
- Implement proper focus states for accessibility:
  ```jsx
  <button className="focus:outline-none focus:ring-2 focus:ring-blue-500 
focus:ring-offset-2">
  ```
- Use `focus-visible:` instead of `focus:` when you only want visible focus 
indicators for keyboard navigation.
- Apply hover states that provide clear visual feedback:
  ```jsx
  <a className="text-blue-600 hover:text-blue-800 hover:underline">
  ```
- Use `group` and `peer` utilities for parent/sibling state-based styling:
  ```jsx
  <div className="group">
    <img className="group-hover:scale-110 transition-transform" />
  </div>
  
  <input className="peer" />
  <label className="peer-focus:text-blue-600" />
  ```
- Implement disabled states with proper visual indicators:
  ```jsx
  <button className="disabled:opacity-50 disabled:cursor-not-allowed" 
disabled={isDisabled}>
  ```

### Transitions and Animations

- Always use Tailwind's transition utilities for smooth state changes.
- Apply the `transition` utility with specific properties:
  ```jsx
  <button className="transition-colors duration-200 hover:bg-blue-600">
  <div className="transition-transform duration-300 hover:scale-105">
  <div className="transition-all duration-200 ease-in-out">
  ```
- Use appropriate duration values: `duration-75`, `duration-100`, 
`duration-150`, `duration-200`, `duration-300`, `duration-500`, 
`duration-700`, `duration-1000`.
- Apply easing functions: `ease-linear`, `ease-in`, `ease-out`, `ease-in-
out`.
- Use built-in animations: `animate-spin`, `animate-ping`, `animate-pulse`, 
`animate-bounce`.
- Define custom animations in config:
  ```javascript
  theme: {
    extend: {
      animation: {
        'fade-in': 'fadeIn 0.5s ease-in',
        'slide-up': 'slideUp 0.3s ease-out',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideUp: {
          '0%': { transform: 'translateY(20px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
      },
    },
  }
  ```
- Keep animations subtle and purposeful; avoid overuse.

### Accessibility

- Always include proper focus indicators using `focus:ring-*` utilities:
  ```jsx
  <button className="focus:outline-none focus:ring-2 focus:ring-blue-500 
focus:ring-offset-2">
  ```
- Ensure sufficient color contrast for text (WCAG AA: 4.5:1 for normal text, 
3:1 for large text).
- Use `sr-only` utility for screen reader-only content:
  ```jsx
  <span className="sr-only">Close menu</span>
  ```
- Implement visible focus states for keyboard navigation with `focus-visible:`:
  ```jsx
  <a className="focus-visible:outline-none focus-visible:ring-2 focus-
visible:ring-blue-500">
  ```
- Use semantic HTML elements with appropriate Tailwind styling.
- Provide proper ARIA attributes alongside Tailwind classes.
- Test components with keyboard navigation and screen readers.
- Ensure interactive elements have sufficient touch target sizes (minimum 44x44 
pixels): `min-h-[44px] min-w-[44px]`.

### Performance and Optimization

- Enable JIT (Just-In-Time) mode in Tailwind v3+ for optimal build 
performance (enabled by default).
- Purge unused CSS in production by configuring the content paths:
  ```javascript
  module.exports = {
    content: [
      './src/**/*.{js,jsx,ts,tsx}',
      './public/index.html',
    ],
  }
  ```
- Avoid using arbitrary values excessively; they reduce the effectiveness of 
purging.
- Use `@layer` directive to properly organize custom CSS:
  ```css
  @layer base {
    h1 { @apply text-4xl font-bold; }
  }
  
  @layer components {
    .btn { @apply px-4 py-2 rounded; }
  }
  
  @layer utilities {
    .content-auto { content-visibility: auto; }
  }
  ```
- Minimize the use of `@apply` to keep CSS bundle size small.
- Use PurgeCSS safelist for dynamically generated classes:
  ```javascript
  module.exports = {
    purge: {
      safelist: ['bg-blue-500', 'text-red-600'],
    },
  }
  ```

### Custom Configuration

- Extend Tailwind's default configuration rather than overriding it:
  ```javascript
  module.exports = {
    theme: {
      extend: {
        // Adds to defaults
        colors: {
          brand: '#ff6b6b',
        },
      },
    },
  }
  ```
- Override defaults only when necessary:
  ```javascript
  module.exports = {
    theme: {
      colors: {
        // Replaces all defaults
        primary: '#3b82f6',
      },
    },
  }
  ```
- Use plugins to add reusable utilities and components:
  ```javascript
  module.exports = {
    plugins: [
      require('@tailwindcss/forms'),
      require('@tailwindcss/typography'),
      require('@tailwindcss/aspect-ratio'),
    ],
  }
  ```
- Create custom plugins for project-specific utilities:
  ```javascript
  const plugin = require('tailwindcss/plugin');
  
  module.exports = {
    plugins: [
      plugin(function({ addUtilities }) {
        addUtilities({
          '.scrollbar-hide': {
            '-ms-overflow-style': 'none',
            'scrollbar-width': 'none',
            '&::-webkit-scrollbar': { display: 'none' },
          },
        });
      }),
    ],
  }
  ```

### Arbitrary Values

- Use arbitrary values sparingly and only when the design requires a value not 
in the default scale:
  ```jsx
  <div className="top-[117px]"> {/* Use only if 117px is required */}
  ```
- Prefer extending the theme in config over using arbitrary values:
  ```javascript
  // Better approach
  theme: {
    extend: {
      spacing: {
        '117': '117px',
      },
    },
  }
  ```
- Use arbitrary values for one-off custom properties:
  ```jsx
  <div className="bg-[url('/images/hero.jpg')]">
  <div className="text-[clamp(1rem,5vw,3rem)]">
  ```
- Document why arbitrary values are used when they appear in code.

### Utility-First Best Practices

- Embrace utility-first approach; avoid premature abstraction into custom 
classes.
- Start with utilities in JSX/templates; extract to components when patterns 
emerge.
- Use component libraries (Headless UI, Radix UI) with Tailwind for complex 
components.
- Keep utility classes in templates for better visibility and maintainability.
- Use editor extensions (Tailwind CSS IntelliSense) for autocomplete and 
previews.
- Avoid mixing CSS-in-JS solutions with Tailwind; use one approach 
consistently.
- Document complex utility combinations with comments when necessary:
  ```jsx
  {/* Complex gradient with custom positioning */}
  <div className="bg-gradient-to-br from-purple-600 via-pink-500 to-red-500 
bg-[length:200%_200%]">
  ```

### Responsive Images and Media

- Use Tailwind utilities for responsive images:
  ```jsx
  <img className="w-full h-auto object-cover" src="..." alt="..." />
  ```
- Apply object-fit utilities: `object-contain`, `object-cover`, `object-fill`, 
`object-none`, `object-scale-down`.
- Use object-position utilities: `object-center`, `object-top`, `object-right`, 
etc.
- Implement aspect ratios with `aspect-*` utilities:
  ```jsx
  <div className="aspect-video">
    <img className="w-full h-full object-cover" />
  </div>
  ```
- Use srcset attributes with Tailwind classes for responsive images:
  ```jsx
  <img 
    className="w-full h-auto"
    srcSet="small.jpg 480w, medium.jpg 800w, large.jpg 1200w"
    sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  />
  ```

### Forms and Inputs

- Use the `@tailwindcss/forms` plugin for better form element defaults.
- Style form inputs consistently:
  ```jsx
  <input 
    type="text"
    className="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-
2 focus:ring-blue-500 focus:border-transparent"
  />
  ```
- Implement form validation states:
  ```jsx
  <input 
    className={clsx(
      'w-full px-4 py-2 border rounded-lg',
      hasError ? 'border-red-500 focus:ring-red-500' : 'border-gray-300 
focus:ring-blue-500'
    )}
  />
  ```
- Style checkboxes and radio buttons:
  ```jsx
  <input 
    type="checkbox"
    className="w-4 h-4 text-blue-600 border-gray-300 rounded focus:ring-blue-
500"
  />
  ```
- Use proper spacing and alignment for form groups:
  ```jsx
  <div className="space-y-4">
    <div className="flex flex-col space-y-2">
      <label className="text-sm font-medium text-gray-700">Label</label>
      <input className="..." />
    </div>
  </div>
  ```

### Code Organization

- Keep Tailwind configuration in a dedicated `tailwind.config.js` file at the 
project root.
- Organize custom CSS in separate files by purpose (base, components, 
utilities).
- Import Tailwind directives in the main CSS file:
  ```css
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
  
  @layer base {
    /* Custom base styles */
  }
  
  @layer components {
    /* Component classes with @apply if needed */
  }
  
  @layer utilities {
    /* Custom utility classes */
  }
  ```
- Use meaningful names for custom utilities and components.
- Document complex configurations and custom utilities in comments.

### Testing and Quality Assurance

- Test components in all breakpoints during development.
- Verify dark mode appearance for all components.
- Test with browser DevTools to ensure proper responsive behavior.
- Use Tailwind's built-in responsive design mode in the browser.
- Validate color contrast ratios for accessibility compliance.
- Test keyboard navigation and focus states for interactive elements.
- Use PostCSS plugins to check for unused or duplicate utilities in CI/CD.

### Best Practices Summary

- Always use utility classes directly in JSX/HTML rather than creating custom 
CSS classes.
- Keep class strings readable by breaking long lists into multiple lines or 
using template literals.
- Use responsive design mobile-first with appropriate breakpoints.
- Implement dark mode support using the `dark:` variant.
- Ensure proper accessibility with focus states, color contrast, and semantic 
HTML.
- Optimize performance by configuring content paths correctly for purging.
- Extend the default theme rather than overriding it to maintain consistency.
- Use Tailwind plugins for common patterns (forms, typography, aspect-ratio).
- Embrace the utility-first approach and avoid premature abstraction.
- Leverage editor tooling (IntelliSense, Prettier plugin) for better DX.
- Document complex utility combinations and custom configurations.
- Test thoroughly across devices, browsers, and accessibility tools.
