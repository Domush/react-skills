# Tailwind CSS v4+ Reference Guide

Comprehensive reference for Tailwind CSS v4+ changes, CSS-native configuration, theming, dark mode, and migration from v3.

> **Source documentation:** See `documentation/tailwind-v4-*.md` files for full official docs.

---

## Architecture Changes

### No More `tailwind.config.ts`

Tailwind v4 moves **all configuration into CSS** using directives. The JavaScript config file (`tailwind.config.ts`, `tailwind.config.js`) is no longer used.

```css
/* ❌ OLD v3: tailwind.config.ts */
/* This file is no longer needed in v4 */

/* ✅ NEW v4: Everything in your CSS file */
@import "tailwindcss";

@theme {
  --color-primary: oklch(0.7 0.15 250);
  --font-display: "Inter", sans-serif;
}
```

### New Import Syntax

```css
/* ❌ v3: Old directives */
@tailwind base;
@tailwind components;
@tailwind utilities;

/* ✅ v4: Single import */
@import "tailwindcss";
```

### PostCSS Changes

```js
// ❌ v3: postcss.config.js
export default {
  plugins: {
    "postcss-import": {},     // ← remove
    tailwindcss: {},          // ← remove
    autoprefixer: {},         // ← remove
  },
};

// ✅ v4: postcss.config.js
export default {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
```

### Vite Plugin (Preferred for Vite Projects)

```ts
// ✅ v4: vite.config.ts — better performance than PostCSS
import { defineConfig } from "vite";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [
    tailwindcss(),
  ],
});
```

### Next.js Integration

For Next.js projects using Tailwind v4, the CSS file should import Tailwind directly:

```css
/* app/globals.css */
@import "tailwindcss";

@theme {
  /* Your custom theme variables */
  --color-brand: oklch(0.7 0.15 250);
  --font-sans: "Inter", ui-sans-serif, system-ui, sans-serif;
}
```

---

## Theme System (`@theme` Directive)

The `@theme` directive is the replacement for `tailwind.config.ts`. It defines theme variables as CSS custom properties that generate corresponding utility classes.

### Basic Theming

```css
@import "tailwindcss";

@theme {
  /* Colors — generates bg-*, text-*, border-*, etc. */
  --color-brand-50: oklch(0.97 0.02 250);
  --color-brand-100: oklch(0.93 0.04 250);
  --color-brand-500: oklch(0.65 0.15 250);
  --color-brand-900: oklch(0.25 0.08 250);

  /* Fonts — generates font-* utilities */
  --font-sans: "Inter", ui-sans-serif, system-ui, sans-serif;
  --font-mono: "JetBrains Mono", ui-monospace, monospace;
  --font-display: "Cal Sans", "Inter", sans-serif;

  /* Font sizes — generates text-* utilities */
  --text-tiny: 0.625rem;

  /* Spacing — generates p-*, m-*, gap-*, w-*, h-* etc. */
  --spacing-18: 4.5rem;
  --spacing-128: 32rem;

  /* Breakpoints — generates responsive variants (sm:, md:, etc.) */
  --breakpoint-xs: 30rem;
  --breakpoint-3xl: 120rem;

  /* Border radius — generates rounded-* utilities */
  --radius-pill: 9999px;

  /* Shadows — generates shadow-* utilities */
  --shadow-glow: 0 0 15px oklch(0.7 0.15 250 / 0.5);

  /* Animations — generates animate-* utilities */
  --animate-fade-in: fade-in 0.3s ease-out;
}

@keyframes fade-in {
  from { opacity: 0; transform: translateY(-4px); }
  to { opacity: 1; transform: translateY(0); }
}
```

### Theme Variable Namespaces

Each namespace maps to specific utility classes:

