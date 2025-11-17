# Color Customization Guide - Adding a Custom Flavor

This guide will walk you through creating a custom color theme (flavor/skin) for your Mastodon instance.

## Table of Contents

1. [Understanding the Theming System](#understanding-the-theming-system)
2. [Prerequisites](#prerequisites)
3. [Quick Start - Creating Your First Custom Theme](#quick-start---creating-your-first-custom-theme)
4. [Color Variables Reference](#color-variables-reference)
5. [Advanced Customization](#advanced-customization)
6. [Testing Your Theme](#testing-your-theme)
7. [Examples](#examples)
8. [Troubleshooting](#troubleshooting)

---

## Understanding the Theming System

Mastodon uses a **two-tier theming system**:

### Flavours (Major Variants)
Complete UI variations located in `/app/javascript/flavours/`:
- **glitch** - GlitchSoc edition with extra features
- **vanilla** - Standard Mastodon UI

### Skins (Color Schemes)
Color scheme variants within each flavour, located in `/app/javascript/skins/`:
- `default` - Dark theme (default)
- `mastodon-light` - Light theme
- `contrast` - High contrast dark theme
- `win95` - Windows 95 retro theme (vanilla only)

**This guide focuses on creating custom skins (color schemes)**, which is what most users want when customizing colors.

---

## Prerequisites

Before you begin, ensure you have:

- Access to the Mastodon codebase
- Basic knowledge of SCSS/CSS
- A code editor
- Ability to restart your Mastodon instance

---

## Quick Start - Creating Your First Custom Theme

Let's create a custom theme called "Ocean Blue" for the Glitch flavour.

### Step 1: Create the Skin Directory

```bash
mkdir -p app/javascript/skins/glitch/ocean-blue
```

### Step 2: Create the Main Stylesheet Entry Point

Create `app/javascript/skins/glitch/ocean-blue/common.scss`:

```scss
@use '@/flavours/glitch/styles/ocean-blue';
```

This file tells the system to load your custom theme styles.

### Step 3: Create Your Theme Directory

```bash
mkdir -p app/javascript/flavours/glitch/styles/ocean-blue
```

### Step 4: Define Your Color Variables

Create `app/javascript/flavours/glitch/styles/ocean-blue/variables.scss`:

```scss
@use 'sass:color';

// Import base functions (required for light themes)
@use '../functions' with (
  $darken-multiplier: 1,
  $lighten-multiplier: 1
);

// Define your custom colors
$ocean-blue: #006994;
$ocean-light: #4a9eca;
$ocean-dark: #003f5c;
$ocean-bg: #001a28;
$ocean-surface: #002b3d;

$black: #000;
$white: #fff;

// Override the default variables
@use '../variables' with (
  // Base UI colors
  $ui-base-color: $ocean-bg,
  $ui-base-lighter-color: $ocean-surface,
  $ui-primary-color: $ocean-light,
  $ui-secondary-color: lighten($ocean-light, 20%),
  $ui-highlight-color: $ocean-blue,

  // Button colors
  $ui-button-background-color: $ocean-blue,
  $ui-button-focus-background-color: $ocean-dark,

  // Text colors
  $primary-text-color: $white,
  $darker-text-color: $ocean-light,
  $highlight-text-color: lighten($ocean-blue, 15%)
);
```

### Step 5: Define CSS Custom Properties

Create `app/javascript/flavours/glitch/styles/ocean-blue/css_variables.scss`:

```scss
@use 'sass:color';
@use '../variables' as *;
@use 'variables' as *;
@use '../functions' as *;

body {
  --dropdown-border-color: #{$ocean-surface};
  --dropdown-background-color: #{$ocean-bg};
  --modal-border-color: #{$ocean-surface};
  --modal-background-color: #{rgba($ocean-bg, 0.9)};
  --background-border-color: #{$ocean-surface};
  --background-color: #{$ocean-bg};
  --background-color-tint: #{rgba($ocean-bg, 0.8)};
  --background-filter: blur(10px);
  --surface-variant-background-color: #{$ocean-surface};
  --surface-border-color: #{$ocean-light};
  --on-surface-color: #{color.adjust($ocean-light, $alpha: -0.65)};
}
```

### Step 6: Import the Styles

Create `app/javascript/flavours/glitch/styles/ocean-blue.scss`:

```scss
@use 'ocean-blue/variables';
@use 'ocean-blue/css_variables';
@use 'application';
```

### Step 7: Create Localized Names

Create `app/javascript/skins/glitch/ocean-blue/names.yml`:

```yml
en:
  skins:
    glitch:
      ocean-blue: Ocean Blue
es:
  skins:
    glitch:
      ocean-blue: Azul Océano
fr:
  skins:
    glitch:
      ocean-blue: Bleu Océan
de:
  skins:
    glitch:
      ocean-blue: Ozean Blau
ja:
  skins:
    glitch:
      ocean-blue: オーシャンブルー
```

### Step 8: Compile and Test

```bash
# Compile the assets
RAILS_ENV=production bundle exec rails assets:precompile

# Or for development
yarn build:dev

# Restart your Mastodon instance
systemctl restart mastodon-web
systemctl restart mastodon-streaming
systemctl restart mastodon-sidekiq
```

### Step 9: Select Your Theme

1. Log into your Mastodon account
2. Go to **Preferences** → **Appearance**
3. Under **Site theme**, select "Ocean Blue"
4. Save changes

---

## Color Variables Reference

### Core Brand Colors

```scss
// Base brand colors from Mastodon
$blurple-600: #563acc;  // Darker brand purple
$blurple-500: #6364ff;  // Main brand purple
$blurple-400: #7477fd;  // Medium brand purple
$blurple-300: #858afa;  // Light brand purple
```

### UI Background Colors

```scss
$ui-base-color              // Main background (darkest)
                            // Default: hsl(240deg, 16%, 19%)

$ui-base-lighter-color      // Secondary backgrounds (lighter)
                            // Default: 26% lighter than base

$ui-primary-color           // Tertiary backgrounds
                            // Default: hsl(240deg, 29%, 70%)

$ui-secondary-color         // Lightest UI backgrounds
                            // Default: hsl(255deg, 25%, 88%)
```

### Accent Colors

```scss
$ui-highlight-color         // Main accent color (links, active states)
                            // Default: $blurple-500
```

### Button Colors

```scss
$ui-button-background-color               // Primary button background
                                          // Default: $blurple-500

$ui-button-focus-background-color         // Primary button hover/focus
                                          // Default: $blurple-600

$ui-button-focus-outline-color            // Button focus outline
                                          // Default: $blurple-400

$ui-button-secondary-color                // Secondary button text
$ui-button-secondary-border-color         // Secondary button border
$ui-button-secondary-focus-border-color   // Secondary button focus

$ui-button-tertiary-color                 // Tertiary button text
$ui-button-tertiary-border-color          // Tertiary button border
$ui-button-tertiary-focus-background-color

$ui-button-destructive-background-color         // Delete/remove buttons
$ui-button-destructive-focus-background-color   // Default: red
```

### Text Colors

```scss
$primary-text-color         // Main text color
                            // Default: white (dark themes)

$darker-text-color          // Secondary text (less emphasis)
                            // Default: $ui-primary-color

$dark-text-color            // Tertiary text
                            // Default: $ui-base-lighter-color

$highlight-text-color       // Accent text (links)
                            // Default: 8% lighter than $ui-highlight-color

$secondary-text-color       // UI secondary text
                            // Default: $ui-secondary-color
```

### Inverted Colors (for light backgrounds)

```scss
$inverted-text-color        // Text on light backgrounds
$lighter-text-color         // Lighter inverted text
$light-text-color           // Light inverted text
```

### Semantic Colors

```scss
$success-green              // Success states (e.g., "Posted!")
                            // Default: #79bd9a

$error-red                  // Error states
                            // Default: #df405a

$warning-red                // Warning states
                            // Default: #ff5050

$gold-star                  // Favorites/bookmarks
                            // Default: #ca8f04
```

### CSS Custom Properties (Modern Variables)

These are set in your `css_variables.scss` file:

```scss
--background-color                  // Main page background
--background-color-tint             // Translucent background
--background-filter                 // Backdrop filter blur
--surface-variant-background-color  // Card/panel backgrounds
--surface-border-color              // Border colors
--dropdown-background-color         // Dropdown menus
--dropdown-border-color             // Dropdown borders
--modal-background-color            // Modal dialogs
--modal-border-color                // Modal borders
--on-surface-color                  // Text on surfaces
--input-background-color            // Form input backgrounds
--input-placeholder-color           // Placeholder text
```

---

## Advanced Customization

### Creating a Light Theme

Light themes require inverting the darken/lighten functions:

```scss
@use '../functions' with (
  $darken-multiplier: 1,
  $lighten-multiplier: -1  // Inverted!
);

@use '../variables' with (
  $ui-base-color: hsl(255deg, 25%, 88%),  // Light background
  $primary-text-color: $black,             // Dark text
  $darker-text-color: hsl(240deg, 16%, 19%)
);
```

### Creating a High Contrast Theme

Focus on color differentiation and brightness:

```scss
@use '../variables' with (
  $ui-highlight-color: hsl(240deg, 100%, 69%),  // Brighter
  $darker-text-color: lighten($ui-primary-color, 20%),
  $ui-button-focus-outline: solid 3px $ui-button-focus-outline-color  // Thicker
);
```

### Customizing Specific Components

You can override specific component styles in your main theme file:

Create `app/javascript/flavours/glitch/styles/ocean-blue/components.scss`:

```scss
@use '../variables' as *;
@use 'variables' as *;

// Custom compose box styling
.compose-form {
  .autosuggest-textarea__textarea {
    background: $ocean-dark;
    color: $white;
  }
}

// Custom notification styling
.notification {
  border-left: 4px solid $ocean-blue;
}

// Custom status card styling
.status__wrapper {
  border-bottom: 1px solid $ocean-surface;
}
```

Then import it in your main theme file:

```scss
// In ocean-blue.scss
@use 'ocean-blue/variables';
@use 'ocean-blue/css_variables';
@use 'application';
@use 'ocean-blue/components';  // Add this
```

### Supporting System Dark/Light Mode

Users can select "System default" which auto-switches based on OS preferences. Your theme will automatically work with this if properly configured.

---

## Testing Your Theme

### Development Testing

```bash
# Watch mode for rapid development
yarn build:dev --watch

# In another terminal, start Rails
foreman start
```

### Production Testing

```bash
# Full production build
RAILS_ENV=production bundle exec rails assets:precompile

# Test the compiled assets
ls -la public/packs
```

### Visual Testing Checklist

Test these areas with your new theme:

- [ ] **Home timeline** - Status cards, compose box
- [ ] **Notifications** - Different notification types
- [ ] **Compose form** - Text area, buttons, emoji picker
- [ ] **Settings page** - Forms, inputs, buttons
- [ ] **Profile page** - Header, bio, posts
- [ ] **Modals** - Media viewer, compose modal
- [ ] **Dropdowns** - Action menus
- [ ] **Buttons** - Primary, secondary, destructive
- [ ] **Dark/light mode switching** (if applicable)
- [ ] **Mobile view** - Responsive design

### Browser Testing

Test in multiple browsers:
- Chrome/Chromium
- Firefox
- Safari (if available)
- Mobile browsers

---

## Examples

### Example 1: Warm Sunset Theme

```scss
// variables.scss
$sunset-orange: #ff6b35;
$sunset-pink: #f7931e;
$sunset-purple: #c92c6d;
$sunset-bg: #1a0a0f;

@use '../variables' with (
  $ui-base-color: $sunset-bg,
  $ui-highlight-color: $sunset-orange,
  $ui-button-background-color: $sunset-pink,
  $primary-text-color: #ffe0d4,
  $success-green: #ffb347
);
```

### Example 2: Cyberpunk Neon

```scss
// variables.scss
$cyber-pink: #ff2a6d;
$cyber-blue: #05d9e8;
$cyber-purple: #d1f7ff;
$cyber-bg: #01012b;

@use '../variables' with (
  $ui-base-color: $cyber-bg,
  $ui-base-lighter-color: #05011f,
  $ui-highlight-color: $cyber-pink,
  $ui-button-background-color: $cyber-blue,
  $primary-text-color: $cyber-purple,
  $highlight-text-color: $cyber-pink
);
```

### Example 3: Forest Green

```scss
// variables.scss
$forest-dark: #0d1b0e;
$forest-green: #1e5128;
$forest-light: #4e9f3d;
$forest-bright: #8bc34a;

@use '../variables' with (
  $ui-base-color: $forest-dark,
  $ui-base-lighter-color: $forest-green,
  $ui-highlight-color: $forest-light,
  $ui-button-background-color: $forest-bright,
  $success-green: $forest-bright,
  $primary-text-color: #d8e9a8
);
```

### Example 4: Minimal Grayscale

```scss
// variables.scss
$gray-100: #f5f5f5;
$gray-200: #e0e0e0;
$gray-600: #757575;
$gray-900: #212121;

@use '../variables' with (
  $ui-base-color: $gray-900,
  $ui-base-lighter-color: lighten($gray-900, 10%),
  $ui-highlight-color: $gray-600,
  $ui-button-background-color: $gray-600,
  $primary-text-color: $gray-100,
  $darker-text-color: $gray-200
);
```

---

## Troubleshooting

### Theme Not Appearing in Settings

**Problem:** Your new theme doesn't show up in the theme selector.

**Solutions:**
1. Check the `names.yml` file exists and has correct YAML syntax
2. Restart your Mastodon instance completely
3. Clear Rails cache: `rails cache:clear`
4. Verify file structure matches exactly:
   ```
   app/javascript/skins/glitch/your-theme/
   ├── common.scss
   └── names.yml
   ```

### Compilation Errors

**Problem:** Assets fail to compile with SCSS errors.

**Solutions:**
1. Check for syntax errors in your `.scss` files
2. Ensure all `@use` statements come before other code
3. Verify variable names match exactly (case-sensitive)
4. Check that you're using `@use` not `@import` (deprecated)

### Colors Not Applying

**Problem:** Theme loads but colors look wrong or default.

**Solutions:**
1. Clear browser cache (hard refresh: Ctrl+Shift+R or Cmd+Shift+R)
2. Check that `css_variables.scss` is properly imported
3. Verify color values are valid CSS (use hex, rgb, or hsl)
4. Check browser console for CSS errors
5. Ensure you're overriding the right variables

### Performance Issues

**Problem:** Page loads slowly or feels sluggish.

**Solutions:**
1. Avoid complex calculations in SCSS
2. Don't use too many `lighten()`/`darken()` operations
3. Prefer CSS custom properties for dynamic values
4. Minimize custom component overrides

### Mobile Layout Broken

**Problem:** Theme works on desktop but breaks on mobile.

**Solutions:**
1. Test with responsive design mode in browser dev tools
2. Don't override layout-related properties, only colors
3. Check that your CSS custom properties work on mobile browsers
4. Avoid fixed widths or heights

### Theme Looks Different in Production

**Problem:** Development looks good but production build differs.

**Solutions:**
1. Ensure `RAILS_ENV=production` when compiling
2. Clear old compiled assets: `rails assets:clobber`
3. Recompile: `RAILS_ENV=production rails assets:precompile`
4. Check for caching issues (CDN, browser, server)
5. Verify file permissions on production server

---

## File Structure Summary

```
app/javascript/
├── skins/
│   └── glitch/
│       └── your-theme/
│           ├── common.scss          # Entry point (required)
│           └── names.yml            # Localized names (required)
│
└── flavours/
    └── glitch/
        └── styles/
            ├── your-theme.scss      # Main import file (required)
            └── your-theme/
                ├── variables.scss   # SCSS variables (required)
                └── css_variables.scss  # CSS custom properties (required)
```

---

## Additional Resources

### Relevant Files to Study

- `/app/javascript/flavours/glitch/styles/_variables.scss` - All default variables
- `/app/javascript/flavours/glitch/styles/mastodon-light/` - Light theme example
- `/app/javascript/flavours/glitch/styles/contrast/` - High contrast example
- `/app/javascript/styles/win95.scss` - Complete custom theme example

### System Files (Don't Modify)

- `/app/lib/themes.rb` - Theme registry (auto-discovers themes)
- `/app/controllers/concerns/theming_concern.rb` - Theme selection logic
- `/app/helpers/theme_helper.rb` - Theme rendering helpers

### Build Commands Reference

```bash
# Development build (faster, includes source maps)
yarn build:dev

# Production build (optimized, minified)
RAILS_ENV=production bundle exec rails assets:precompile

# Clear compiled assets
bundle exec rails assets:clobber

# Clear Rails cache
bundle exec rails cache:clear

# Restart services (adjust for your setup)
systemctl restart mastodon-web
systemctl restart mastodon-streaming
systemctl restart mastodon-sidekiq
```

---

## Best Practices

1. **Start with an existing theme** - Copy and modify rather than create from scratch
2. **Use semantic naming** - Name colors by function, not appearance (e.g., `$primary-bg` not `$dark-blue`)
3. **Test accessibility** - Ensure sufficient contrast ratios (WCAG AA minimum: 4.5:1)
4. **Version control** - Keep your theme in git for easy updates
5. **Document your colors** - Add comments explaining color choices
6. **Test thoroughly** - Check all UI states (hover, focus, active, disabled)
7. **Consider colorblindness** - Don't rely solely on color to convey information
8. **Use CSS custom properties** - For values that might change dynamically
9. **Keep it simple** - Don't override too many defaults initially
10. **Follow the existing structure** - Maintain consistency with built-in themes

---

## Contributing Your Theme

If you create an amazing theme, consider contributing it back to the community:

1. Test thoroughly across browsers and devices
2. Add translations for common languages in `names.yml`
3. Create a pull request to the Mastodon/GlitchSoc repository
4. Include screenshots showing your theme
5. Document any special features or accessibility considerations

---

## License

This guide is provided as documentation for the Mastodon project. Follow the same license as the main Mastodon project (AGPL-3.0) when creating and distributing custom themes.

---

## Questions or Issues?

- Check the [Mastodon documentation](https://docs.joinmastodon.org/)
- Visit the [GlitchSoc repository](https://github.com/glitch-soc/mastodon)
- Ask in the Mastodon community forums
- Review existing theme implementations for examples

Happy theming!
