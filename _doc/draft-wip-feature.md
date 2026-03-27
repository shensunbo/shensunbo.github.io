# WIP / Unfinished Posts Feature — Ideas & Plans

> How to keep unfinished blog posts in the repo without showing them to readers by default.
> Constraint: This is a Jekyll static site on GitHub Pages — no server-side logic.

---

## The Core Problem

`jekyll-paginate` (the plugin used) paginates **all published posts** — you can't filter posts out of the paginator using Liquid alone. So any "hide WIP posts" solution needs to work around or outside the paginator.

---

## Option A — Frontend Toggle (Recommended ⭐)

**How it works:**
- Add `wip: true` to front matter of unfinished posts
- Jekyll builds them normally and adds a `data-wip="true"` attribute to their cards on the index page
- WIP post cards are **hidden by default via CSS**
- A toggle button in the navbar/sidebar reveals them (state saved in `localStorage`)
- WIP posts are still accessible by direct URL (you can share them with yourself)

**What you'd change:**

1. **`index.html`** — wrap each post card with `data-wip`:
   ```html
   <div class="post-preview" {% if post.wip %}data-wip="true"{% endif %}>
   ```

2. **CSS** — hide WIP cards by default:
   ```css
   [data-wip="true"] { display: none; }
   body.show-wip [data-wip="true"] { display: block; }
   ```

3. **JS** — add a toggle button:
   ```js
   // On load, restore preference
   if (localStorage.getItem('showWip') === 'true') {
     document.body.classList.add('show-wip');
   }
   // Toggle button click
   document.getElementById('wip-toggle').addEventListener('click', () => {
     document.body.classList.toggle('show-wip');
     localStorage.setItem('showWip', document.body.classList.contains('show-wip'));
   });
   ```

4. **`_includes/nav.html`** — add a small "🚧 Ongoing" button in the nav or sidebar

5. **Front matter** for WIP posts:
   ```yaml
   ---
   layout: post
   title: "My Unfinished Post"
   wip: true
   catalog: true
   tags:
       - C++
   ---
   ```

**Pros:** Simple, no config changes, works with existing paginator, posts accessible by URL  
**Cons:** WIP posts still appear in RSS feed and sitemap (fixable, see below)

**Optional cleanup:**
- Exclude from `feed.xml`: wrap the post loop with `{% unless post.wip %}`
- Exclude from `sitemap.xml`: same Liquid filter
- Add a visible "🚧 Work In Progress" banner at the top of WIP post pages in `_layouts/post.html`:
  ```html
  {% if page.wip %}
  <div class="wip-banner">🚧 This post is a work in progress</div>
  {% endif %}
  ```

---

## Option B — Separate `_wip` Jekyll Collection

**How it works:**
- Create a `_wip/` folder alongside `_posts/`
- Define it as a Jekyll collection in `_config.yml`
- WIP posts live in `_wip/` instead of `_posts/`, so they **never appear in `site.posts`** or the paginator
- Create a dedicated `/ongoing/` page that lists `site.wip`

**What you'd change:**

1. **`_config.yml`**:
   ```yaml
   collections:
     wip:
       output: true
       permalink: /wip/:name/
   ```

2. **Create `_wip/` folder** and move/create unfinished posts there (same front matter format)

3. **Create `ongoing.html`** at root:
   ```html
   ---
   layout: page
   title: "Ongoing"
   ---
   {% for post in site.wip %}
   <div class="post-preview">
     <a href="{{ post.url }}"><h2>{{ post.title }}</h2></a>
     <p>{{ post.content | strip_html | truncate: 150 }}</p>
   </div>
   {% endfor %}
   ```

4. **Add "Ongoing" link to nav** (`_includes/nav.html`)

**Pros:** Clean separation, WIP posts completely invisible to paginator/RSS/sitemap by default  
**Cons:** Posts live in a different folder (slightly less intuitive), URLs become `/wip/post-name/` instead of `/YYYY/MM/DD/post-name/`

---

## Option C — Password-Protected Drafts Page

**How it works:**
- Same as Option A (WIP posts exist, hidden by default)
- The "Show Ongoing" button requires a simple password (stored as a JS hash)
- Password prompt stored in `localStorage` so you only type it once

**JS sketch:**
```js
document.getElementById('wip-toggle').addEventListener('click', () => {
  if (localStorage.getItem('wipAuth') !== 'ok') {
    const input = prompt('Password:');
    // compare against a SHA-256 hash; never store plaintext
    if (hash(input) !== EXPECTED_HASH) return;
    localStorage.setItem('wipAuth', 'ok');
  }
  document.body.classList.toggle('show-wip');
});
```

**Note:** This is security-by-obscurity. The post content is still in the built HTML — anyone who reads the source can see it. This is appropriate for "I don't want casual readers to see rough drafts", not for truly private content.

---

## Option D — `published: false` (Local Draft Only)

**How it works:**
- Set `published: false` in front matter
- Jekyll skips these files entirely at build time — they don't exist on the live site
- View them locally with: `bundle exec jekyll serve --unpublished`

**Pros:** Truly invisible on the live site, zero code changes needed  
**Cons:** Can't share a WIP URL with someone else, can't see your own WIP posts on mobile or on the live site

**Best for:** Very early-stage drafts you never want online until they're done.

---

## Recommendation

| Need | Best Option |
|---|---|
| See my own WIP posts on the live site | **A (Toggle)** |
| Keep WIP posts completely separate | **B (Collection)** |
| Hide rough drafts from casual visitors | **C (Password)** |
| True local-only drafts | **D (published: false)** |

**Suggested combo:** Use **Option D** for very rough notes, and **Option A** for posts that are "mostly done but not ready" — giving you a personal "Ongoing" view while keeping the public feed clean.

---

## Implementation Steps for Option A (Quickest to Ship)

1. Add `wip: true` to any post in `_posts/` that's unfinished
2. In `index.html`, add `{% if post.wip %}data-wip="true"{% endif %}` to post card divs
3. In `less/hux-blog.less` (or a new `less/wip.less`), add the hide/show rules
4. In `js/hux-blog.js`, add the toggle logic with localStorage
5. Add a "🚧 Ongoing" button to `_includes/nav.html` that only renders if `site.posts | where: "wip", true` is non-empty
6. In `_layouts/post.html`, add a WIP banner at the top for `wip: true` posts
7. In `feed.xml` and `sitemap.xml`, wrap loops with `{% unless post.wip %}`
8. Compile: `npx grunt` → commit