| Namespace | Utilities Generated | Example |
|-----------|-------------------|---------|
| `--color-*` | `bg-*`, `text-*`, `border-*`, `ring-*`, `fill-*`, `stroke-*` | `--color-mint: #00c9a7` → `bg-mint` |
| `--font-*` | `font-*` (font-family) | `--font-display: Inter` → `font-display` |
| `--text-*` | `text-*` (font-size) | `--text-tiny: 0.625rem` → `text-tiny` |
| `--font-weight-*` | `font-*` (weight) | `--font-weight-black: 900` → `font-black` |
| `--tracking-*` | `tracking-*` (letter-spacing) | `--tracking-ultra: 0.2em` → `tracking-ultra` |
| `--leading-*` | `leading-*` (line-height) | `--leading-relaxed: 1.75` → `leading-relaxed` |
| `--breakpoint-*` | Responsive variants | `--breakpoint-3xl: 120rem` → `3xl:*` |
| `--spacing-*` | `p-*`, `m-*`, `gap-*`, `w-*`, `h-*`, `inset-*` | `--spacing-18: 4.5rem` → `p-18` |
| `--radius-*` | `rounded-*` | `--radius-pill: 9999px` → `rounded-pill` |
| `--shadow-*` | `shadow-*` | `--shadow-glow: 0 0 15px ...` → `shadow-glow` |
| `--animate-*` | `animate-*` | `--animate-fade: ...` → `animate-fade` |
| `--container-*` | `@container` variants, `max-w-*` | `--container-3xl: 120rem` |

### Overriding Default Theme

```css
@import "tailwindcss";

@theme {
  /* Override default colors completely */
  --color-*: initial;  /* Clear all default colors first */

  /* Then define your own */
  --color-primary: oklch(0.65 0.18 250);
  --color-secondary: oklch(0.55 0.12 300);
  --color-surface: oklch(0.15 0.02 250);
  --color-text: oklch(0.92 0.01 250);
}
```

### Using `@theme inline`

When you want theme values available as CSS variables but DON'T want utility classes generated:

```css
@theme inline {
  /* These won't generate utility classes, just CSS variables */
  --sidebar-width: 18rem;
  --header-height: 4rem;
}
```

---

## Dark Mode

### Using the `dark:` Variant

Tailwind v4 uses `prefers-color-scheme` by default:

```html
<div class="bg-white dark:bg-gray-900">
  <h1 class="text-gray-900 dark:text-white">Hello</h1>
  <p class="text-gray-600 dark:text-gray-400">Content</p>
</div>
```

### Class-Based Dark Mode (Manual Toggle)

```css
@import "tailwindcss";

/* Use class-based dark mode instead of media query */
@variant dark (&:where(.dark, .dark *));
```

```html
<!-- Toggle dark mode by adding/removing 'dark' class on a parent -->
<html class="dark">
  <body class="bg-white dark:bg-gray-950">
    <!-- Everything inside inherits dark mode -->
  </body>
</html>
```

### Theme-Aware CSS Custom Properties

```css
@import "tailwindcss";

@theme {
  --color-surface: oklch(0.98 0.01 250);
  --color-surface-dark: oklch(0.15 0.02 250);
  --color-text: oklch(0.15 0.01 250);
  --color-text-dark: oklch(0.92 0.01 250);
}
```

```html
<div class="bg-surface dark:bg-surface-dark text-text dark:text-text-dark">
  Theme-aware content
</div>
```

---

## Custom Styles & `@layer`

### Adding Custom Utilities

```css
@import "tailwindcss";

/* Custom utilities using @utility directive */
@utility glass {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.15);
}

@utility text-gradient {
  background: linear-gradient(135deg, var(--color-primary), var(--color-secondary));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

/* Now usable as classes */
/* <div class="glass p-4 rounded-xl"> */
/* <h1 class="text-gradient text-4xl font-bold"> */
```

### Adding Custom Variants

```css
@import "tailwindcss";

/* Custom variant for hover on non-touch devices */
@variant hocus (&:hover, &:focus-visible);

/* Custom variant for data attributes */
@variant data-active (&[data-active="true"]);

/* Usage: <button class="hocus:bg-blue-600 data-active:ring-2"> */
```

### Adding Base Styles

