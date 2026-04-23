# Jekyll Update Skill

Update Jekyll, gems, GitHub Actions, and associated config files to their latest stable versions — safely, with a pre-update audit and post-update verification.

## Steps

### 1. Pre-update audit

Before touching anything, read and record the current state:

- Read `Gemfile` and `Gemfile.lock` — note current versions of `jekyll`, theme gems, and plugins.
- Check `.github/workflows/*.yml` — note `actions/checkout`, `ruby/setup-ruby`, and other action versions.
- Check `_config.yml` for any version-specific settings (e.g. `kramdown` options removed in Jekyll 4, `gems:` key renamed to `plugins:`).
- Run `bundle outdated` (if Bundler is available) and capture output.

Report what will be updated before making any changes.

---

### 2. Update `Gemfile` gem version constraints

Open `Gemfile` and update version constraints for the core stack. Use pessimistic (`~>`) constraints to allow patch releases but not breaking majors:

```ruby
source "https://rubygems.org"

gem "jekyll", "~> 4.3"           # current stable
gem "minima", "~> 2.5"           # or your theme gem

group :jekyll_plugins do
  gem "jekyll-feed",     "~> 0.17"
  gem "jekyll-seo-tag",  "~> 2.8"
  gem "jekyll-sitemap",  "~> 1.4"
end

# Windows/JRuby compatibility shims (keep if present)
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end
gem "wdm", "~> 0.1", platforms: [:mingw, :x64_mingw, :mswin]
gem "http_parser.rb", "~> 0.6.0", platforms: [:jruby]
```

Only bump a gem's constraint if the new major version is stable. Flag any gem whose latest release is a pre-release or RC.

---

### 3. Update `Gemfile.lock`

Instruct the user to run:

```bash
bundle update
```

Or, to update a single gem without touching others:

```bash
bundle update jekyll
```

If running in the conversation, execute `bundle update` and capture the output. Report any dependency conflicts or gems that could not be updated.

---

### 4. Update `_config.yml` for breaking changes

#### Jekyll 3 → 4 migration flags

- `gems:` key → rename to `plugins:`.
- `highlighter: pygments` → change to `rouge` (Pygments removed).
- `relative_permalinks: true` → remove (no longer supported).
- `paginate` + `jekyll-paginate` → note it still works but `jekyll-paginate-v2` is recommended for collections.
- `kramdown.input: GFM` may need to be set explicitly.

#### Minima 2 → 3 migration flags

- `minima.skin` key added for color schemes (`classic`, `solarized`, `solarized-dark`).
- `header_pages` moved under `minima:` namespace in some versions — check theme changelog.

Report each migration issue found with the exact line to change.

---

### 5. Update GitHub Actions workflow

Scan `.github/workflows/*.yml` for pinned action versions and update to latest stable:

| Action | Current stable |
|---|---|
| `actions/checkout` | `v4` |
| `actions/upload-pages-artifact` | `v3` |
| `actions/deploy-pages` | `v4` |
| `actions/configure-pages` | `v5` |
| `ruby/setup-ruby` | `v1` (self-updating) |
| `actions/jekyll-build-pages` | `v1` |

Replace old versions in the YAML files. Example replacement:
```yaml
# Before
- uses: actions/checkout@v2
# After
- uses: actions/checkout@v4
```

---

### 6. Update `.ruby-version` (if present)

Check if `.ruby-version` exists. If it pins a Ruby version older than the current stable (3.3.x as of 2025), update it:

```
3.3.6
```

Also update `ruby-version` in the GitHub Actions workflow to match.

---

### 7. Post-update verification checklist

After all updates, report a checklist:

- [ ] `bundle install` completes without errors.
- [ ] `bundle exec jekyll build` completes without warnings about deprecated config keys.
- [ ] `bundle exec jekyll serve` starts and site renders correctly at `http://localhost:4000`.
- [ ] GitHub Actions workflow YAML is valid (no syntax errors).
- [ ] No gems still reporting outdated status after `bundle update`.

---

### 8. Report summary

```
## Jekyll Update Summary — YYYY-MM-DD

### Updated
- jekyll: 4.2.2 → 4.3.4
- jekyll-seo-tag: 2.7.1 → 2.8.0
- actions/checkout: v2 → v4
- actions/deploy-pages: v1 → v4
- .ruby-version: 3.1.2 → 3.3.6

### Skipped (already current)
- minima: 2.5.1 ✓
- jekyll-feed: 0.17.2 ✓

### Requires manual review
- _config.yml line 12: `gems:` should be renamed to `plugins:`
- Gemfile: jekyll-paginate pinned to ~> 1.1 — consider migrating to jekyll-paginate-v2
```

## Edge cases

- If `Gemfile.lock` is not committed (in `.gitignore`), warn the user that builds may be non-reproducible and recommend committing it.
- If the site uses a `github-pages` gem umbrella, do not update individual gems — the umbrella pins everything. Update the umbrella gem (`gem "github-pages"`) instead, or migrate to a direct GitHub Actions build (see `jekyll-deploy` skill) for full version control.
- If `bundle update` introduces a breaking change (build fails), roll back with `git checkout Gemfile.lock` and update gems individually using `bundle update <gem>`.
- Never update gems in a CI environment — only update locally, commit `Gemfile.lock`, then push.
