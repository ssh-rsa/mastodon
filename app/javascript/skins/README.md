# Creating custom color themes (skins)

This guide explains how the theming system works and walks through creating
your own color theme from scratch, using the bundled **Ocean Blue** skin as a
worked example.

## Concepts

Three independent settings combine to produce what a user sees:

| Concept | What it is | Where users pick it |
| --- | --- | --- |
| **Flavour** | An entire frontend (JS + CSS). This fork ships `glitch` and `vanilla`. | Settings → Flavours |
| **Skin** | A stylesheet for a flavour. This is what a color theme is. | Settings → Flavours → *(flavour)*, shown once a flavour has more than one skin |
| **Color scheme** | Light, dark, or automatic (follow the OS). Applied *on top of* whatever skin is active. | Settings → Preferences → Appearance |

The key consequence: **a single skin should support both light and dark**.
You do not create separate "my-theme-light" and "my-theme-dark" skins; you
create one skin whose colors respond to the user's light/dark preference.

## How colors work: design tokens

All colors in the UI are CSS custom properties ("design tokens"), defined in
`app/javascript/styles/mastodon/tokens/` (the glitch flavour has an identical
copy under `app/javascript/flavours/glitch/styles/mastodon/tokens/`). There
are two layers:

1. **Palette ramps** (`tokens/theme/_base.scss`), set on `html` — raw colors
   in 11 steps from 50 (lightest) to 950 (darkest):
   - `--color-grey-*` — neutrals: backgrounds, borders, body text
   - `--color-indigo-*` — the brand/accent color: buttons, links, highlights
   - `--color-red-*`, `--color-yellow-*`, `--color-green-*` — error, warning,
     and success states

2. **Semantic tokens** (`tokens/theme/_dark.scss` and `_light.scss`) — names
   like `--color-text-primary`, `--color-bg-secondary`,
   `--color-border-brand` that components actually use. Each scheme maps
   them to different palette steps, e.g. dark uses
   `--color-bg-primary: var(--color-grey-950)` while light uses
   `--color-bg-primary: var(--color-white)`.

The scheme is selected by a `data-color-scheme="light|dark"` attribute on
`<html>` (absent = dark, and in "automatic" mode the client sets it from the
OS preference). High-contrast mode additionally sets `data-contrast="high"`.

Because every semantic token derives from the palette ramps, **overriding
just the grey and indigo ramps recolors the entire UI — in both light and
dark — while preserving all the carefully tuned contrast relationships.**
That is the recommended approach, and it is how the Ocean Blue skin works.

## How skins are discovered

A skin is any stylesheet the build can find under
`app/javascript/skins/<flavour>/`. Two layouts are supported:

```
app/javascript/skins/glitch/my-theme.scss            # single file
app/javascript/skins/glitch/my-theme/common.scss     # directory (index.scss or
                                                     # application.scss also work)
```

Discovery is automatic — there is no registry to edit:

- `app/lib/themes.rb` finds the skin and adds it to the settings UI.
- `config/vite/plugin-glitch-themes.ts` adds it as a build entrypoint.

Both scan at boot, so **restart the Rails server and the Vite dev server**
after adding or removing a skin.

The directory layout is preferred because it can hold extra files, such as a
`names.yml` giving the skin a localized display name (any file matching
`names.yml` or `names/*.yml` inside the skin directory is loaded):

```yaml
en:
  skins:
    glitch:
      my-theme: My Wonderful Theme
```

Inside a skin directory, only `common`/`index`/`application` files count as
entrypoints, and directories that don't match a flavour name (like `shared/`
here) are ignored entirely — so both are safe places for extra partials.
Don't place loose partials directly in `skins/<flavour>/`, though: any
`.scss` file at that level is registered as a skin.

## Step by step: create your own theme

### 1. Create the skin entrypoint

A skin must import the flavour's base stylesheet, then add its overrides.
For the glitch flavour, create `app/javascript/skins/glitch/my-theme/common.scss`:

```scss
@use '@/flavours/glitch/styles/application';

// overrides go here (or @use a partial, as Ocean Blue does)
```

