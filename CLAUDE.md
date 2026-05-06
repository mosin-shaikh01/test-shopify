# Shopify Online Store 2.0 Theme Development Guide

## Project Overview
This is a Shopify Online Store 2.0 theme project built with Liquid, JavaScript, and CSS. The theme follows Shopify's modular section architecture and is version-controlled with Git.

## Directory Structure
- **assets/** — CSS, JavaScript, SVG icons, and other static files
- **blocks/** — Reusable block components (sections with block support)
- **config/** — Theme configuration (settings_schema.json for settings UI)
- **layout/** — Base layout templates (theme.liquid, password.liquid)
- **locales/** — Internationalization files (JSON translations)
- **sections/** — Dynamic section templates (rendered via section settings)
- **snippets/** — Reusable Liquid partials (included with {% include %})
- **templates/** — Page templates (product.liquid, collection.liquid, etc.)

## Shopify 2.0 Structure Rules

### Sections
- Use **sections/** for all dynamic components rendered in the theme editor
- Each section must have a `{% schema %}` JSON block defining settings
- Sections support **blocks** for repeatable sub-components (use block_type in schema)
- Use **section groups** to organize related sections in the theme editor
- Avoid hardcoding data; expose all customizable values in schema settings

### Blocks
- Use **blocks/** for block templates referenced by section schemas
- Blocks inherit parent section context and settings
- Block naming: descriptive, lowercase, hyphenated (e.g., `block-product-card.liquid`)

### Snippets
- Use **snippets/** for reusable Liquid partials
- Keep snippets focused and small (under 50 lines when possible)
- Pass data via `include` parameters: `{% include 'snippet-name' with data: var %}`
- Avoid global dependencies; favor explicit parameter passing

### Templates
- Use **templates/** for page-level templates
- Standard templates: `product.liquid`, `collection.liquid`, `page.liquid`, etc.
- Custom templates: prefix with template name (e.g., `product.featured.liquid`)
- Templates should mostly orchestrate sections; avoid heavy logic

## Performance Rules

### Lazy Loading
- Use native `loading="lazy"` on images that aren't above the fold
- Implement native lazy loading for `<img>` and `<iframe>` elements
- Use Intersection Observer for custom lazy-load triggers only when necessary
- Preload critical images with `<link rel="preload">`

### Minimal JavaScript
- Inline critical JavaScript in `<head>` (< 10KB)
- Defer non-critical JS with `defer` or `async` attributes
- Use native Web APIs before importing libraries (e.g., `fetch`, `IntersectionObserver`, `CustomEvent`)
- Avoid jQuery, unnecessary polyfills, or large dependencies
- Load third-party scripts asynchronously (Google Analytics, chatbots, etc.)

### CSS Optimization
- Use CSS Grid and Flexbox for layouts; avoid floats and relative positioning
- Minimize CSS-in-JS; prefer static stylesheets in **assets/**
- Use media queries for responsive design; avoid JavaScript media detection
- Combine critical CSS into a single file (base.css) loaded in `<head>`
- Defer non-critical CSS with `media="print"` fallback technique or async loading

### Image Optimization
- Use Shopify's built-in image CDN via `{{ image | image_url }}` filter
- Serve responsive images with `srcset` and `sizes` attributes
- Optimize image dimensions: use `image_url: width=400` to resize at CDN level
- Lazy load below-the-fold images with `loading="lazy"`
- Avoid oversized images; compress before upload

## Liquid Best Practices

### Variables & Scope
- Use descriptive variable names: `product_title`, `is_featured`, not `x`, `tmp`
- Scope variables to their block/section: `section.settings.color` not global assignments
- Minimize variable reassignments; use filters to transform data
- Use `capture` for complex string building, not string concatenation

### Control Flow
- Prefer `if`/`elsif`/`else` over nested `unless`
- Use `case`/`when` for multiple branches (cleaner than chained `if`)
- Avoid deeply nested conditionals; extract to snippets if needed
- Use `blank` and `nil` checks correctly: `if product.image` (truthy), `if image != empty` (explicit)

### Filters
- Chain filters for readability: `{{ product.title | upcase | truncate: 30 }}`
- Use built-in filters; avoid custom filters unless necessary
- Common filters: `date`, `money`, `size`, `split`, `join`, `map`, `where`, `first`, `last`
- Use `default` filter for fallback values: `{{ product.description | default: "No description" }}`

### Loops
- Use `for` loops with pagination support: `{% for product in collection.products limit: 12 offset: 0 %}`
- Use `break` and `continue` sparingly; restructure logic instead
- Avoid `tablerow`; use semantic `<div>` or `<ul>` with CSS Grid/Flexbox
- Use `limit` to prevent rendering too many items (performance impact)

### Whitespace Control
- Use `{%- and -%}` to strip whitespace when needed for HTML cleanliness
- Avoid over-stripping; keep code readable
- Example: `{%- if condition -%}text{%- endif -%}` vs `{% if condition %}text{% endif %}`

## CSS & Styling Rules

### CSS Architecture
- Use CSS Grid and Flexbox for all layouts
- Avoid floats, absolute positioning, and relative positioning except in specific cases
- Use CSS custom properties (variables) for colors, spacing, fonts: `--color-primary`, `--spacing-base`
- Group related styles by component/section
- Keep specificity low; avoid `!important` (only for utility overrides)

### Naming Conventions
- Use BEM (Block Element Modifier) for class names: `.product-card`, `.product-card__image`, `.product-card--featured`
- Prefix JavaScript-targeting classes with `js-`: `.js-toggle-menu`, `.js-add-to-cart`
- Use descriptive names; avoid generic names like `.container`, `.wrapper`

### Responsive Design
- Mobile-first: start with mobile styles, add breakpoints for larger screens
- Use CSS media queries: `@media (min-width: 768px) { ... }`
- Define breakpoints as CSS variables or in a stylesheet
- Test on real devices; avoid hard-coded pixel breakpoints

## Liquid Deprecation & Compatibility

### Avoid Deprecated Liquid
- ❌ `tablerow` — use `<div>` with CSS Grid instead
- ❌ Custom Liquid filters (Shopify theme limits) — use built-in filters
- ❌ `assign` with complex logic — use `capture` or restructure
- ❌ Global variables like `page` — use explicit section/template variables
- ✅ Use modern filters: `where`, `map`, `first`, `last`, `default`

### Backward Compatibility
- Maintain compatibility with Shopify theme API versions (check theme.liquid)
- Test with multiple storefronts if theme is multi-currency or multi-language
- Use `{{ 'filename' | asset_url }}` for asset URLs (never hardcode paths)

## Safe Git Workflow Rules

### Branching
- Always work on feature branches: `git checkout -b feature/section-name`
- Never commit directly to `main` without a review
- Use descriptive branch names: `feature/add-loyalty-section`, `fix/cart-drawer-scroll`
- Merge via pull request (if using GitHub) for visibility and testing

### Commit Messages
- Use clear, imperative commit messages: "Add product recommendations section" not "fixed stuff"
- Reference issue/ticket numbers if applicable: "Add cart-drawer animation (fix #123)"
- Keep commits atomic: one feature per commit when possible
- Example: `git commit -m "Add lazy loading to product images"`

### Protected Files
- **config/settings_data.json** — never commit (store-specific, ignored by .gitignore)
- **.env** — never commit (credentials, use .env.example template instead)
- Large binaries or videos — use Git LFS if needed

### Deployment Safety
- Test locally with Shopify CLI before pushing: `shopify theme dev`
- Use Git tags for releases: `git tag -a v1.0.0 -m "Release 1.0.0"`
- Pull latest `main` before deploying to production
- Review all changes before merge: `git diff main..feature/branch`

## Production-Safe Code Generation Guidelines

### DO
- ✅ Generate semantic HTML: `<nav>`, `<article>`, `<button>`, not `<div onclick="...">`
- ✅ Use Liquid's built-in security (auto-escaping): `{{ user_input }}` is safe by default
- ✅ Use CSRF tokens for forms: `{{ csrf_token_tag }}`
- ✅ Validate on server-side; never trust client-side validation alone
- ✅ Use `data-*` attributes for JavaScript hooks, not inline event handlers
- ✅ Test generated code locally with `shopify theme dev` before committing
- ✅ Use section presets for default configurations
- ✅ Minify CSS/JS in production (Shopify handles this, but avoid inline bloat)

### DON'T
- ❌ Hardcode API keys or credentials in code
- ❌ Use `eval()` or `new Function()` anywhere
- ❌ Generate unescaped HTML: always use `{{ }}` (auto-escapes) or `| escape` filter
- ❌ Disable Content Security Policy headers
- ❌ Mix server-side (Liquid) and client-side (JavaScript) logic without clear separation
- ❌ Generate massive inline `<style>` blocks; use external stylesheets
- ❌ Use `innerHTML` in JavaScript; prefer `textContent`, `appendChild`, or template literals
- ❌ Commit unminified vendor libraries; rely on npm/package manager

### Liquid Security
- `{{ variable }}` — safe (auto-escaped)
- `{{ variable | escape }}` — explicit escape (redundant but safe)
- `{{ variable | strip_html }}` — remove HTML tags (use for user-generated content)
- `{{ variable | json }}` — safe JSON serialization for JavaScript
- ❌ `{{ variable }}` with `|raw` disabled escaping — avoid unless absolutely necessary

### JavaScript Security
- Use `textContent` instead of `innerHTML` for dynamic content
- Sanitize user input before rendering: no `innerHTML` with untrusted data
- Use `fetch()` with CORS headers; validate server responses
- Avoid `eval()` and dynamic `Function()` constructors
- Use Content Security Policy (CSP) headers (Shopify provides defaults)

## Performance Monitoring
- Use Shopify's built-in performance tools: Page Speed Insights, Lighthouse
- Monitor Core Web Vitals (CLS, FID, LCP) in production
- Test on low-end devices and slow networks (Chrome DevTools throttling)
- Avoid render-blocking resources; prioritize above-the-fold content
- Use Shopify's Analytics to track custom metrics (if needed)

## Resources
- [Shopify Theme Architecture](https://shopify.dev/themes/architecture)
- [Liquid Reference](https://shopify.dev/api/liquid)
- [Section Schema Documentation](https://shopify.dev/themes/architecture/sections/section-schema)
- [Shopify CLI Documentation](https://shopify.dev/themes/tools/cli)
- [Theme Performance Best Practices](https://shopify.dev/themes/best-practices/performance)
- [Web Vitals Optimization](https://web.dev/vitals/)
