# Jekyll Component Skill

Build reusable Liquid includes (cards, CTAs, alerts, etc.) and place them in `_includes/`, with ready-to-paste usage snippets.

## Steps

1. **Identify the component type.** Ask the user what they need if not specified. Common types:
   - `card` — image + title + description + link
   - `alert` — info / warning / success / danger banner
   - `cta` — call-to-action section with heading, body text, and button
   - `badge` — small inline label
   - `gallery` — responsive image grid
   - `video` — responsive YouTube/Vimeo embed wrapper
   - `toc` — custom table of contents
   - Custom — user describes the markup they want

2. **Check for existing includes** in `_includes/`. If a similar file exists, ask whether to extend it or create a new one.

3. **Create `_includes/<component-name>.html`** using Liquid best practices:
   - Accept all variable inputs via `include` parameters (never hardcode content).
   - Provide sensible defaults using `| default:` filters.
   - Use semantic HTML5 elements.
   - Add BEM-style CSS class names so styles are easy to target.

   Example — alert component (`_includes/alert.html`):

```html
{% assign type  = include.type  | default: "info" %}
{% assign title = include.title | default: "" %}
<div class="alert alert--{{ type }}" role="alert">
  {% if title != "" %}<strong>{{ title }}</strong> {% endif %}
  {{ include.content }}
</div>
```

4. **Generate the usage snippet** and display it clearly for the user to copy:

```liquid
{% include alert.html type="warning" title="Heads up!" content="This is an important notice." %}
```

5. **Generate matching SCSS** (if a `_sass/` directory exists) and append it — or create `_sass/_components/<name>.scss` and `@use` it from `assets/css/main.scss`:

```scss
.alert {
  padding: 1rem 1.25rem;
  border-left: 4px solid;
  border-radius: 4px;
  margin-bottom: 1.5rem;

  &--info    { border-color: #3b82f6; background: #eff6ff; }
  &--warning { border-color: #f59e0b; background: #fffbeb; }
  &--success { border-color: #22c55e; background: #f0fdf4; }
  &--danger  { border-color: #ef4444; background: #fef2f2; }
}
```

6. **Report back** with:
   - File path of the new include.
   - Copy-paste usage snippet.
   - Any SCSS files created or modified.

## Edge cases

- If the site uses a CDN-hosted theme (no local `_sass/`), advise the user to add styles inline or in a `<style>` block inside the include, or to set up a local asset pipeline first (see `jekyll-theme` skill).
- If the component requires JavaScript (e.g. modal, accordion), scaffold a minimal `assets/js/<component>.js` file and add a `<script>` tag at the bottom of the include.