For the vanilla flavour the base import is `@use '@/styles/application';`
(`@/` is an alias for `app/javascript/`). Everything else is identical, so if
you want your theme available in both flavours, put the overrides in a shared
partial — see `skins/shared/_ocean.scss` and the two four-line `common.scss`
files that use it.

### 2. Recolor the palette

Override the ramps on `html`, *after* the application import. Generate a
tint/shade ramp from your base color (tools like Tailwind's palette
generator or oklch.com help), keeping each step's *lightness* close to the
stock ramp so text/background contrast keeps working:

```scss
html {
  // Accent: override the indigo ramp with your brand color.
  --color-indigo-50: #eef8ff;
  --color-indigo-100: #d9efff;
  // ... all 11 steps ...
  --color-indigo-950: #142c4b;

  // Optional: tint the neutrals to match.
  --color-grey-50: #f3f7fa;
  // ... all 11 steps ...
  --color-grey-950: #101c26;
}
```

Since your rule appears later in the compiled CSS than the stock `html` rule,
it wins the cascade. This alone is a complete two-scheme theme.

### 3. (Optional) Fine-tune individual schemes

To adjust a specific semantic token in only one scheme, mirror the selectors
that `tokens/index.scss` uses — dark is the default when the attribute is
absent, so it needs both selectors:

```scss
[data-color-scheme='dark'],
html:not([data-color-scheme]) {
  --color-bg-primary: #0a1520; // darker than grey-950
}

[data-color-scheme='light'] {
  --color-bg-primary: #fbfdff; // blue-tinted white instead of pure white
}
```

The most useful semantic tokens:

| Token | Role |
| --- | --- |
| `--color-bg-primary` / `-secondary` / `-tertiary` | Main surfaces (columns, cards, panels) |
| `--color-text-primary` / `-secondary` | Body and muted text |
| `--color-text-brand` | Links and accent text |
| `--color-bg-brand-base` / `-soft` / `-softest` | Buttons and accent fills |
| `--color-border-primary` / `-brand` | Dividers and outlines |
| `--color-text-error` / `-warning` / `-success` (+ matching `bg`/`border`) | State colors |

Read `tokens/theme/_dark.scss` and `_light.scss` for the full list. Tokens
marked `// legacy` still work but may be consolidated in the future — prefer
the unmarked ones. You can also add `contrast-overrides`-style rules by
targeting `[data-contrast='high']` if your palette needs high-contrast
adjustments.

A skin is ordinary CSS, so it isn't limited to colors — you can restyle any
selector — but token overrides survive upstream refactors far better than
selector overrides do.

### 4. Name it and try it

Add a `names.yml` (see above), restart the Rails server and Vite dev server,
then pick your skin under **Settings → Flavours → (flavour)** and flip
**Settings → Preferences → Appearance → color scheme** between light, dark,
and automatic to check both variants.

### 5. Check accessibility

Aim for WCAG AA contrast: ≥ 4.5:1 for text against the background it sits on
(≥ 3:1 for large text and UI outlines). The pairings to verify in both
schemes, using your browser devtools' contrast checker:

- `--color-text-primary` and `--color-text-secondary` on `--color-bg-primary`
  and `--color-bg-secondary`
- `--color-text-brand` (links) on `--color-bg-primary`
- `--color-text-on-brand-base` on `--color-bg-brand-base` (buttons)

If you keep each ramp step's lightness close to the stock palette's, these
pass automatically.

## Worked example: Ocean Blue

The `ocean` skin in this directory is a complete, minimal reference:

- [`shared/_ocean.scss`](shared/_ocean.scss) — ocean-blue accent ramp +
  blue-slate neutral ramp, plus one small per-scheme tweak each for light
  and dark
- [`glitch/ocean/common.scss`](glitch/ocean/common.scss) /
  [`vanilla/ocean/common.scss`](vanilla/ocean/common.scss) — entrypoints
- `glitch/ocean/names.yml` / `vanilla/ocean/names.yml` — display names

Copying those five files and swapping the colors is the fastest way to make
your own theme.

## Alternative: instance-wide custom CSS

Admins who just want small tweaks without deploying code can paste CSS
(including token overrides like the ones above) into
**Administration → Server settings → Appearance → Custom CSS**. It loads
after every skin, for every user, regardless of their chosen skin.