```css
@import "tailwindcss";

/* Global base styles */
@layer base {
  html {
    font-family: var(--font-sans);
    scroll-behavior: smooth;
  }

  body {
    @apply bg-white text-gray-900 dark:bg-gray-950 dark:text-gray-100;
  }

  /* Focus visible for accessibility */
  :focus-visible {
    @apply outline-2 outline-offset-2 outline-blue-500;
  }
}
```

---

## Migration Guide: v3 → v4

### Utility Renames

| v3 Utility | v4 Utility | Notes |
|-----------|-----------|-------|
| `shadow-sm` | `shadow-xs` | Sizes shifted down by one step |
| `shadow` | `shadow-sm` | |
| `shadow-md` | `shadow-md` | (unchanged) |
| `drop-shadow-sm` | `drop-shadow-xs` | |
| `drop-shadow` | `drop-shadow-sm` | |
| `blur-sm` | `blur-xs` | |
| `blur` | `blur-sm` | |
| `backdrop-blur-sm` | `backdrop-blur-xs` | |
| `backdrop-blur` | `backdrop-blur-sm` | |
| `rounded-sm` | `rounded-xs` | |
| `rounded` | `rounded-sm` | |
| `outline-none` | `outline-hidden` | `outline-none` now literally sets `outline-style: none` |
| `ring` | `ring-3` | Default ring width changed from 3px to 1px |

### Removed Utilities

| v3 (Removed) | v4 (Replacement) |
|--------------|-----------------|
| `bg-opacity-50` | `bg-black/50` or `bg-blue-500/50` |
| `text-opacity-75` | `text-red-500/75` |
| `border-opacity-25` | `border-gray-300/25` |
| `ring-opacity-50` | `ring-blue-500/50` |
| `divide-opacity-*` | `divide-black/50` |
| `placeholder-opacity-*` | `placeholder-gray-400/50` |
| `flex-shrink-*` | `shrink-*` |
| `flex-grow-*` | `grow-*` |
| `overflow-ellipsis` | `text-ellipsis` |
| `decoration-slice` | `box-decoration-slice` |
| `decoration-clone` | `box-decoration-clone` |

### Configuration Migration

```ts
// ❌ OLD: tailwind.config.ts — DELETE THIS FILE
import type { Config } from 'tailwindcss';

const config: Config = {
  content: ['./src/**/*.{js,ts,jsx,tsx}'],
  theme: {
    extend: {
      colors: {
        brand: '#3b82f6',
        surface: '#1e293b',
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
      spacing: {
        '18': '4.5rem',
      },
      borderRadius: {
        '4xl': '2rem',
      },
    },
  },
  plugins: [],
};
export default config;
```

```css
/* ✅ NEW: All in your CSS file */
@import "tailwindcss";

@theme {
  --color-brand: #3b82f6;
  --color-surface: #1e293b;
  --font-sans: "Inter", sans-serif;
  --spacing-18: 4.5rem;
  --radius-4xl: 2rem;
}
```

### Content Detection

In v4, Tailwind **automatically detects** your content files — no `content` array needed. It scans your project for class names. You can configure source detection if needed:

```css
@import "tailwindcss";

/* Include additional source paths */
@source "../shared-ui/src";

/* Explicitly include additional file patterns */
@source "../../packages/design-system/src/**/*.tsx";
```

### Dark Mode Migration

```css
/* ❌ v3: In tailwind.config.ts */
/* darkMode: 'class' */

/* ✅ v4: In CSS */
@import "tailwindcss";
@variant dark (&:where(.dark, .dark *));
```

### Plugin Migration

```css
/* ❌ v3: In tailwind.config.ts */
/* plugins: [require('@tailwindcss/typography')] */

/* ✅ v4: In CSS */
@import "tailwindcss";
@plugin "@tailwindcss/typography";
```

---

## Complete Example: Full App Theme

