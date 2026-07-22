# CSS Modules Best Practices Guide

## Overview

This project uses CSS Modules for styling to ensure maintainability, performance, and proper theme support across all environments (development, test, and production).

## Naming Convention

- CSS Module files: `ComponentName.module.css`
- Import in component: `import styles from './ComponentName.module.css'`
- Class names in CSS: Use camelCase (e.g., `.headerContainer`)

## File Structure

```
src/
  components/
    layout/
      AppHeader.tsx
      AppHeader.module.css
    dashboard/
      StatCard.tsx
      StatCard.module.css
```

## Basic Usage

```typescript
// Component.tsx
import styles from './Component.module.css';

export default function Component() {
  return (
    <div className={styles.container}>
      <h1 className={styles.title}>Hello</h1>
    </div>
  );
}
```

```css
/* Component.module.css */
.container {
  padding: 1rem;
  background: var(--card-bg);
}

.title {
  color: var(--text-dark);
  font-size: 1.5rem;
}
```

## Theme Support

Our app supports three themes: default, State Farm, and dark. Use CSS variables for colors:

```css
.card {
  background-color: var(--card-bg);
  color: var(--text-dark);
  border: 1px solid var(--border-color);
}

/* Theme-specific overrides */
:global(html.theme-dark) .card {
  background-color: var(--card-bg);
  border-color: var(--border-color);
}

:global(html.theme-statefarm) .card {
  /* State Farm specific styles */
}
```

## Dynamic Styles

For dynamic colors passed as props:

```typescript
// Only use inline styles for truly dynamic values
<div
  className={styles.icon}
  style={{ backgroundColor: props.color }}
>
```

## Conditional Classes

```typescript
import styles from './Component.module.css';

// Single conditional
<div className={error ? styles.error : styles.success}>

// Multiple classes
<div className={`${styles.base} ${isActive ? styles.active : ''}`}>

// Using a utility function
const classNames = (...classes: string[]) => classes.filter(Boolean).join(' ');
<div className={classNames(
  styles.button,
  isPrimary && styles.primary,
  isLoading && styles.loading
)}>
```

## Media Queries

```css
.container {
  padding: 2rem;
}

@media (max-width: 768px) {
  .container {
    padding: 1rem;
  }
}
```

## Global Styles

For truly global styles, use `src/styles/globals.scss`. For component-specific globals:

```css
/* Target global elements within component scope */
:global(.modal-open) .container {
  filter: blur(5px);
}
```

## Performance Best Practices

1. **Avoid Inline Styles**: Use CSS modules for all static styles
2. **Use CSS Variables**: Leverage our theme system
3. **Minimize Specificity**: Keep selectors simple
4. **Group Related Styles**: Organize by component sections

## Migration Checklist

When converting from inline styles to CSS modules:

1. Create `.module.css` file
2. Copy inline styles to CSS module
3. Convert kebab-case to camelCase for class names
4. Import styles in component
5. Replace className strings with styles.className
6. Remove `<style jsx>` blocks
7. Test in all themes
8. Verify production build

## Common CSS Variables

The following are examples of common CSS variables available from `src/styles/globals.scss`. **This is not an exhaustive list; always refer to `src/styles/globals.scss` for the complete and definitive set of variables.**

It is best practice to use these variables for all colors, font sizes (where a typographic scale is defined), spacing, and other themeable properties to ensure consistency.

```css
/* Colors */
--primary-color
--primary-dark
--primary-light
--text-dark
--text-medium
--text-light
--bg-light
--card-bg
--border-color
--card-shadow

/* Typography (Examples - see globals.scss for full scale) */
--font-size-xs
--font-size-sm
--font-size-base
--font-size-lg
--font-size-xl

/* Metrics */
--metric-blue
--metric-teal
--metric-green
--metric-orange

/* Layout */
--header-height: 60px
--sidebar-width: 260px
```

## DO's and DON'Ts

### DO's ✅

- Use CSS modules for all component styles
- Use CSS variables for theme support
- Keep styles close to components
- Use semantic class names
- Test in all themes

### DON'Ts ❌

- Don't use inline styles except for dynamic values
- Don't use `!important` unless absolutely necessary
- Don't nest selectors deeply
- Don't hardcode colors
- Don't use styled-jsx

## Troubleshooting

### CSS Modules Not Working

1. Ensure file ends with `.module.css`
2. Check import statement
3. Verify Next.js config
4. Clear `.next` cache

### Styles Not Applied in Production

1. Check for typos in class names
2. Ensure no dynamic class name generation
3. Verify build output
4. Check browser console for errors

### Theme Not Applying

1. Verify CSS variable usage
2. Check :global() selectors
3. Ensure theme class on html/body
4. Test theme toggle functionality
