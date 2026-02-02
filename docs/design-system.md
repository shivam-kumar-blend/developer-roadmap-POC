# Design System

## Overview

The roadmap.sh design system is built on **TailwindCSS 4.x** with custom configurations and design tokens. The system emphasizes clarity, accessibility, and performance.

## Design Principles

### 1. Clarity First
- Clear visual hierarchy
- Readable typography
- Consistent spacing
- Obvious interactive elements

### 2. Performance
- Minimal CSS footprint
- JIT compilation
- Utility-first approach
- No unused styles

### 3. Accessibility
- WCAG 2.1 AA compliant
- Keyboard navigable
- Screen reader friendly
- Color contrast ratios > 4.5:1

### 4. Consistency
- Reusable components
- Consistent naming
- Predictable behavior
- Standard patterns

## Color System

### Primary Colors

The roadmap.sh color palette emphasizes blues and grays for a professional, tech-focused aesthetic.

#### Brand Colors

```css
--color-primary: #000000;      /* Primary black */
--color-background: #ffffff;   /* White background */
```

#### Semantic Colors

**Success** (Green):
```css
--color-success-50: #f0fdf4;
--color-success-500: #22c55e;
--color-success-600: #16a34a;
```

**Error** (Red):
```css
--color-error-50: #fef2f2;
--color-error-500: #ef4444;
--color-error-600: #dc2626;
```

**Warning** (Orange):
```css
--color-warning-50: #fff7ed;
--color-warning-500: #f97316;
--color-warning-600: #ea580c;
```

**Info** (Blue):
```css
--color-info-50: #eff6ff;
--color-info-500: #3b82f6;
--color-info-600: #2563eb;
```

#### Neutral Grays

```css
--color-gray-50: #f9fafb;
--color-gray-100: #f3f4f6;
--color-gray-200: #e5e7eb;
--color-gray-300: #d1d5db;
--color-gray-400: #9ca3af;
--color-gray-500: #6b7280;
--color-gray-600: #4b5563;
--color-gray-700: #374151;
--color-gray-800: #1f2937;
--color-gray-900: #111827;
```

### Usage Guidelines

**Text Colors**:
- Primary text: `text-gray-900`
- Secondary text: `text-gray-600`
- Disabled text: `text-gray-400`
- Links: `text-blue-600 hover:text-blue-700`

**Backgrounds**:
- Page background: `bg-white`
- Card/container: `bg-white`
- Hover states: `hover:bg-gray-50`
- Active states: `bg-gray-100`

**Borders**:
- Default: `border-gray-200`
- Focus: `border-blue-500`
- Error: `border-red-500`

## Typography

### Font Family

**Primary Font**: System font stack for optimal performance

```css
font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 
             'Helvetica Neue', Arial, sans-serif;
```

**Benefits**:
- No web font download
- Instant rendering
- Native look and feel
- Excellent cross-platform support

### Font Sizes

Tailwind default scale with semantic naming:

| Class | Size | Use Case |
|-------|------|----------|
| `text-xs` | 12px | Small labels, captions |
| `text-sm` | 14px | Body text (secondary) |
| `text-base` | 16px | Body text (primary) |
| `text-lg` | 18px | Large body text |
| `text-xl` | 20px | Section headings |
| `text-2xl` | 24px | Page subheadings |
| `text-3xl` | 30px | Page headings |
| `text-4xl` | 36px | Hero headings |
| `text-5xl` | 48px | Large hero |

### Font Weights

| Class | Weight | Use Case |
|-------|--------|----------|
| `font-normal` | 400 | Body text |
| `font-medium` | 500 | Emphasis, labels |
| `font-semibold` | 600 | Headings, buttons |
| `font-bold` | 700 | Strong emphasis |

### Line Heights

| Class | Height | Use Case |
|-------|--------|----------|
| `leading-tight` | 1.25 | Headings |
| `leading-snug` | 1.375 | Short paragraphs |
| `leading-normal` | 1.5 | Body text |
| `leading-relaxed` | 1.625 | Long-form content |

### Typography Examples

**Page Heading**:
```html
<h1 class="text-3xl font-bold text-gray-900">
  Frontend Developer Roadmap
</h1>
```

**Section Heading**:
```html
<h2 class="text-xl font-semibold text-gray-900">
  Getting Started
</h2>
```

**Body Text**:
```html
<p class="text-base text-gray-600 leading-normal">
  Learn the fundamentals of web development...
</p>
```

**Label**:
```html
<label class="text-sm font-medium text-gray-700">
  Email Address
</label>
```

## Spacing System

Tailwind's spacing scale (0.25rem increments):

| Class | Size | Pixels (16px base) |
|-------|------|-----|
| `p-1` / `m-1` | 0.25rem | 4px |
| `p-2` / `m-2` | 0.5rem | 8px |
| `p-3` / `m-3` | 0.75rem | 12px |
| `p-4` / `m-4` | 1rem | 16px |
| `p-5` / `m-5` | 1.25rem | 20px |
| `p-6` / `m-6` | 1.5rem | 24px |
| `p-8` / `m-8` | 2rem | 32px |
| `p-10` / `m-10` | 2.5rem | 40px |
| `p-12` / `m-12` | 3rem | 48px |
| `p-16` / `m-16` | 4rem | 64px |

