# Agent Sales Activity Manager (ASAM) - Styling Standards & Expert Guidelines

**🎨 Modern Design System & Implementation Standards**

This document establishes the styling standards for the Agent Sales Activity Manager (ASAM) application. Our approach prioritizes:

- **CSS Modules**: For component-level scoped styles, ensuring modularity and preventing global namespace collisions.
- **Global Stylesheet (`src/styles/globals.scss`)**: For base HTML element styling, CSS variable definitions (themes), and a limited set of utility classes.
- **CSS Variables**: For theme support (default, State Farm, dark modes) and consistent design tokens (colors, spacing, typography).
- **Responsive Design**: Ensuring adaptability across mobile, tablet, and desktop viewports using media queries within CSS Modules and `globals.scss`.

All new and refactored components **MUST** use CSS Modules for their primary styling. Inline styles and `<style jsx>` are strictly prohibited.

---

## 📋 **Key Principles**

### **1. CSS Modules First**

Component-specific styles, including layout, typography, and state-based variations, are managed within `ComponentName.module.css` files.

### **2. Global CSS Variables for Theming**

Colors, fonts, and other design tokens are defined as CSS variables in `src/styles/globals.scss`. These variables enable theme switching and maintain visual consistency. Components should leverage these variables in their CSS Modules.

### **3. Base Styles & Utilities**

`src/styles/globals.scss` provides foundational styles for HTML elements (e.g., `body`, `h1-h6`, `p`) and may contain a few global utility classes. However, reliance on extensive global utility classes for component structure is discouraged in favor of CSS Modules.

### **4. Responsive & Dark Mode Compatibility**

- Responsive design is implemented using media queries, primarily within component-specific CSS Modules or, for global concerns, in `src/styles/globals.scss`.
- Dark mode and other themes are supported through CSS variables defined in `src/styles/globals.scss`.

---

## 🏗️ **Styling Structure**

### **Component Styles (CSS Modules)**

- Each component should have its own `ComponentName.module.css` file.
- Class names within modules are locally scoped by default (e.g., `styles.myClass`).
- Use CSS variables from `globals.scss` for consistency (e.g., `background-color: var(--card-bg);`).

### **Global Styles (`src/styles/globals.scss`)**

- **CSS Variable Definitions**: Central place for all theme variables (e.g., `--primary-color`, `--text-dark`, `--card-bg`).
- **Base Element Styling**: Default styles for `body`, typography (`h1`-`h6`, `a`, `p`), form elements, etc.
- **Theme Overrides**: Global rules for applying theme-specific variable values (e.g., `html.theme-dark { --card-bg: #000; }`).
- **Limited Utility Classes**: Only if a style is truly global and reusable across many unrelated components (e.g., screen reader visibility helpers). Prefer CSS Modules for component-specific utilities.

---

## 🎯 **Implementation Guidelines**

### **For New Components**

1. **Create a `ComponentName.module.css` file** alongside your component.
2. **Define component-specific styles** in this module.
3. **Utilize global CSS variables** (e.g., `var(--primary-color)`, `var(--font-size-base)`) from `src/styles/globals.scss` within your module's rules.
4. **Import and use styles**: `import styles from './ComponentName.module.css'; <div className={styles.container}>`.
5. **Ensure responsive behavior** using media queries within the module.
6. **Test in all supported themes** (light, dark, State Farm) to ensure variables are applied correctly.

### **For Existing Components**

1. If not already using CSS Modules, **migrate all styles** (including inline and `<style jsx>`) to a new `.module.css` file.
2. **Replace hardcoded values** (colors, fonts, spacing) with appropriate global CSS variables.
3. **Remove any local CSS overrides** that conflict with or duplicate what can be achieved with global variables or base styles.

### **General Practices**

- **Avoid `!important`** where possible. Rely on specificity or better rule definition.
- **Keep selectors concise** and tied to the component's structure.
- **Adhere to the `CSS_MODULES_GUIDE.md`** for detailed patterns and best practices.

---

## 🔧 **Technical Implementation**

### **Global CSS Location**

All global styles, CSS variable definitions, and theme rules are centralized in: `src/styles/globals.scss`

### **Key CSS Variables (Examples from `globals.scss`)**

Refer to `src/styles/globals.scss` for the comprehensive list. Examples include:

```css
/* Found in src/styles/globals.scss */
:root {
  --primary-color: #2c3e50; /* Example, actual values may vary */
  --text-dark: #1e2a3a;
  --card-bg: #fafbfc;
  --border-color: #e8ebef;
  --font-size-base: 1rem;
  /* ... and many more for themes, typography, spacing, etc. */
}

html.theme-dark {
  /* Example dark theme overrides */
  --primary-color: #21262d;
  --text-dark: #e0e6ed;
  --card-bg: #161b22;
  /* ... etc. */
}
```

---

## 🔙 **Universal Back Button System**

