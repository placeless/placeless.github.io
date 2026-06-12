# Head And Schema Cleanup Audit

## Scope

Reviewed metadata and microdata emitted by the active layouts and includes:

- `_includes/head.html`
- `_layouts/default.html`
- `_layouts/page.html`
- `_layouts/post.html`
- `_includes/nav.html`
- `index.html`

## Current Behavior

- `_includes/head.html` manually emits `<title>`, `description`, `canonical`,
  stylesheet, apple touch icon, and RSS feed links.
- `_includes/head.html` also calls `{% seo %}`, which emits overlapping SEO
  metadata through `jekyll-seo-tag`.
- The generated pages currently preserve the expected canonical URLs, feed URL,
  Google site verification meta tag, and Cloudflare analytics script.
- `_includes/head.html` still includes
  `<meta http-equiv="x-ua-compatible" content="ie=edge" />`, which is obsolete
  for the site's current browser target.
- `_layouts/post.html` wraps posts in `itemscope
  itemtype="http://schema.org/BlogPosting"` and marks the post body with
  `itemprop="articleBody"`.
- `index.html` also declares `BlogPosting` even though it renders the home-page
  archive list, then marks the list wrapper as `itemprop="articleList"`.
- `_layouts/page.html` and `_includes/nav.html` use `itemprop="name headline"`
  in headers. On standalone pages this is not inside an explicit schema item.

## Risks Or Unknowns

- Head metadata, canonical URLs, feeds, SEO behavior, analytics, and sitemap
  behavior are protected areas in `AGENTS.md`.
- Removing manual metadata that overlaps with `jekyll-seo-tag` is likely
  beneficial, but should be checked against generated output before and after.
- The home-page schema appears semantically stale, but changing structured data
  can affect search interpretation even when visible HTML is unchanged.
- The one-line GitHub icon include in `about.md` is load-bearing: formatting the
  included inline SVG across multiple lines causes Markdown to emit broken HTML.

## Proposed Next Steps

1. Capture generated head output for home, about, projects, and a representative
   post before any SEO/template change.
2. In a separate approved pass, remove only redundant or obsolete head metadata
   after confirming `jekyll-seo-tag` emits the equivalent required values.
3. In a separate approved pass, correct or remove stale schema/microdata on the
   home and standalone page templates.
4. Keep Markdown-inline includes that render SVGs on a single line unless the
   surrounding content is moved out of Markdown.

## Verification Commands

```sh
rg -n "seo|canonical|description|schema.org|itemprop|itemscope|x-ua-compatible|google-site-verification" _includes _layouts index.html _config.yml
mise exec -- bundle exec jekyll build
rg -n "canonical|description|schema.org|itemprop|google-site-verification|/feed.xml|cf.js" _site/index.html _site/about/index.html _site/projects.html _site/blog/asyncio-python.html
```

## Implementation Notes

- Removed the obsolete `x-ua-compatible` meta tag from `_includes/head.html`.
- Removed manual `description` and `canonical` output from `_includes/head.html`
  so those values are emitted only by `jekyll-seo-tag`.
- Kept the manual `<title>` in `_includes/head.html` for now. Removing it would
  change current browser-title behavior from the simple page/post title to the
  SEO title format emitted by `jekyll-seo-tag`.
- Removed stale `BlogPosting` microdata from the home-page archive wrapper in
  `index.html`.
- Removed orphaned `itemprop="name headline"` from standalone page headings in
  `_layouts/page.html`.
