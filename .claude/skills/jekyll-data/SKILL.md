# Jekyll Data Skill

Generate or edit `_data/*.yml` files (nav menus, team members, FAQs, etc.) and scaffold the corresponding Liquid templates that loop over them.

## Steps

1. **Identify the data type.** Ask the user what they need if not specified. Common patterns:
   - `navigation` — list of nav links with title + URL.
   - `team` — team member profiles (name, role, bio, photo, social links).
   - `faq` — question + answer pairs, optionally grouped by category.
   - `features` — icon + title + description cards.
   - `testimonials` — quote + author + company.
   - `social_links` — platform + URL pairs for footer/header.
   - Custom — user describes the data structure.

2. **Create or edit `_data/<name>.yml`** with a sensible structure and 2-3 sample entries so the template can be tested immediately.

   Example — `_data/navigation.yml`:
```yaml
- title: Home
  url: /
- title: About
  url: /about/
- title: Blog
  url: /blog/
- title: Projects
  url: /projects/
- title: Contact
  url: /contact/
```

   Example — `_data/team.yml`:
```yaml
- name: "Jane Smith"
  role: "Lead Developer"
  bio: "Builds things on the web. Loves open source."
  photo: /assets/img/team/jane.jpg
  github: janesmith
  twitter: janesmith

- name: "Alex Doe"
  role: "Designer"
  bio: "Creates interfaces people actually enjoy."
  photo: /assets/img/team/alex.jpg
  dribbble: alexdoe
```

   Example — `_data/faq.yml`:
```yaml
- question: "How do I get started?"
  answer: "Clone the repo, run `bundle install`, then `jekyll serve`."
  category: setup

- question: "Which themes are supported?"
  answer: "Any gem-based or remote theme works."
  category: themes
```

3. **Scaffold the Liquid template** — either as an include (`_includes/<name>.html`) or inline within the page where the data will be used.

   Example — nav include (`_includes/nav.html`):
```html
<nav class="site-nav">
  <ul>
    {% for item in site.data.navigation %}
      <li>
        <a href="{{ item.url }}" {% if page.url == item.url %}class="active"{% endif %}>
          {{ item.title }}
        </a>
      </li>
    {% endfor %}
  </ul>
</nav>
```

   Example — team section:
```html
<section class="team">
  {% for member in site.data.team %}
    <div class="team__card">
      <img src="{{ member.photo }}" alt="{{ member.name }}">
      <h3>{{ member.name }}</h3>
      <p class="team__role">{{ member.role }}</p>
      <p>{{ member.bio }}</p>
    </div>
  {% endfor %}
</section>
```

   Example — FAQ with grouping:
```html
{% assign categories = site.data.faq | map: "category" | uniq %}
{% for cat in categories %}
  <h2>{{ cat | capitalize }}</h2>
  {% for item in site.data.faq %}
    {% if item.category == cat %}
      <details>
        <summary>{{ item.question }}</summary>
        <p>{{ item.answer }}</p>
      </details>
    {% endif %}
  {% endfor %}
{% endfor %}
```

4. **Report back** with:
   - Path to the data file created/edited.
   - Path to the include/template created.
   - The usage snippet to drop into a page or layout.

## Edge cases

- YAML is indentation-sensitive. If the user's data has special characters (colons, ampersands), ensure values are quoted.
- For large datasets (100+ entries), suggest moving to CSV (`_data/<name>.csv`) which Jekyll also supports natively.
- If the data file already exists, read it first and add entries rather than overwriting.