### Spacing Guidelines

**Component Padding**:
- Small components: `p-2` or `p-3`
- Medium components: `p-4` or `p-6`
- Large containers: `p-8` or `p-12`

**Margins**:
- Between elements: `mb-4` or `mb-6`
- Between sections: `mb-8` or `mb-12`
- Page sections: `mb-16` or `mb-20`

**Gap** (for Flexbox/Grid):
- Tight spacing: `gap-2`
- Normal spacing: `gap-4`
- Loose spacing: `gap-6`

## Responsive Breakpoints

```css
/* Mobile-first approach */
sm:  640px   /* Small tablets and large phones */
md:  768px   /* Tablets */
lg:  1024px  /* Laptops */
xl:  1280px  /* Desktops */
2xl: 1536px  /* Large desktops */
```

### Responsive Design Patterns

**Mobile-first**:
```html
<!-- Stack on mobile, row on desktop -->
<div class="flex flex-col md:flex-row gap-4">
  <div class="w-full md:w-1/2">Left</div>
  <div class="w-full md:w-1/2">Right</div>
</div>
```

**Hide on mobile**:
```html
<div class="hidden md:block">
  Desktop only content
</div>
```

**Responsive text sizes**:
```html
<h1 class="text-2xl md:text-3xl lg:text-4xl">
  Responsive heading
</h1>
```

## Components

### Buttons

#### Primary Button

```html
<button class="rounded-md bg-blue-600 px-4 py-2 text-white hover:bg-blue-700 focus:outline-hidden focus:ring-2 focus:ring-blue-500">
  Click Me
</button>
```

#### Secondary Button

```html
<button class="rounded-md border border-gray-300 bg-white px-4 py-2 text-gray-700 hover:bg-gray-50 focus:outline-hidden focus:ring-2 focus:ring-blue-500">
  Cancel
</button>
```

#### Danger Button

```html
<button class="rounded-md bg-red-600 px-4 py-2 text-white hover:bg-red-700 focus:outline-hidden focus:ring-2 focus:ring-red-500">
  Delete
</button>
```

#### Button Sizes

**Small**:
```html
<button class="rounded-md bg-blue-600 px-3 py-1.5 text-sm text-white">
  Small Button
</button>
```

**Large**:
```html
<button class="rounded-md bg-blue-600 px-6 py-3 text-lg text-white">
  Large Button
</button>
```

### Form Inputs

#### Text Input

```html
<input 
  type="text"
  class="w-full rounded-md border border-gray-300 px-3 py-2 focus:border-blue-500 focus:outline-hidden focus:ring-2 focus:ring-blue-500"
  placeholder="Enter text"
/>
```

#### Input with Label

```html
<div class="space-y-1">
  <label class="text-sm font-medium text-gray-700">
    Email Address
  </label>
  <input 
    type="email"
    class="w-full rounded-md border border-gray-300 px-3 py-2 focus:border-blue-500 focus:outline-hidden focus:ring-2 focus:ring-blue-500"
  />
</div>
```

#### Input with Error

```html
<div class="space-y-1">
  <label class="text-sm font-medium text-gray-700">
    Email Address
  </label>
  <input 
    type="email"
    class="w-full rounded-md border border-red-500 px-3 py-2 focus:border-red-500 focus:outline-hidden focus:ring-2 focus:ring-red-500"
  />
  <p class="text-sm text-red-600">
    Invalid email address
  </p>
</div>
```

### Cards

#### Basic Card

```html
<div class="rounded-lg border border-gray-200 bg-white p-6 shadow-sm">
  <h3 class="text-lg font-semibold text-gray-900">Card Title</h3>
  <p class="mt-2 text-gray-600">Card content goes here</p>
</div>
```

#### Hoverable Card

```html
<div class="rounded-lg border border-gray-200 bg-white p-6 shadow-sm hover:shadow-md hover:border-gray-300 transition-all cursor-pointer">
  <h3 class="text-lg font-semibold text-gray-900">Interactive Card</h3>
</div>
```

### Badges

```html
<!-- Success -->
<span class="inline-flex rounded-full bg-green-100 px-2 py-1 text-xs font-medium text-green-700">
  Completed
</span>

<!-- Warning -->
<span class="inline-flex rounded-full bg-orange-100 px-2 py-1 text-xs font-medium text-orange-700">
  In Progress
</span>

<!-- Info -->
<span class="inline-flex rounded-full bg-blue-100 px-2 py-1 text-xs font-medium text-blue-700">
  New
</span>
```

### Loading States

```html
<!-- Spinner -->
<div class="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600"></div>

<!-- Skeleton loader -->
<div class="animate-pulse space-y-4">
  <div class="h-4 bg-gray-200 rounded w-3/4"></div>
  <div class="h-4 bg-gray-200 rounded w-1/2"></div>
</div>
```

## Shadows

