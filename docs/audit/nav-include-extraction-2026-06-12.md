# Nav Include Extraction Audit

## Scope

Reviewed the active navigation markup in:

- `_includes/nav.html`
- `_layouts/page.html`

## Current Behavior

- `_includes/nav.html` renders post metadata and post navigation links.
- `_layouts/page.html` renders standalone page navigation links.
- Both templates embedded long inline SVG/link snippets directly in the layout
  file, making the active templates hard to scan and review.
- The about-page GitHub include remains untouched because Markdown-inline SVG
  rendering is fragile.

## Risks Or Unknowns

- Navigation labels, hrefs, icon counts, and layout wrapping are visible
  user-facing behavior.
- Include extraction can introduce whitespace changes in generated HTML. The
  extracted snippets therefore keep each icon and label together inside a single
  include.
- The new includes are intentionally specific to the current post/page nav
  markup rather than a generic abstraction.
- Standalone pages should now visually align with the main post navigation,
  while still omitting the post date.

## Change Made

- Extracted post nav snippets into:
  `_includes/post-nav-date.html`, `_includes/post-nav-home.html`,
  `_includes/post-nav-email.html`, `_includes/post-nav-projects.html`,
  `_includes/post-nav-running.html`, and `_includes/post-nav-lights.html`.
- Extracted page nav snippets into:
  `_includes/page-nav-home.html`, `_includes/page-nav-email.html`, and
  `_includes/page-nav-projects.html`.
- Replaced long inline SVG/link lines in `_includes/nav.html` and
  `_layouts/page.html` with include calls.
- Updated `_layouts/page.html` to reuse the shared post navigation item includes
  for `Home`, `Email`, `Projects`, `Running`, and `Theme`, without including
  the post date.
- Replaced the theme toggle's lightbulb-style `span` with a semantic button
  using moon/sun icons, the visible desktop label `Theme`, and `aria-pressed`
  state updates.
- Removed the unused page-specific nav includes from the implementation to
  avoid maintaining two parallel page/post nav systems.
- Kept the structural `</p>` tags in the parent templates.
- Wrapped visible nav labels in `.nav-label` spans and visually hid those labels
  at mobile widths, leaving the text in the DOM for link names.

## Verification Commands

```sh
mise exec -- bundle exec jekyll build
```

## Browser Verification

Checked generated pages through a local static server:

- `/blog/asyncio-python.html`
- `/about/`
- `/projects.html`

Confirmed:

- Post nav links remained `Home`, `Email`, `Projects`, and `Running`.
- Page nav links now match post nav controls: `Home`, `Email`, `Projects`,
  `Running`, and `Theme`, without rendering a post date.
- Hrefs remained stable.
- Post nav still rendered six SVG icons.
- Page nav now renders five SVG icons.
- No horizontal overflow at the default viewport.
- No horizontal overflow at a 390px mobile viewport for post and page nav.
- Desktop nav labels remained visible.
- Mobile nav labels were visually hidden while link text remained available in
  the DOM.
