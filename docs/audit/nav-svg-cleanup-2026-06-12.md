# Nav SVG Cleanup Audit

## Scope

Reviewed the active post navigation include:

- `_includes/nav.html`
- `_layouts/post.html`

## Current Behavior

- `_layouts/post.html` includes `_includes/nav.html` for post headers.
- `_includes/nav.html` renders the post title, post date, optional author,
  Home, Email, Projects, Running, and light-toggle controls.
- The include uses inline SVGs for each nav icon.
- Several SVGs contained legacy export metadata: `version="1.1"`, unused
  `xmlns:xlink`, and invisible zero-opacity `<rect>` placeholders.

## Risks Or Unknowns

- Navigation labels and hrefs are visible user-facing behavior and should remain
  stable.
- The SVG path data, dimensions, color, and inline positioning are visual
  behavior and should not change in this pass.
- Further extraction into shared icon includes is possible, but should be a
  separate step because inline SVG and Markdown interactions have already shown
  fragile rendering behavior elsewhere in the site.

## Change Made

- Removed inert SVG boilerplate from `_includes/nav.html`:
  `version="1.1"`, unused `xmlns:xlink`, and invisible zero-opacity `<rect>`
  placeholders.
- Preserved all nav labels, hrefs, SVG path data, dimensions, fills, and inline
  positioning.

## Verification Commands

```sh
rg -n "version=\"1\\.1\"|xmlns:xlink|<rect [^>]*opacity=\"0\"" _includes/nav.html _site/blog/asyncio-python.html
mise exec -- bundle exec jekyll build
```

## Browser Verification

Checked `http://127.0.0.1:4173/blog/asyncio-python.html`:

- Post title remained visible.
- Nav links remained `Home`, `Email`, `Projects`, and `Running`.
- Nav hrefs remained `/`, `mailto:placeless@outlook.com`, `/projects`, and
  `https://m.placeless.net`.
- Post meta still contained six SVG icons.
- No horizontal overflow at the default browser viewport.