```css
shadow-xs:   0 1px 2px 0 rgba(0, 0, 0, 0.05)
shadow-sm:   0 1px 3px 0 rgba(0, 0, 0, 0.1)
shadow:      0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06)
shadow-md:   0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06)
shadow-lg:   0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05)
```

**Usage**:
- Cards: `shadow-sm` or `shadow`
- Modals: `shadow-lg` or `shadow-xl`
- Tooltips: `shadow-xs`

## Border Radius

```css
rounded-none: 0px
rounded-sm:   0.125rem (2px)
rounded:      0.25rem  (4px)
rounded-md:   0.375rem (6px)
rounded-lg:   0.5rem   (8px)
rounded-xl:   0.75rem  (12px)
rounded-2xl:  1rem     (16px)
rounded-full: 9999px
```

**Usage**:
- Buttons: `rounded-md`
- Cards: `rounded-lg`
- Input fields: `rounded-md`
- Badges: `rounded-full`
- Avatars: `rounded-full`

## Animations

### Custom Animations

Defined in `src/styles/global.css`:

```css
@keyframes fade-slide-up {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-slide-up {
  animation: fade-slide-up 0.3s ease-out;
}
```

### Transition Classes

```html
<!-- Smooth transitions -->
<div class="transition-all duration-200 ease-in-out">
  Content
</div>

<!-- Hover transition -->
<button class="hover:bg-gray-100 transition-colors duration-150">
  Hover me
</button>
```

## Z-Index Scale

```css
z-0:     0
z-10:    10
z-20:    20
z-30:    30
z-40:    40
z-50:    50
z-9999:  9999    /* Modals, toasts */
```

**Layer Guidelines**:
- Base content: `z-0` (default)
- Dropdowns: `z-10`
- Fixed headers: `z-40`
- Modals: `z-50`
- Toasts: `z-9999`

## Iconography

### Icon Library

Uses **Lucide React** icons:

```tsx
import { CheckIcon, XIcon, AlertCircle } from 'lucide-react';

<CheckIcon className="h-5 w-5" />
```

### Custom Icons

Located in `src/components/ReactIcons/`:

```tsx
import { CheckIcon } from './ReactIcons/CheckIcon';

<CheckIcon additionalClasses="h-5 w-5 text-green-500" />
```

### Icon Sizes

| Class | Size | Use Case |
|-------|------|----------|
| `h-4 w-4` | 16px | Small icons in text |
| `h-5 w-5` | 20px | Standard icons |
| `h-6 w-6` | 24px | Large icons |
| `h-8 w-8` | 32px | Hero icons |

## Layout Patterns

### Container

```html
<div class="container mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
  Content
</div>
```

### Two-Column Layout

```html
<div class="grid grid-cols-1 gap-6 lg:grid-cols-2">
  <div>Column 1</div>
  <div>Column 2</div>
</div>
```

### Sidebar Layout

```html
<div class="flex">
  <aside class="w-64 shrink-0">
    Sidebar
  </aside>
  <main class="flex-1">
    Main content
  </main>
</div>
```

## Accessibility

### Focus States

Always include visible focus indicators:

```html
<button class="focus:outline-hidden focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
  Accessible Button
</button>
```

### Screen Reader Only Text

```html
<span class="sr-only">
  Text for screen readers only
</span>
```

### ARIA Labels

```html
<button aria-label="Close dialog">
  <XIcon className="h-5 w-5" />
</button>
```

### Keyboard Navigation

Ensure all interactive elements are keyboard accessible:
- Tab through focusable elements
- Enter/Space to activate buttons
- Escape to close modals
- Arrow keys for navigation

## Dark Mode

⚠️ **Not Currently Implemented**

To add dark mode support:

1. Enable in Tailwind config:
```js
module.exports = {
  darkMode: 'class',
  // ...
}
```

2. Add dark mode styles:
```html
<div class="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
  Content
</div>
```

## Best Practices

### Do's ✅

- Use Tailwind utility classes
- Follow mobile-first responsive design
- Maintain consistent spacing
- Use semantic color names
- Test with keyboard navigation
- Ensure color contrast ratios
- Keep components reusable

### Don'ts ❌

- Don't use inline styles
- Don't create custom CSS unless necessary
- Don't use arbitrary values excessively (e.g., `w-[347px]`)
- Don't override Tailwind defaults without good reason
- Don't ignore accessibility
- Don't use `!important` to fix specificity issues

## Tailwind Configuration

Located in `tailwind.config.cjs`:

```javascript
module.exports = {
  content: ['./src/**/*.{astro,html,js,jsx,md,mdx,svelte,ts,tsx,vue}'],
  theme: {
    extend: {
      // Custom theme extensions
    },
  },
  plugins: [
    require('@tailwindcss/typography'),
    require('tailwind-scrollbar'),
  ],
}
```

## Resources

- **Tailwind CSS Documentation**: https://tailwindcss.com/docs
- **Lucide Icons**: https://lucide.dev
- **Radix UI**: https://www.radix-ui.com/primitives/docs/overview/introduction
- **Accessibility**: https://www.w3.org/WAI/WCAG21/quickref/

---

For component usage, see [Component Catalog](./components/README.md).

For development guide, see [Developer Guide](./personas/developer-guide.md).
