# Style Guide

How styling works on revealandremember.com. **Read this before changing any CSS or adding a page or section.** The goal: every page looks like it belongs to the same site, and new work reuses the shared building blocks instead of inventing one-off styles.

## Rules

1. **Use tokens, never raw values.** Colors, fonts, font sizes, shadows and borders are CSS variables in `src/styles/tokens.css`. Write `color: var(--deep-terracotta)`, not `color: #a84632`. Write `font-family: var(--font-serif)`, not `'Cormorant Garamond'`.
2. **Use the shared classes first.** Headings, body text, sections, backgrounds, cards and buttons all have global classes (listed below). A new section should be mostly global classes with little or no new CSS.
3. **Page CSS is only for things unique to that page** (the home hero, the Resources podcast tiles, the Contact accordion). Scope it under the page's wrapper class (`.home-page`, `.about-page`, `.resources-page`, `.testimonials-page`, `.contact-page`) or a class only that page uses.
4. **If two pages need the same thing, move it to the global layer** (`typography.css`, `layout.css` or `components.css`) instead of copying it.
5. **Don't add `!important`** and don't fight specificity with long selectors. If a shared class doesn't fit, add a modifier (`.heading-card--sm`) next to it in the global file.
6. **Check desktop and mobile** (the main breakpoint is 900px) before pushing.

## Files

| File | What's in it |
|---|---|
| `src/styles/global.css` | Entry point, loaded on every page. It only imports the layers below, in order. |
| `src/styles/tokens.css` | Design tokens: colors, surfaces, fonts, type scale, shadows, widths, spacing. |
| `src/styles/base.css` | Element defaults (`body`, `p`, `img`, focus outlines, `.sr-only`). |
| `src/styles/layout.css` | Page offset, content widths, section spacing, section backgrounds, `.text-center`, `.section-rule`. |
| `src/styles/typography.css` | Text roles: headings, eyebrow, quotes, body copy, color utilities. |
| `src/styles/components.css` | Page header, site header/nav, footer, quote banner, buttons, cards. |
| `src/styles/<page>.css` | One per page, imported by that page. Page-unique styles only. |

## Typography

Two fonts: **Cormorant Garamond** (`--font-serif`) for headings, quotes and decorative italics, and **Montserrat** (`--font-sans`) for body copy, labels, buttons and navigation. Body `<p>` text is Montserrat, weight 300, charcoal by default.

| Class | Use for | Notes |
|---|---|---|
| `.text-display` | Page title (`h1`) | Applied by `PageHeader`. |
| `.heading-section` | Section heading (`h2`) | **Default for new sections.** Serif, terracotta, `--text-section`. |
| `.heading-section--compact` | Section `h2` on About | Existing variant: smaller and medium weight. |
| `.heading-section--large` | Section `h2` on Testimonials | Existing variant: larger and medium weight. |
| `.heading-section--accent` | Section `h2` on Resources | Existing variant: burnt-orange. |
| `.heading-card` | Card / list-item heading (`h3`/`h4`) | Serif, 1.8rem, terracotta. Add `.heading-card--sm` for 1.5rem. |
| `.text-eyebrow` | Small uppercase label above content | Montserrat caps, burnt-orange. |
| `.text-quote` | Pull quotes / testimonials | Serif italic, terracotta. |
| `.prose-lg` | Container: every `<p>` inside gets 1.1rem / 1.8 | Default body size for sections. |
| `.prose` | Container: every `<p>` inside gets 1.04rem / 1.82 | Long-form reading (About, Contact). |
| `.text-accent` | Turn a heading or text burnt-orange | e.g. `class="heading-card text-accent"` |
| `.text-center` | Center-align a block | |

Type sizes live in `tokens.css` (`--text-display`, `--text-section`, `--text-card`, `--text-lead`, `--text-body-lg`, `--text-body`, `--text-sm`, `--text-xs`). If you need a size, use one of these before adding a new one.

## Layout and sections

A typical section:

```astro
<section class="section surface-alt prose-lg">
  <div class="section-inner">
    <h2 class="heading-section">Section title</h2>
    <p>Body copy…</p>
  </div>
</section>
```

| Class | What it does |
|---|---|
| `.section` | Standard vertical padding (`--section-space`). |
| `.section--compact-mobile` | Tighter padding under 900px (used on Home). |
| `.surface-base` / `.surface-alt` | Cream / warm-linen backgrounds. **Alternate them down the page.** |
| `.surface-sand` | Desert-sand feature background (About intro). |
| `.surface-dark` | Warm espresso background (`--espresso`). `<p>` turns cream and `.heading-section` turns sand automatically. |
| `.section-inner` | Centered 1200px content column (the gutter tightens on mobile). **Use it inside every section.** |
| `.section-inner--narrow` | Same, but 780px wide (Contact options). |
| `.wide-container` | Centered 1400px column (home hero, About intro). |
| `.page-shell` | Pushes content below the fixed header. Only for pages that don't start with `PageHeader` or a hero (currently About). |
| `.section-rule` | Short accent line under a heading (About). |

## Components

| Component / class | Use |
|---|---|
| `<PageHeader title subtitle>` (`src/components/PageHeader.astro`) | Title band at the top of inner pages. Use it on any new page. |
| `<QuoteBanner quote author tone>` (`src/components/QuoteBanner.astro`) | Full-width quote above the footer; `tone="sand"` (default) or `"sage"`. |
| `.cta-button` | The primary button (consult, send, join). Works on `<a>` and `<button>`. |
| `.card` | White card with a subtle border, burnt-orange left stripe and soft shadow. |
| `.accent-border` | Just the burnt-orange left stripe, for blocks with their own background. |
| `<a class="card-link">` around an `<article>` card | Makes the whole card clickable, with a lift on hover. |

## Adding a new page

1. Create `src/pages/<name>.astro` using `BaseLayout`, then `PageHeader`, then sections built from the classes above, then a `QuoteBanner` if it fits.
2. Wrap the page content in `<div class="<name>-page">`.
3. Only if the page needs something unique, add `src/styles/<name>.css`, import it in the page, and scope rules under `.<name>-page`.
4. Add the page to `site.nav` in `src/data/site.ts` if it belongs in the menu, and update the Routes list in `README.md`.

## How the cascade works

All shared CSS sits inside `@layer global`. Page stylesheets are not layered, so **a rule in a page stylesheet always beats a shared rule, whatever its specificity.** That makes overrides predictable, and dev matches production (Astro's production build loads page CSS *before* the shared CSS, so plain source order can't be trusted). It also means a page rule like `.my-card { color: … }` will silently override a shared class on that page, which is another reason to keep page CSS limited to page-unique things.

## Known inconsistencies (kept on purpose for now)

The CSS refactor preserved the site's appearance exactly, so these existing differences between pages are still there. **Don't copy them into new work; use the base classes.** Standardizing any of them would visibly change the site, so ask Emily before doing it.

- **Section headings** use four sizes/weights/colors across pages (`.heading-section` on Home plus the `--compact`, `--large` and `--accent` variants).
- **Card headings** vary in weight and spacing per page (Resources cards 600, Testimonials themes 500, Home cards bold).
- **Home "Accepting new clients" status text** was written as medium weight (500), but a more specific rule made it render light (300). It's light today; the dead 500 was removed.
- **Resources "by Author" lines** were written as moss green, but render charcoal for the same reason. They're charcoal today; the dead color was removed.
- **Home "Two Pathways" cards** use solid white, a larger soft shadow and no border, instead of the standard `.card`.