The `BackButton` component, located at `src/components/common/BackButton.tsx`, provides a consistent navigation element. It is an exception to the "CSS Modules First" principle as it relies on global classes and CSS variables defined in `src/styles/globals.scss` to ensure universal applicability and theme support.

### **BackButton Component**

Location: `src/components/common/BackButton.tsx`

### **Usage Examples**

```jsx
// Basic usage (defaults to 'primary' variant)
<BackButton />

// Custom destination
<BackButton href="/custom-page" text="Back to Custom Page" />

// Different variants (NOTE: 'secondary' and 'minimal' variants currently do not have distinct global styles)
<BackButton variant="secondary" />
<BackButton variant="minimal" />

// Custom click handler
<BackButton onClick={() => router.back()} text="Go Back" />

// Different icons
<BackButton icon="chevron" />
<BackButton icon="arrow" />
```

### **Theme Support Appearance**

The `BackButton`'s appearance changes based on the active theme, primarily through CSS variables:

- **Default Theme**:
  - Text Color: Uses `var(--primary-color)`.
  - Background: Light transparent blue (`rgba(74, 144, 226, 0.05)`). No gradient.
- **State Farm Theme**:
  - Text Color: Uses `var(--primary-color)` (which is red in the State Farm theme).
  - Background: Same as default (light transparent blue), as no specific override exists. No gradient.
- **Dark Theme**:
  - Text Color: White.
  - Background: Blue gradient (`linear-gradient(135deg, #4a90e2, #357abd)`).
  - Border: `2px solid #6ba6ff`.

### **CSS Classes Utilized (from `src/styles/globals.scss`)**

```css
.back-nav          /* Container div for the button */
.back-nav-link     /* Base style for the link/button */
.back-button       /* Applied instead of .back-nav-link when an onClick handler is used */
/* Note: Standalone '.secondary' or '.minimal' classes for variants are not currently defined globally. */
/* The component adds these as classes if the variant prop is set, but they lack global CSS rules. */
```

The `className` prop on `BackButton` can be used to add further custom styling via CSS Modules if needed for specific contexts, augmenting the base global style.

---

## 📱 Responsive Breakpoints & Typography Guidelines

The following are recommended `font-size` values for typical heading elements at various breakpoints. Developers should implement these responsive adjustments within their component-specific CSS Modules to ensure a consistent typographic scale across the application.

While `src/styles/globals.scss` defines base font sizes (e.g., via `--font-size-base`, `--font-size-lg`, etc.) and may apply default styling to `<h1>`-`<h6>` elements, specific responsive adjustments for semantic page/section/card titles as listed below should be handled in CSS Modules.

### **Recommended Sizes for Tablet (768px and below)**

- **Page Titles** (e.g., primary `<h1>` in a page component): `2rem`
- **Section Titles** (e.g., `<h2>` or important headings within a page): `1.5rem`
- **Card Titles** (e.g., `<h3>` or headings within cards): `1.2rem`
- **Card Subtitles** (e.g., secondary headings in cards): `1rem`

### **Recommended Sizes for Mobile (480px and below)**

- **Page Titles**: `1.75rem`
- **Section Titles**: `1.25rem`
- **Card Titles**: `1.1rem`
- **Card Subtitles**: `0.95rem`

**Example Implementation in a CSS Module:**

```css
/* MyPageComponent.module.css */
.pageTitle {
  font-size: var(--font-size-xl); /* Default from a variable, e.g., 2.5rem */
}

.sectionHeading {
  font-size: var(--font-size-lg); /* Default, e.g., 1.75rem */
}

/* Tablet */
@media (max-width: 768px) {
  .pageTitle {
    font-size: 2rem; /* As per guideline */
  }
  .sectionHeading {
    font-size: 1.5rem; /* As per guideline */
  }
}

/* Mobile */
@media (max-width: 480px) {
  .pageTitle {
    font-size: 1.75rem; /* As per guideline */
  }
  .sectionHeading {
    font-size: 1.25rem; /* As per guideline */
  }
}
```

---

## 🌙 **Dark Mode Support**

Dark mode (and other themes like State Farm) are fully supported throughout the application by leveraging CSS variables defined in `src/styles/globals.scss`.

- **Mechanism**: Theme-specific CSS variables are applied to the `<html>` element (e.g., `html.theme-dark`), which then cascade throughout the application.
- **Consistency**: Components styled with CSS Modules should use these variables (e.g., `color: var(--text-dark); background-color: var(--card-bg);`) to ensure they adapt correctly to the active theme.
- **No Extra CSS**: Well-structured components using these variables generally do not require additional, separate CSS rules specifically for dark mode within their own modules, as the variable values themselves change.
- **Contrast & Readability**: Theme variable values are chosen to ensure good text readability and visual appeal in all modes.

---

## ✅ **Quality Assurance Checklist**

### **Before Committing Code**

