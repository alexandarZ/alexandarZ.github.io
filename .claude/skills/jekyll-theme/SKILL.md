# Jekyll Theme Skill

Customize or override a Minima, Cayman, or remote theme — handles `_sass/` overrides, `assets/css/main.scss`, and layout inheritance without touching gem files.

## Steps

1. **Identify the active theme** by reading `_config.yml`:
   - `theme: minima` → local gem-based theme.
   - `remote_theme: owner/repo` → GitHub-hosted via `jekyll-remote-theme`.
   - No theme key → likely a fully custom layout.

2. **Determine what the user wants to change:**
   - Colors, typography, spacing (SCSS variables / custom properties).
   - Layout structure (header, footer, sidebar).
   - A specific page layout override.
   - Adding a dark-mode toggle or custom font.

3. **Set up the override structure** (create directories if missing):

```
_sass/
  minima/
    custom-variables.scss   ← override theme SCSS variables here
    custom-styles.scss      ← add arbitrary extra CSS here
assets/
  css/
    main.scss               ← entry point; must import theme then overrides
```

4. **Edit `assets/css/main.scss`** to import theme + overrides in the correct order:

```scss
---
---

@import "minima";
@import "minima/custom-variables";
@import "minima/custom-styles";
```

   For `jekyll-remote-theme`, the import name matches the theme's own SCSS entry point (check the theme repo's `_sass/` folder).

5. **Override layouts** by copying the relevant file from the gem into `_layouts/` and modifying it locally. Provide the user with the gem path command:

```bash
bundle info --path minima
```

   Then copy, e.g.:
```bash
cp "$(bundle info --path minima)/_layouts/default.html" _layouts/default.html
```

6. **Common variable overrides for Minima** — scaffold `_sass/minima/custom-variables.scss`:

```scss
$base-font-family: "Inter", system-ui, sans-serif;
$base-font-size:   16px;
$base-line-height: 1.6;

$brand-color:       #0070f3;
$brand-color-light: lighten($brand-color, 40%);
$brand-color-dark:  darken($brand-color, 15%);

$background-color: #ffffff;
$text-color:       #111827;
$link-base-color:  $brand-color;
```

7. **Report back** with every file created or modified, and remind the user to restart `jekyll serve` so SCSS changes are picked up.

## Edge cases

- If `Gemfile` doesn't include the theme gem, remind the user to run `bundle install` after adding it.
- If the remote theme is pinned to a specific commit/tag in `_config.yml`, SCSS variable names may differ from the current `main` branch — check the theme source at that ref.
- For GitHub Pages with `remote_theme`, local `_sass/` overrides work; gem-based overrides require the exact gem version GitHub Pages uses (see `github-pages` gem dependency list).
