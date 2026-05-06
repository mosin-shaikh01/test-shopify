# Shopify Online Store 2.0 Theme Development Guide

## Project Overview
This is a Shopify Online Store 2.0 theme project built with Liquid, JavaScript, and CSS. The theme follows Shopify's modular section architecture and is version-controlled with Git.

## Directory Structure
- **assets/** — CSS, JavaScript, SVG icons, and other static files
- **blocks/** — Reusable block components
- **config/** — Theme configuration (settings_schema.json)
- **layout/** — Base layout templates (theme.liquid, password.liquid)
- **locales/** — Internationalization files
- **sections/** — Dynamic section templates
- **snippets/** — Reusable Liquid partials
- **templates/** — Page templates

## Shopify 2.0 Structure Rules

### Sections
- Use sections/ for all dynamic components rendered in theme editor
- Each section must have a {% schema %} JSON block defining settings
- Sections support blocks for repeatable sub-components
- Use section groups to organize related sections
- Avoid hardcoding; expose all customizable values in schema settings

### Blocks
- Use blocks/ for block templates referenced by section schemas
- Blocks inherit parent section context and settings
- Block naming: descriptive, lowercase, hyphenated

### Snippets
- Use snippets/ for reusable Liquid partials
- Keep snippets focused and small (under 50 lines)
- Pass data via include parameters
- Avoid global dependencies

### Templates
- Use templates/ for page-level templates
- Standard: product.liquid, collection.liquid, page.liquid, etc
- Custom: prefix with template name
- Templates should orchestrate sections; avoid heavy logic

## Performance Rules

### Lazy Loading
- Use native loading="lazy" on images not above fold
- Implement native lazy loading for img and iframe elements
- Use Intersection Observer for custom triggers only when necessary
- Preload critical images with link rel="preload"

### Minimal JavaScript
- Inline critical JavaScript in head (< 10KB)
- Defer non-critical JS with defer or async
- Use native Web APIs before importing libraries
- Avoid jQuery, polyfills, or large dependencies
- Load third-party scripts asynchronously

### CSS Optimization
- Use CSS Grid and Flexbox for layouts
- Minimize CSS-in-JS; prefer static stylesheets
- Use media queries for responsive design
- Combine critical CSS into base.css
- Defer non-critical CSS appropriately

### Image Optimization
- Use Shopify's image CDN via {{ image | image_url }}
- Serve responsive images with srcset and sizes
- Optimize dimensions: use image_url: width=400
- Lazy load below-fold images
- Compress before upload

## Liquid Best Practices

### Variables & Scope
- Use descriptive names: product_title, is_featured
- Scope to section: section.settings.color
- Minimize reassignments; use filters
- Use capture for complex strings

### Control Flow
- Prefer if/elsif/else over nested unless
- Use case/when for multiple branches
- Avoid deep nesting; extract to snippets
- Use blank and nil checks correctly

### Filters
- Chain filters: {{ product.title | upcase | truncate: 30 }}
- Use built-in filters
- Common: date, money, size, split, join, map, where
- Use default filter for fallbacks

### Loops
- Use for with pagination: {% for product in collection.products limit: 12 %}
- Use break/continue sparingly
- Avoid tablerow; use div with CSS Grid
- Use limit to prevent too many renders

## CSS & Styling Rules

### CSS Architecture
- Use CSS Grid and Flexbox
- Avoid floats and absolute positioning
- Use CSS custom properties for colors, spacing
- Group related styles by component
- Keep specificity low; avoid !important

### Naming Conventions
- Use BEM: .product-card, .product-card__image
- Prefix JS hooks: .js-toggle-menu, .js-add-to-cart
- Use descriptive names; avoid generic ones

### Responsive Design
- Mobile-first approach
- Use media queries: @media (min-width: 768px)
- Define breakpoints as CSS variables
- Test on real devices

## Liquid Deprecation

### Avoid Deprecated
- ❌ tablerow — use div with CSS Grid
- ❌ Custom filters — use built-in filters
- ❌ assign with complex logic — use capture
- ❌ Global variables — use explicit variables
- ✅ Use modern filters: where, map, first, last

## Safe Git Workflow

### Branching
- Always use feature branches
- Never commit to main without review
- Use descriptive names: feature/add-section, fix/cart-scroll
- Merge via pull request

### Commit Messages
- Use clear, imperative messages
- Reference issue numbers if applicable
- Keep commits atomic
- Example: "Add lazy loading to product images"

### Protected Files
- config/settings_data.json — never commit
- .env — never commit (use .env.example)
- Large binaries — use Git LFS

### Deployment Safety
- Test locally with: shopify theme dev
- Use Git tags for releases
- Pull latest main before deploying
- Review all changes before merge

## Production-Safe Code Generation

### DO
- ✅ Generate semantic HTML
- ✅ Use Liquid auto-escaping
- ✅ Use CSRF tokens for forms
- ✅ Validate on server-side
- ✅ Use data-* attributes for JS hooks
- ✅ Test locally before committing
- ✅ Use section presets

### DON'T
- ❌ Hardcode API keys or credentials
- ❌ Use eval() or new Function()
- ❌ Generate unescaped HTML
- ❌ Disable Content Security Policy
- ❌ Mix Liquid and JS logic without separation
- ❌ Use innerHTML in JavaScript
- ❌ Commit unminified vendor libraries

### Liquid Security
- {{ variable }} — safe (auto-escaped)
- {{ variable | escape }} — explicit escape
- {{ variable | strip_html }} — remove HTML tags
- {{ variable | json }} — safe JSON serialization
- ❌ Avoid |raw filter unless necessary

### JavaScript Security
- Use textContent instead of innerHTML
- Sanitize user input
- Use fetch() with CORS headers
- Avoid eval() and dynamic Function()
- Use Content Security Policy headers

## Performance Monitoring
- Use Page Speed Insights, Lighthouse
- Monitor Core Web Vitals (CLS, FID, LCP)
- Test on slow devices and networks
- Prioritize above-the-fold content

## Resources
- [Shopify Theme Architecture](https://shopify.dev/themes/architecture)
- [Liquid Reference](https://shopify.dev/api/liquid)
- [Section Schema](https://shopify.dev/themes/architecture/sections/section-schema)
- [Shopify CLI](https://shopify.dev/themes/tools/cli)
- [Theme Performance](https://shopify.dev/themes/best-practices/performance)