- [ ] Component-specific styles are defined in their respective `.module.css` files.
- [ ] Styling leverages global CSS variables from `src/styles/globals.scss` for colors, fonts, and theme consistency.
- [ ] No inline styles or `<style jsx>` blocks are used.
- [ ] Tested in all supported themes (light, dark, State Farm).
- [ ] Verified responsive behavior on relevant screen sizes.
- [ ] Build passes without errors (`npm run build`).
- [ ] Code is consistent with examples in `CSS_MODULES_GUIDE.md` and this document.

### **Code Review Standards**

- [ ] No hardcoded colors, font sizes, or spacing; CSS variables are used instead.
- [ ] CSS Module class names are semantic and clear.
- [ ] Styling logic is primarily within CSS Modules, not in JavaScript (except for dynamic style properties like `backgroundColor` from props).
- [ ] No unnecessary `!important` overrides.
- [ ] Proper semantic HTML structure is used.

---

## 🚀 **Benefits of This Styling System**

### **Key Advantages**

1.  **Modularity & Scope**: CSS Modules prevent global namespace collisions and keep styles component-specific.
2.  **Theming**: Centralized CSS variables in `src/styles/globals.scss` allow for easy theme management (light, dark, State Farm) and consistent application of design tokens.
3.  **Maintainability**: Styles are co-located with their components, making them easier to find, update, and manage.
4.  **Responsive Design**: Media queries within CSS Modules and `globals.scss` ensure adaptability across devices.
5.  **Performance**: CSS Modules can be optimized during the build process, and browsers can efficiently parse and apply them.

---

## 📚 **Examples**

### **Correct Implementation (Using CSS Modules and Global Variables)**

```jsx
// MyComponent.tsx
import styles from './MyComponent.module.css';

function MyComponent({ title }) {
  return (
    <div className={styles.card}>
      <h3 className={styles.cardTitle}>{title}</h3>
      <p className={styles.cardBody}>This component uses CSS Modules and global variables.</p>
    </div>
  );
}
```

```css
/* MyComponent.module.css */
.card {
  background-color: var(--card-bg); /* Variable from globals.scss */
  border: 1px solid var(--border-color); /* Variable from globals.scss */
  padding: var(--spacing-md); /* Assuming --spacing-md is defined in globals.scss */
  box-shadow: var(--card-shadow);
}

.cardTitle {
  color: var(--primary-color); /* Variable from globals.scss */
  font-size: var(--font-size-lg); /* Variable from globals.scss */
  margin-bottom: var(--spacing-sm);
}

.cardBody {
  color: var(--text-medium);
  font-size: var(--font-size-base);
}

/* Example of a responsive adjustment within the module */
@media (max-width: 768px) {
  .cardTitle {
    font-size: var(--font-size-md); /* Assuming --font-size-md is defined */
  }
}
```

### **Incorrect Implementation**

```jsx
// ❌ INCORRECT - Using inline styles
<div style={{ backgroundColor: 'blue', padding: '10px' }}>
  <h3 style={{ color: 'white' }}>Inline Styled Title</h3>
</div>

// ❌ INCORRECT - Using <style jsx>
/*
<style jsx>{`
  .card {
    background-color: blue; // Hardcoded, not using CSS variables
    padding: 10px;
  }
  .title {
    color: white;
  }
`}</style>
*/

// ❌ INCORRECT - Relying on non-existent global classes for component structure
// (e.g., <h1 className="page-title">My Component's Specific Title</h1> where .page-title
// was meant for general page headers, not specific component titles that should be in a module)
// Instead, use styles.mySpecificTitle from a CSS module.
```

---

## 🔄 **Maintenance & Updates**

### **When Adding New Components**

1.  Create a `ComponentName.module.css` file for the component.
2.  Follow the patterns outlined in this document and `CSS_MODULES_GUIDE.md`.
3.  Utilize global CSS variables for theme-related properties.
4.  Test across all supported devices and themes.
5.  Update relevant documentation if new, widely applicable styling patterns emerge.

### **When Modifying Existing Components**

1.  Ensure changes adhere to the CSS Modules-first principle.
2.  Verify that modifications do not negatively impact theme support or responsiveness.
3.  If making changes to global CSS variables in `src/styles/globals.scss`, test thoroughly across all components that might be affected.
4.  Run full regression testing for significant styling changes.

---

## 📞 **Support & Questions**

For questions about styling standards or implementation:

1.  Refer to this document and `CSS_MODULES_GUIDE.md` first.
2.  Check `src/styles/globals.scss` for defined CSS variables and base styles.
3.  Review existing components that correctly implement CSS Modules for practical examples.
4.  Ensure all styling choices maintain consistency with the established design system.

---

**Remember**: Adherence to CSS Modules and the defined global CSS variables is key to maintaining a consistent, manageable, and themeable application. Prioritize component-scoped styles and leverage the shared theme infrastructure.
