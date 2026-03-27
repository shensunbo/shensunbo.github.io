# Popular Blog Features to Add

> Tailored to shensunbo.github.io — Jekyll 4 + Hux Blog template, technical C++/rendering audience.

---

## 🔥 High Impact / Easy to Add

### 1. Copy Code Button
A small "Copy" button on every code block. Very expected by technical readers.
- Pure JS: inject a `<button>` into each `<pre>` block, use `navigator.clipboard.writeText()`
- Add to `js/hux-blog.js`, style in `less/`
- **Effort:** Low

### 2. Reading Time Estimate
"~8 min read" shown in post meta line. Standard on tech blogs.
- Jekyll Liquid: `{{ content | number_of_words | divided_by: 200 }} min read`
- Add to `_layouts/post.html` next to the date
- **Effort:** Very Low (one line of Liquid)

### 3. Reading Progress Bar
A thin bar at the top of the page that fills as you scroll down.
- ~10 lines of JS + a CSS rule
- Add to `js/hux-blog.js` and a `position:fixed` bar in `_layouts/post.html`
- **Effort:** Low

### 4. Back to Top Button
A floating button that appears after scrolling down ~400px.
- Already a common pattern; fits the blog's style
- **Effort:** Low

### 5. Dark Mode Toggle
A sun/moon icon in the nav bar switching between light and dark color schemes.
- CSS custom properties (variables) override in a `[data-theme="dark"]` class
- Store preference in `localStorage`
- Requires touching `less/variables.less` to convert hardcoded colors to CSS vars
- **Effort:** Medium

---

## 📌 Content & Navigation

### 6. Series / Multi-Part Posts
Group related posts (e.g., a "Renderer from Scratch" series) with prev/next navigation within the series and a series index box at the top of each part.
- Add `series: "My Renderer"` + `series_part: 1` to front matter
- Create a `_includes/series-nav.html` include
- **Effort:** Medium

### 7. "Last Updated" Date
Show both "Published" and "Last Updated" dates on posts.
- Add `updated: YYYY-MM-DD` to front matter
- Display in `_layouts/post.html` post meta
- **Effort:** Very Low

### 8. Related Posts
Show 3–5 related posts at the bottom based on shared tags.
- Pure Liquid: iterate `site.posts`, count tag intersections, take top N
- Add to `_layouts/post.html` below the comments section
- **Effort:** Low–Medium

### 9. Post Word Count
"1,240 words" shown in the post meta (pairs well with reading time).
- `{{ content | number_of_words }}` in Liquid
- **Effort:** Very Low

### 10. Better Pagination
Replace the plain Older/Newer links with page number buttons (1 2 3 …).
- Requires `jekyll-paginate-v2` plugin (not supported on GitHub Pages without CI)
- Or: build a custom JS-based page number UI over the existing paginator links
- **Effort:** Medium–High

---

## 🛠 Developer-Specific (Great for Technical Audience)

### 11. Code Block Language Badge
A label like `cpp` or `bash` in the top-right corner of code blocks.
- Rouge adds `language-xxx` class to `<code>` elements — read it with JS and inject a badge
- **Effort:** Low

### 12. Collapsible Code Blocks
Long code blocks (>30 lines) collapse by default with a "Show more" button.
- JS: detect block height, clip with CSS `max-height`, inject toggle button
- **Effort:** Low–Medium

### 13. Inline Diff Highlighting
Support for `diff` language blocks with red/green line coloring.
- CSS rules targeting `.language-diff .line` prefixes (`+`, `-`)
- Add to `less/highlight.less`
- **Effort:** Low

---

## 💬 Community & Engagement

### 14. Switch Comments to Giscus
Replace Cusdis with [Giscus](https://giscus.app/) (GitHub Discussions-based).
- Better UX, no external service, reactions + threads
- One script tag, no backend
- **Effort:** Very Low (config swap)

### 15. RSS Feed Enable
`RSS: false` in `_config.yml` — just flip it to `true`. Already wired up via `feed.xml`.
- **Effort:** Trivial

### 16. Social Sharing Buttons
Twitter/X, LinkedIn, WeChat share links on each post.
- Static share URLs using `window.location`; no API needed
- Add to `_layouts/post.html`
- **Effort:** Low

---

## 🎨 Polish

### 17. Image Lightbox / Zoom
Click any image in a post to view it full-screen.
- Lightweight library: [medium-zoom](https://github.com/francoischalifour/medium-zoom) (~3KB)
- Or a CSS-only approach with `<dialog>`
- **Effort:** Low

### 18. Smooth Scroll to Anchors
Make TOC sidebar links scroll smoothly instead of jumping.
- One CSS rule: `html { scroll-behavior: smooth; }`
- **Effort:** Trivial

### 19. Favicon & PWA Icon Polish
The blog has a service worker — add proper PWA icons (192x192, 512x512) and a `manifest.json` for full installability.
- **Effort:** Low (mostly asset creation)

### 20. 404 Page Improvement
The existing `404.html` exists — add a search box or "recent posts" list.
- **Effort:** Low