```css
/* app/globals.css — Complete v4 configuration */
@import "tailwindcss";

/* Import plugins */
@plugin "@tailwindcss/typography";

/* Class-based dark mode */
@variant dark (&:where(.dark, .dark *));

/* Custom source paths */
@source "../shared-components/src";

/* Design System Theme */
@theme {
  /* Color palette */
  --color-background: oklch(0.98 0.005 250);
  --color-foreground: oklch(0.14 0.02 250);
  --color-muted: oklch(0.55 0.01 250);

  --color-primary: oklch(0.65 0.18 250);
  --color-primary-hover: oklch(0.58 0.2 250);
  --color-primary-light: oklch(0.9 0.05 250);

  --color-secondary: oklch(0.55 0.12 300);
  --color-accent: oklch(0.72 0.16 150);

  --color-destructive: oklch(0.55 0.2 25);
  --color-success: oklch(0.6 0.18 145);
  --color-warning: oklch(0.7 0.16 70);

  --color-surface: oklch(0.97 0.005 250);
  --color-surface-elevated: oklch(1 0 0);
  --color-border: oklch(0.88 0.01 250);

  /* Typography */
  --font-sans: "Inter", ui-sans-serif, system-ui, sans-serif;
  --font-mono: "JetBrains Mono", ui-monospace, monospace;
  --font-display: "Cal Sans", "Inter", sans-serif;

  --text-2xs: 0.625rem;

  /* Spacing */
  --spacing-4.5: 1.125rem;
  --spacing-18: 4.5rem;
  --spacing-88: 22rem;

  /* Border radius */
  --radius-pill: 9999px;
  --radius-card: 0.75rem;

  /* Shadows */
  --shadow-card: 0 1px 3px oklch(0 0 0 / 0.08), 0 1px 2px oklch(0 0 0 / 0.04);
  --shadow-dropdown: 0 4px 16px oklch(0 0 0 / 0.12);
  --shadow-glow: 0 0 20px oklch(0.65 0.18 250 / 0.3);

  /* Animations */
  --animate-slide-in: slide-in 0.2s ease-out;
  --animate-fade-in: fade-in 0.3s ease-out;
  --animate-scale-in: scale-in 0.15s ease-out;

  /* Breakpoints */
  --breakpoint-xs: 30rem;
  --breakpoint-3xl: 120rem;
}

/* Keyframes */
@keyframes slide-in {
  from { transform: translateY(-0.5rem); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}

@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes scale-in {
  from { transform: scale(0.95); opacity: 0; }
  to { transform: scale(1); opacity: 1; }
}

/* Custom utilities */
@utility glass {
  background: oklch(1 0 0 / 0.08);
  backdrop-filter: blur(12px);
  border: 1px solid oklch(1 0 0 / 0.12);
}

@utility text-gradient {
  background: linear-gradient(135deg, var(--color-primary), var(--color-secondary));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

/* Base styles */
@layer base {
  html {
    scroll-behavior: smooth;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  :focus-visible {
    @apply outline-2 outline-offset-2 outline-primary;
  }
}
```

Usage in components:

```html
<!-- Using custom theme colors, shadows, animations -->
<div class="bg-surface shadow-card rounded-card p-6 animate-fade-in">
  <h2 class="font-display text-2xl text-foreground">Card Title</h2>
  <p class="text-muted mt-2">Some description text</p>
  <button class="bg-primary text-white px-4 py-2 rounded-pill
                 hover:bg-primary-hover transition-colors
                 shadow-glow">
    Action
  </button>
</div>

<!-- Glass morphism card -->
<div class="glass rounded-card p-6">
  <h3 class="text-gradient text-xl font-bold">Premium Feature</h3>
</div>

<!-- Responsive with custom breakpoints -->
<div class="grid grid-cols-1 xs:grid-cols-2 md:grid-cols-3 3xl:grid-cols-6 gap-4">
  <!-- ... -->
</div>
```

---

## Upgrading Automatically

```bash
# Run the official upgrade tool
npx @tailwindcss/upgrade

# Requires Node.js 20+
```

This handles most migration automatically:
- Converts `tailwind.config.ts` to `@theme` in CSS
- Updates utility class names
- Removes `@tailwind` directives
- Updates PostCSS config
