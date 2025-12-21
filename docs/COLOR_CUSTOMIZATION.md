# Mastodon Color Customization Guide

This comprehensive guide will teach you how to create custom color themes for Mastodon. We'll use an **Ocean Blue** theme as a practical example throughout this guide.

## Table of Contents

1. [Understanding the Theming System](#understanding-the-theming-system)
2. [Color Architecture](#color-architecture)
3. [Creating a Custom Theme](#creating-a-custom-theme)
4. [Ocean Blue Theme Example](#ocean-blue-theme-example)
5. [Advanced Customization](#advanced-customization)
6. [Testing Your Theme](#testing-your-theme)
7. [Deployment](#deployment)
8. [Troubleshooting](#troubleshooting)

---

## Understanding the Theming System

Mastodon uses a sophisticated two-layer theming system:

- **Flavours**: Different UI layouts (e.g., `vanilla` for standard Mastodon, `glitch` for glitch-soc features)
- **Skins**: Color schemes applied to a flavour (e.g., `default`, `mastodon-light`, `contrast`)

### How It Works

1. **CSS Custom Properties**: Modern CSS variables power the theming system
2. **Semantic Tokens**: Colors are named by purpose (e.g., `--color-text-primary`) rather than appearance (e.g., `--dark-blue`)
3. **Automatic Discovery**: New themes are automatically detected by the build system
4. **User Selection**: Users can choose their preferred theme from settings

---

## Color Architecture

### Three-Layer System

#### Layer 1: Base Palette (`_base.scss`)

27 foundational colors that serve as building blocks:

**Grays** (12 shades):
- `--color-grey-950` (darkest) through `--color-grey-50` (lightest)
- `--color-black` and `--color-white`

**Brand Colors** (Indigo by default, 6 shades):
- `--color-indigo-600` through `--color-indigo-50`

**Status Colors**:
- Red: `--color-red-500`, `--color-red-600`
- Yellow: `--color-yellow-400`, `--color-yellow-600`, `--color-yellow-700`
- Green: `--color-green-400`, `--color-green-600`

#### Layer 2: Semantic Tokens

90+ purpose-based variables organized into categories:

**Text Tokens** (~30 variables):
- Primary, secondary, tertiary text
- Brand, error, warning, success states
- On-brand, on-inverted, disabled states

**Background Tokens** (~20 variables):
- Primary, secondary, tertiary backgrounds
- Brand backgrounds (base, soft, softer)
- Status backgrounds (error, warning, success)
- Overlays and media backgrounds

**Border Tokens** (~8 variables):
- Primary borders, media borders
- Context-specific borders

**Other Tokens**:
- Shadows (dropdowns, overlays)
- Graph/chart colors

#### Layer 3: Component Styles

Components use semantic tokens, ensuring automatic theme adaptation:

```scss
.button {
  background-color: var(--color-bg-brand-base);
  color: var(--color-text-on-brand-base);
}
```

---

## Creating a Custom Theme

### Method 1: Create a New Skin (Recommended)

This is the cleanest approach for a complete custom theme.

#### Step 1: Choose Your Flavour

Decide which flavour to customize:
- `vanilla` - Standard Mastodon UI
- `glitch` - Glitch-soc enhanced UI

For this guide, we'll create themes for both flavours.

#### Step 2: Create Skin Directories

```bash
# For vanilla flavour
mkdir -p app/javascript/skins/vanilla/ocean-blue

# For glitch flavour
mkdir -p app/javascript/skins/glitch/ocean-blue
```

#### Step 3: Create Translation Files

Create `names.yml` in each directory to provide the theme name:

**`app/javascript/skins/vanilla/ocean-blue/names.yml`**:
```yaml
en:
  skins:
    vanilla:
      ocean-blue: Ocean Blue
```

**`app/javascript/skins/glitch/ocean-blue/names.yml`**:
```yaml
en:
  skins:
    glitch:
      ocean-blue: Ocean Blue
```

#### Step 4: Create Theme Stylesheet

Create `common.scss` in each directory. This is where the magic happens.

---

## Ocean Blue Theme Example

Let's create a beautiful ocean-inspired theme with deep blues, turquoise accents, and coral highlights.

### Color Palette Design

First, let's define our ocean blue color palette:

```scss
/* Ocean Blue Palette */

/* Primary Blues - Deep ocean depths */
--ocean-depth-950: #041e2e;      // Deepest ocean
--ocean-depth-900: #062a42;      // Deep water
--ocean-depth-800: #083856;      // Dark ocean
--ocean-depth-700: #0a466a;      // Ocean blue
--ocean-depth-600: #0d5885;      // Medium ocean

/* Accent Blues - Surface waters */
--ocean-surface-500: #1a7baa;    // Bright ocean
--ocean-surface-400: #2e9ed4;    // Light ocean
--ocean-surface-300: #5cb8e6;    // Sky blue
--ocean-surface-200: #8dd0f2;    // Pale blue
--ocean-surface-100: #c7e9fa;    // Lightest blue

/* Turquoise - Tropical waters */
--ocean-turquoise-600: #0d9488;  // Deep turquoise
--ocean-turquoise-500: #14b8a6;  // Turquoise
--ocean-turquoise-400: #2dd4bf;  // Light turquoise

/* Coral - Reef accents */
--ocean-coral-600: #e76f51;      // Deep coral
--ocean-coral-500: #f4a261;      // Coral
--ocean-coral-400: #fbb675;      // Light coral

/* Seafoam - Success states */
--ocean-seafoam-600: #059669;    // Deep seafoam
--ocean-seafoam-500: #10b981;    // Seafoam
--ocean-seafoam-400: #34d399;    // Light seafoam

/* Sand - Neutral tones */
--ocean-sand-100: #fef7ed;       // Light sand
--ocean-sand-200: #fde8d2;       // Sand
```

### Complete Theme Implementation

**`app/javascript/skins/vanilla/ocean-blue/common.scss`**:

```scss
@use '@/styles/common';

html {
  /* ===================================
     OCEAN BLUE THEME
     A deep ocean-inspired theme with
     turquoise accents and coral highlights
     =================================== */

  /* =====================================
     BASE PALETTE OVERRIDES
     ===================================== */

  /* Ocean Grays - Blue-tinted neutrals */
  --color-grey-950: #0a1929;
  --color-grey-900: #0f2839;
  --color-grey-850: #14354a;
  --color-grey-800: #19425b;
  --color-grey-750: #1e506c;
  --color-grey-700: #235e7d;
  --color-grey-600: #2e7a9f;
  --color-grey-500: #4a95b8;
  --color-grey-400: #6dadc9;
  --color-grey-300: #91c5db;
  --color-grey-200: #b5ddec;
  --color-grey-100: #d9f0f8;
  --color-grey-50: #ecf7fb;

  /* Ocean Brand Colors - Turquoise */
  --color-indigo-600: #0d9488;
  --color-indigo-500: #14b8a6;
  --color-indigo-400: #2dd4bf;
  --color-indigo-300: #5eead4;
  --color-indigo-200: #99f6e4;
  --color-indigo-100: #ccfbf1;
  --color-indigo-50: #f0fdfa;

  /* Status Colors - Ocean themed */
  --color-red-600: #dc2626;
  --color-red-500: #ef4444;
  --color-yellow-700: #b45309;
  --color-yellow-600: #d97706;
  --color-yellow-400: #fbbf24;
  --color-green-600: #059669;
  --color-green-400: #34d399;

  /* =====================================
     SEMANTIC TOKENS - DARK THEME
     ===================================== */

  /* TEXT TOKENS */
  --color-text-primary: #e0f2f7;              // Soft white with blue tint
  --color-text-secondary: #94c5d9;            // Muted light blue
  --color-text-tertiary: #6397b3;             // Subtle blue-gray
  --color-text-brand: #5eead4;                // Bright turquoise
  --color-text-error: #fca5a5;                // Soft coral red
  --color-text-warning: #fcd34d;              // Warm yellow
  --color-text-success: #6ee7b7;              // Seafoam green
  --color-text-disabled: #4a6b7d;             // Desaturated blue-gray
  --color-text-on-brand-base: #042f2e;        // Dark text on turquoise
  --color-text-on-error-base: #450a0a;        // Dark text on red
  --color-text-on-warning-base: #451a03;      // Dark text on yellow
  --color-text-on-success-base: #064e3b;      // Dark text on green
  --color-text-on-inverted-base: #0a1929;     // Dark text on light bg
  --color-text-on-media: #ffffff;             // White text on media
  --color-text-placeholder: #5d8aa3;          // Placeholder blue-gray

  /* BACKGROUND TOKENS */
  --color-bg-primary: #0a1929;                // Deep ocean background
  --color-bg-secondary: #0f2839;              // Slightly lighter ocean
  --color-bg-tertiary: #14354a;               // Medium ocean depth
  --color-bg-brand-base: #0d9488;             // Turquoise buttons
  --color-bg-brand-base-hover: #0f766e;       // Darker turquoise on hover
  --color-bg-brand-base-active: #115e59;      // Active turquoise
  --color-bg-brand-soft: #134e4a;             // Soft turquoise background
  --color-bg-brand-softer: #0a2e29;           // Subtle turquoise tint
  --color-bg-error-base: #dc2626;             // Error red
  --color-bg-error-soft: #450a0a;             // Soft error background
  --color-bg-error-softer: #1a0a0a;           // Subtle error tint
  --color-bg-warning-base: #d97706;           // Warning orange
  --color-bg-warning-soft: #451a03;           // Soft warning background
  --color-bg-warning-softer: #1a0f05;         // Subtle warning tint
  --color-bg-success-base: #059669;           // Success green
  --color-bg-success-soft: #064e3b;           // Soft success background
  --color-bg-success-softer: #051f1a;         // Subtle success tint
  --color-bg-overlay: rgba(10, 25, 41, 0.9);  // Modal overlay
  --color-bg-media: #0a1929;                  // Media background
  --color-bg-disabled: #19425b;               // Disabled state
  --color-bg-inverted-primary: #e0f2f7;       // Light background (inverted)
  --color-bg-inverted-secondary: #b5ddec;     // Light secondary (inverted)

  /* BORDER TOKENS */
  --color-border-primary: #2e7a9f;            // Medium ocean border
  --color-border-media: #235e7d;              // Media border
  --color-border-on-secondary: #4a95b8;       // Border on secondary bg
  --color-border-on-brand: #5eead4;           // Border on brand bg
  --color-border-on-error: #fca5a5;           // Border on error bg
  --color-border-on-warning: #fcd34d;         // Border on warning bg
  --color-border-on-success: #6ee7b7;         // Border on success bg
  --color-border-on-inverted: #6dadc9;        // Border on light bg

  /* SHADOW TOKENS */
  --color-shadow-dropdown: rgba(0, 0, 0, 0.4);
  --color-shadow-overlay-icon: rgba(10, 25, 41, 0.7);

  /* GRAPH/CHART TOKENS */
  --color-graph-stroke-primary: #14b8a6;      // Turquoise chart line
  --color-graph-stroke-warning: #fbbf24;      // Warning chart line
  --color-graph-stroke-disabled: #4a6b7d;     // Disabled chart line
  --color-graph-fill-primary: rgba(20, 184, 166, 0.2);
  --color-graph-fill-warning: rgba(251, 191, 36, 0.2);
  --color-graph-fill-disabled: rgba(74, 107, 125, 0.2);

  /* =====================================
     ADDITIONAL CUSTOMIZATIONS
     ===================================== */

  /* Accent gradients for special elements */
  --ocean-gradient-primary: linear-gradient(135deg, #0d9488 0%, #14b8a6 100%);
  --ocean-gradient-highlight: linear-gradient(135deg, #2dd4bf 0%, #5eead4 100%);

  /* Custom focus rings */
  --focus-ring-color: #5eead4;
  --focus-ring-width: 2px;

  /* Scrollbar theming */
  scrollbar-color: var(--color-indigo-500) var(--color-bg-secondary);

  &::-webkit-scrollbar-thumb {
    background-color: var(--color-indigo-500);
    border-radius: 4px;

    &:hover {
      background-color: var(--color-indigo-400);
    }
  }

  &::-webkit-scrollbar-track {
    background-color: var(--color-bg-secondary);
  }
}

/* =====================================
   COMPONENT-SPECIFIC ENHANCEMENTS
   ===================================== */

/* Enhanced focus states with ocean glow */
*:focus-visible {
  outline: var(--focus-ring-width) solid var(--focus-ring-color);
  outline-offset: 2px;
}

/* Highlighted links with ocean accent */
a {
  &:hover {
    color: var(--color-text-brand);
  }
}

/* Button hover effects */
.button {
  transition: all 0.2s ease-in-out;

  &:hover:not(:disabled) {
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(13, 148, 136, 0.3);
  }

  &:active:not(:disabled) {
    transform: translateY(0);
  }
}

/* Card elements with subtle ocean glow */
.card,
.status__wrapper,
.detailed-status,
.account__header {
  box-shadow: 0 2px 8px rgba(13, 148, 136, 0.1);

  &:hover {
    box-shadow: 0 4px 16px rgba(13, 148, 136, 0.15);
  }
}

/* Navigation with ocean theme */
.navigation-panel,
.compose-panel {
  background: linear-gradient(180deg,
    var(--color-bg-primary) 0%,
    color-mix(in srgb, var(--color-bg-primary) 95%, var(--color-indigo-600) 5%) 100%
  );
}

/* Notification badges with coral accent */
.notification__badge,
.react-notification-badge {
  background: linear-gradient(135deg, #e76f51 0%, #f4a261 100%);
  box-shadow: 0 2px 8px rgba(231, 111, 81, 0.4);
}

/* Loading indicators */
.loading-indicator {
  color: var(--color-indigo-400);
}

.loading-bar {
  background-color: var(--color-indigo-500);
}

/* Color scheme declaration */
color-scheme: dark;
```

### Creating a Light Ocean Blue Variant

For users who prefer light themes, create a complementary light version:

**`app/javascript/skins/vanilla/ocean-blue-light/common.scss`**:

```scss
@use '@/styles/common';

html {
  /* ===================================
     OCEAN BLUE LIGHT THEME
     A bright, airy ocean theme
     =================================== */

  /* =====================================
     BASE PALETTE OVERRIDES
     ===================================== */

  /* Light Ocean Grays */
  --color-grey-950: #0a1929;
  --color-grey-900: #14354a;
  --color-grey-850: #1e506c;
  --color-grey-800: #2e7a9f;
  --color-grey-750: #4a95b8;
  --color-grey-700: #6dadc9;
  --color-grey-600: #91c5db;
  --color-grey-500: #b5ddec;
  --color-grey-400: #c7e9fa;
  --color-grey-300: #d9f0f8;
  --color-grey-200: #e8f6fc;
  --color-grey-100: #f4fbfe;
  --color-grey-50: #fafeff;

  /* Ocean Brand Colors - Turquoise */
  --color-indigo-600: #0f766e;
  --color-indigo-500: #0d9488;
  --color-indigo-400: #14b8a6;
  --color-indigo-300: #2dd4bf;
  --color-indigo-200: #5eead4;
  --color-indigo-100: #99f6e4;
  --color-indigo-50: #ccfbf1;

  /* Status Colors */
  --color-red-600: #dc2626;
  --color-red-500: #ef4444;
  --color-yellow-700: #b45309;
  --color-yellow-600: #d97706;
  --color-yellow-400: #f59e0b;
  --color-green-600: #059669;
  --color-green-400: #10b981;

  /* =====================================
     SEMANTIC TOKENS - LIGHT THEME
     ===================================== */

  /* TEXT TOKENS */
  --color-text-primary: #0a1929;              // Deep ocean text
  --color-text-secondary: #14354a;            // Dark blue-gray
  --color-text-tertiary: #2e7a9f;             // Medium blue
  --color-text-brand: #0f766e;                // Dark turquoise
  --color-text-error: #b91c1c;                // Deep red
  --color-text-warning: #b45309;              // Deep orange
  --color-text-success: #047857;              // Deep green
  --color-text-disabled: #91c5db;             // Light blue-gray
  --color-text-on-brand-base: #ffffff;        // White on turquoise
  --color-text-on-error-base: #ffffff;        // White on red
  --color-text-on-warning-base: #ffffff;      // White on orange
  --color-text-on-success-base: #ffffff;      // White on green
  --color-text-on-inverted-base: #e0f2f7;     // Light text on dark
  --color-text-on-media: #ffffff;             // White on media
  --color-text-placeholder: #6dadc9;          // Blue-gray placeholder

  /* BACKGROUND TOKENS */
  --color-bg-primary: #ffffff;                // White background
  --color-bg-secondary: #f4fbfe;              // Very light blue
  --color-bg-tertiary: #e8f6fc;               // Light blue
  --color-bg-brand-base: #0d9488;             // Turquoise buttons
  --color-bg-brand-base-hover: #0f766e;       // Darker turquoise hover
  --color-bg-brand-base-active: #115e59;      // Active turquoise
  --color-bg-brand-soft: #ccfbf1;             // Soft turquoise bg
  --color-bg-brand-softer: #f0fdfa;           // Very soft turquoise
  --color-bg-error-base: #dc2626;             // Error red
  --color-bg-error-soft: #fee2e2;             // Soft error bg
  --color-bg-error-softer: #fef2f2;           // Very soft error
  --color-bg-warning-base: #d97706;           // Warning orange
  --color-bg-warning-soft: #fed7aa;           // Soft warning bg
  --color-bg-warning-softer: #fffbeb;         // Very soft warning
  --color-bg-success-base: #059669;           // Success green
  --color-bg-success-soft: #d1fae5;           // Soft success bg
  --color-bg-success-softer: #f0fdf4;         // Very soft success
  --color-bg-overlay: rgba(10, 25, 41, 0.8);  // Modal overlay
  --color-bg-media: #f4fbfe;                  // Media background
  --color-bg-disabled: #e8f6fc;               // Disabled state
  --color-bg-inverted-primary: #0a1929;       // Dark background
  --color-bg-inverted-secondary: #14354a;     // Dark secondary

  /* BORDER TOKENS */
  --color-border-primary: #b5ddec;            // Light ocean border
  --color-border-media: #c7e9fa;              // Light media border
  --color-border-on-secondary: #91c5db;       // Border on secondary
  --color-border-on-brand: #5eead4;           // Border on brand
  --color-border-on-error: #fca5a5;           // Border on error
  --color-border-on-warning: #fcd34d;         // Border on warning
  --color-border-on-success: #6ee7b7;         // Border on success
  --color-border-on-inverted: #4a95b8;        // Border on dark

  /* SHADOW TOKENS */
  --color-shadow-dropdown: rgba(10, 25, 41, 0.1);
  --color-shadow-overlay-icon: rgba(10, 25, 41, 0.5);

  /* GRAPH/CHART TOKENS */
  --color-graph-stroke-primary: #0d9488;
  --color-graph-stroke-warning: #d97706;
  --color-graph-stroke-disabled: #91c5db;
  --color-graph-fill-primary: rgba(13, 148, 136, 0.1);
  --color-graph-fill-warning: rgba(217, 119, 6, 0.1);
  --color-graph-fill-disabled: rgba(145, 197, 219, 0.1);

  /* =====================================
     CUSTOMIZATIONS
     ===================================== */

  --ocean-gradient-primary: linear-gradient(135deg, #0d9488 0%, #2dd4bf 100%);
  --ocean-gradient-highlight: linear-gradient(135deg, #5eead4 0%, #99f6e4 100%);
  --focus-ring-color: #0d9488;
  --focus-ring-width: 2px;

  scrollbar-color: var(--color-indigo-500) var(--color-bg-secondary);
}

/* Component enhancements */
*:focus-visible {
  outline: var(--focus-ring-width) solid var(--focus-ring-color);
  outline-offset: 2px;
}

.button {
  transition: all 0.2s ease-in-out;

  &:hover:not(:disabled) {
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(13, 148, 136, 0.2);
  }
}

.card,
.status__wrapper,
.detailed-status {
  box-shadow: 0 1px 4px rgba(10, 25, 41, 0.1);

  &:hover {
    box-shadow: 0 2px 8px rgba(13, 148, 136, 0.15);
  }
}

color-scheme: light;
```

### Create Names Files for Light Theme

**`app/javascript/skins/vanilla/ocean-blue-light/names.yml`**:
```yaml
en:
  skins:
    vanilla:
      ocean-blue-light: Ocean Blue (Light)
```

**`app/javascript/skins/glitch/ocean-blue-light/names.yml`**:
```yaml
en:
  skins:
    glitch:
      ocean-blue-light: Ocean Blue (Light)
```

---

## Advanced Customization

### Custom Color Utilities

Create reusable color utilities for your theme:

```scss
/* Color mixing utilities */
@function ocean-tint($color, $percentage) {
  @return color-mix(in srgb, $color $percentage, #e0f2f7);
}

@function ocean-shade($color, $percentage) {
  @return color-mix(in srgb, $color $percentage, #0a1929);
}

/* Usage */
.custom-element {
  background: ocean-tint(var(--color-indigo-500), 80%);
  border-color: ocean-shade(var(--color-indigo-400), 70%);
}
```

### Animated Backgrounds

Add subtle ocean wave animations:

```scss
/* Animated ocean waves */
@keyframes ocean-wave {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

.ocean-animated-bg {
  background: linear-gradient(
    -45deg,
    #0a1929,
    #0f2839,
    #14354a,
    #0f2839
  );
  background-size: 400% 400%;
  animation: ocean-wave 15s ease infinite;
}
```

### Seasonal Variants

Create variations for different moods:

```scss
/* Sunset Ocean - Warmer tones */
html[data-ocean-variant='sunset'] {
  --color-indigo-500: #f59e0b; // Amber
  --color-indigo-400: #fbbf24; // Yellow
  --color-text-brand: #fcd34d;
}

/* Storm Ocean - Darker, moodier */
html[data-ocean-variant='storm'] {
  --color-bg-primary: #030712; // Near black
  --color-grey-900: #0a0f1a;
  --color-text-secondary: #6b7280; // More muted
}

/* Coral Reef - Vibrant colors */
html[data-ocean-variant='reef'] {
  --color-indigo-500: #ec4899; // Pink
  --color-green-400: #f97316;  // Orange coral
}
```

### Custom Syntax Highlighting

If your Mastodon instance shows code blocks:

```scss
/* Ocean-themed syntax highlighting */
.hljs {
  background: var(--color-bg-secondary);
  color: var(--color-text-primary);
}

.hljs-keyword {
  color: #5eead4; // Turquoise
}

.hljs-string {
  color: #fbbf24; // Amber
}

.hljs-number {
  color: #f4a261; // Coral
}

.hljs-comment {
  color: var(--color-text-tertiary);
  font-style: italic;
}

.hljs-function {
  color: #2dd4bf; // Light turquoise
}
```

### Glassmorphism Effects

Add modern glassmorphism to modals and overlays:

```scss
/* Ocean glassmorphism */
.modal__container,
.dropdown-menu,
.privacy-dropdown__dropdown {
  background: rgba(10, 25, 41, 0.7);
  backdrop-filter: blur(12px) saturate(150%);
  border: 1px solid rgba(94, 234, 212, 0.1);
  box-shadow:
    0 8px 32px rgba(0, 0, 0, 0.3),
    inset 0 1px 0 rgba(94, 234, 212, 0.1);
}
```

---

## Testing Your Theme

### Local Development

1. **Start the development server**:
   ```bash
   # Terminal 1: Rails server
   bin/rails server

   # Terminal 2: Vite dev server
   bin/vite dev
   ```

2. **Access Mastodon**:
   - Navigate to `http://localhost:3000`
   - Log in to your account
   - Go to Settings → Appearance
   - Select "Ocean Blue" from the theme dropdown

3. **Live Reload**:
   - Changes to `.scss` files trigger automatic rebuilds
   - Refresh your browser to see updates
   - Use browser DevTools to inspect CSS variables

### Testing Checklist

Ensure your theme works properly across all components:

#### Visual Elements
- [ ] Text is readable in all states (primary, secondary, disabled)
- [ ] Buttons have proper contrast and hover states
- [ ] Links are distinguishable from regular text
- [ ] Status indicators (error, warning, success) are clearly visible
- [ ] Borders and dividers are visible but not overwhelming

#### Components
- [ ] Navigation panels render correctly
- [ ] Compose box is usable and attractive
- [ ] Timeline posts are readable
- [ ] Profile headers look good
- [ ] Modals and dropdowns work well
- [ ] Emoji picker is functional
- [ ] Media attachments display properly
- [ ] Notification badges are visible

#### Accessibility
- [ ] Contrast ratios meet WCAG AA standards (4.5:1 for text)
- [ ] Focus indicators are visible
- [ ] Color is not the only indicator of state
- [ ] High contrast mode still works

#### Browser Testing
- [ ] Chrome/Edge
- [ ] Firefox
- [ ] Safari
- [ ] Mobile browsers

### Contrast Testing Tools

Use these tools to verify accessibility:

```bash
# Install axe-core for accessibility testing
npm install -D @axe-core/cli

# Run accessibility audit
npx axe http://localhost:3000 --tags wcag2aa
```

Online tools:
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [Coolors Contrast Checker](https://coolors.co/contrast-checker)
- [Adobe Color Accessibility Tools](https://color.adobe.com/create/color-accessibility)

### Debug Mode

Enable CSS debugging to see which variables are being used:

```scss
/* Add to your theme for debugging */
html[data-debug='true'] {
  * {
    outline: 1px solid rgba(255, 0, 0, 0.1);
  }

  /* Show CSS variable values */
  body::before {
    content: 'Primary BG: ' var(--color-bg-primary) ' | Brand: ' var(--color-text-brand);
    position: fixed;
    bottom: 0;
    left: 0;
    background: black;
    color: white;
    padding: 8px;
    font-family: monospace;
    font-size: 12px;
    z-index: 9999;
  }
}
```

Enable with: `<html data-debug="true">`

---

## Deployment

### Production Build

1. **Build assets**:
   ```bash
   # Full production build
   RAILS_ENV=production bundle exec rails assets:precompile
   ```

2. **Verify build output**:
   ```bash
   # Check that theme files were generated
   ls -la public/packs/css/skins/*ocean-blue*
   ```

3. **Test in production mode**:
   ```bash
   RAILS_ENV=production bin/rails server
   ```

### Making Theme Available to Users

Your theme is automatically available once built. Users can select it from:

**Settings → Appearance → Site Theme**

### Setting as Default Instance Theme

Instance administrators can set your theme as the default:

1. **Via Admin UI**:
   - Go to Administration → Server Settings → Appearance
   - Set "Default Theme" to "Ocean Blue"

2. **Via Environment Variable**:
   ```bash
   # In .env.production
   DEFAULT_THEME=ocean-blue
   ```

3. **Via Rails Console**:
   ```ruby
   rails console
   Setting.default_settings['theme'] = 'ocean-blue'
   ```

### Distribution

To share your theme with others:

1. **Create a repository**:
   ```bash
   git init ocean-blue-theme
   cd ocean-blue-theme

   # Copy theme files
   mkdir -p skins/vanilla/ocean-blue
   mkdir -p skins/glitch/ocean-blue
   cp /path/to/mastodon/app/javascript/skins/vanilla/ocean-blue/* skins/vanilla/ocean-blue/
   cp /path/to/mastodon/app/javascript/skins/glitch/ocean-blue/* skins/glitch/ocean-blue/
   ```

2. **Add installation instructions**:
   ```markdown
   # Ocean Blue Theme for Mastodon

   ## Installation

   1. Copy the theme files:
      ```bash
      cp -r skins/vanilla/ocean-blue your-mastodon/app/javascript/skins/vanilla/
      cp -r skins/glitch/ocean-blue your-mastodon/app/javascript/skins/glitch/
      ```

   2. Rebuild assets:
      ```bash
      RAILS_ENV=production bundle exec rails assets:precompile
      ```

   3. Restart Mastodon
   ```

3. **Publish**:
   - Push to GitHub/GitLab
   - Share in Mastodon theming communities
   - Submit to theme collections

---

## Troubleshooting

### Theme Not Appearing in Dropdown

**Problem**: Ocean Blue doesn't show in Settings → Appearance

**Solutions**:

1. **Check file structure**:
   ```bash
   # Should see:
   app/javascript/skins/vanilla/ocean-blue/common.scss
   app/javascript/skins/vanilla/ocean-blue/names.yml
   ```

2. **Verify names.yml format**:
   ```yaml
   en:
     skins:
       vanilla:  # Must match flavour name
         ocean-blue: Ocean Blue  # Must match directory name
   ```

3. **Rebuild and restart**:
   ```bash
   # Clear cache
   rm -rf public/packs
   rm -rf tmp/cache

   # Rebuild
   bin/rails assets:precompile

   # Restart
   systemctl restart mastodon-web
   ```

### Colors Not Applying

**Problem**: Theme selected but colors unchanged

**Solutions**:

1. **Check CSS variable names**:
   - Must match exact names from `_dark.scss` or `_light.scss`
   - Case-sensitive: `--color-bg-primary` not `--color-BG-Primary`

2. **Inspect in DevTools**:
   ```javascript
   // In browser console
   getComputedStyle(document.documentElement).getPropertyValue('--color-bg-primary')
   ```

3. **Check CSS specificity**:
   ```scss
   /* Your overrides must target html element */
   html {
     --color-bg-primary: #0a1929; /* ✓ Correct */
   }

   :root {
     --color-bg-primary: #0a1929; /* ✗ May not work */
   }
   ```

4. **Clear browser cache**:
   - Hard refresh: `Ctrl+Shift+R` (Windows/Linux) or `Cmd+Shift+R` (Mac)
   - Clear site data in DevTools

### Build Failures

**Problem**: Asset compilation fails

**Solutions**:

1. **Check SCSS syntax**:
   ```bash
   # Validate SCSS
   npx sass app/javascript/skins/vanilla/ocean-blue/common.scss
   ```

2. **Common syntax errors**:
   ```scss
   /* ✗ Missing semicolon */
   --color-bg-primary: #0a1929

   /* ✓ Correct */
   --color-bg-primary: #0a1929;

   /* ✗ Invalid @use path */
   @use 'styles/common';

   /* ✓ Correct */
   @use '@/styles/common';
   ```

3. **Check import paths**:
   ```scss
   /* In skin files, always use: */
   @use '@/styles/common';

   /* Not: */
   @use '../../../styles/common';
   ```

### Contrast Issues

**Problem**: Text hard to read

**Solutions**:

1. **Use contrast checker**:
   ```
   Text color: #94c5d9
   Background: #0a1929
   Ratio: 6.8:1 ✓ (Passes WCAG AA)
   ```

2. **Adjust lightness**:
   ```scss
   /* Too low contrast */
   --color-text-secondary: #4a95b8;  /* 3.2:1 ✗ */

   /* Better */
   --color-text-secondary: #94c5d9;  /* 6.8:1 ✓ */
   ```

3. **Test with high contrast mode**:
   - Windows: Alt+Shift+Print Screen
   - macOS: System Preferences → Accessibility → Display → Increase Contrast

### Performance Issues

**Problem**: Theme causes lag or slow rendering

**Solutions**:

1. **Minimize animations**:
   ```scss
   /* Reduce animation complexity */
   @media (prefers-reduced-motion: reduce) {
     * {
       animation-duration: 0.01ms !important;
       animation-iteration-count: 1 !important;
       transition-duration: 0.01ms !important;
     }
   }
   ```

2. **Optimize gradients**:
   ```scss
   /* Heavy - multiple gradients */
   background:
     linear-gradient(45deg, ...),
     linear-gradient(90deg, ...),
     linear-gradient(135deg, ...);

   /* Lighter - single gradient */
   background: linear-gradient(135deg, #0d9488 0%, #14b8a6 100%);
   ```

3. **Reduce box-shadows**:
   ```scss
   /* Heavy */
   box-shadow:
     0 2px 4px rgba(0,0,0,0.1),
     0 4px 8px rgba(0,0,0,0.1),
     0 8px 16px rgba(0,0,0,0.1);

   /* Lighter */
   box-shadow: 0 2px 8px rgba(0,0,0,0.1);
   ```

---

## Additional Resources

### Color Theory for Themes

- **60-30-10 Rule**: 60% dominant (background), 30% secondary (surfaces), 10% accent (brand)
- **Complementary Colors**: Use color wheel opposites for accents (blue + coral)
- **Analogous Colors**: Use adjacent colors for harmony (blue + turquoise + teal)
- **Triadic Colors**: Use three evenly-spaced colors for vibrancy

### Accessibility Guidelines

- **WCAG 2.1 AA**: Minimum 4.5:1 contrast for normal text, 3:1 for large text
- **WCAG 2.1 AAA**: Minimum 7:1 contrast for normal text, 4.5:1 for large text
- **Color Independence**: Never use color alone to convey information
- **Focus Indicators**: Minimum 3:1 contrast against background

### Useful Tools

- **Color Palette Generators**:
  - [Coolors.co](https://coolors.co/)
  - [Adobe Color](https://color.adobe.com/)
  - [Paletton](https://paletton.com/)

- **Accessibility Checkers**:
  - [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
  - [Accessible Colors](https://accessible-colors.com/)
  - [Color Oracle](https://colororacle.org/) (Colorblindness simulator)

- **CSS Variable Tools**:
  - [CSS Variables Chrome Extension](https://chrome.google.com/webstore/detail/css-variables/)
  - Browser DevTools → Elements → Computed → Filter for `--color`

### Example Themes for Inspiration

Explore these color schemes to inspire your own:

- **Forest Green**: Earth tones with green accents
- **Sunset Purple**: Warm purples and oranges
- **Midnight Blue**: Deep navy with silver accents
- **Cherry Blossom**: Soft pinks and whites
- **Autumn**: Warm browns, oranges, and reds
- **Arctic**: Cool whites and ice blues
- **Desert**: Sand tones with terracotta
- **Cyberpunk**: Neon pinks and electric blues

---

## Conclusion

You've learned how to create comprehensive color themes for Mastodon using the Ocean Blue theme as an example. Key takeaways:

1. **Understand the architecture**: Base palette → Semantic tokens → Components
2. **Use semantic naming**: Name by purpose, not appearance
3. **Test thoroughly**: Check contrast, accessibility, and browser compatibility
4. **Customize thoughtfully**: Maintain usability while expressing creativity
5. **Share your work**: Help the Mastodon community with your themes

The Ocean Blue theme demonstrates how thoughtful color choices can create a cohesive, accessible, and beautiful user experience. Use it as a template to create your own unique themes!

Happy theming! 🌊

---

## Quick Reference

### File Structure
```
app/javascript/skins/
  vanilla/
    ocean-blue/
      common.scss
      names.yml
    ocean-blue-light/
      common.scss
      names.yml
  glitch/
    ocean-blue/
      common.scss
      names.yml
    ocean-blue-light/
      common.scss
      names.yml
```

### Essential CSS Variables

```scss
/* Most commonly customized */
--color-bg-primary          /* Main background */
--color-bg-secondary        /* Cards, panels */
--color-text-primary        /* Main text */
--color-text-secondary      /* Muted text */
--color-text-brand          /* Links, brand accents */
--color-bg-brand-base       /* Buttons, active states */
--color-border-primary      /* Dividers, borders */
```

### Build Commands

```bash
# Development
bin/vite dev

# Production
RAILS_ENV=production bundle exec rails assets:precompile

# Clean build
rm -rf public/packs tmp/cache && bin/rails assets:precompile
```
