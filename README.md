# Reveal & Remember

Developer documentation for the Astro version of Emily's site.

## Stack

- Astro
- Static site output
- Vercel for hosting
- GitHub for source control and deploy triggers

## Requirements

- Node.js 20+
- npm

## Getting Started

Install dependencies:

```bash
npm install
```

Start local dev server:

```bash
npm run dev
```

Build production output:

```bash
npm run build
```

Preview production build locally:

```bash
npm run preview
```

## Project Structure

```text
public/
  images/            Static image assets copied directly to the build output
src/
  components/        Shared UI pieces like header, footer, quote sections
  data/              Shared site data such as nav items and Calendly URL
  layouts/           Base page shell and shared document structure
  pages/             Route files
  styles/            Global and page-specific CSS
```

## Routes

- `/` -> home page
- `/about` -> about page
- `/resources` -> resources page
- `/testimonials` -> testimonials page

Astro generates static HTML into `dist/` during build.

## Editing Guidance

### Content changes

For routine copy/content updates, start in:

- `src/pages/index.astro`
- `src/pages/about.astro`
- `src/pages/resources.astro`
- `src/pages/testimonials.astro`
- `src/data/site.ts`

Use `src/data/site.ts` for anything shared across pages, especially:

- navigation labels/links
- site title/description
- Calendly URL

### Styling changes

Global/shared styles:

- `src/styles/global.css`

Page-specific styles:

- `src/styles/home.css`
- `src/styles/about.css`
- `src/styles/resources.css`
- `src/styles/testimonials.css`

Preferred approach:

- keep shared tokens and layout primitives in `global.css`
- keep page-specific styling in the matching page stylesheet
- avoid reintroducing large inline style blocks unless there is a strong reason

### Assets

Put images in:

```text
public/images/
```

Reference them with root-relative paths, for example:

```astro
<img src="/images/emily-photo.jpg" alt="Emily" />
```

Do not put content images back at the repo root.

## Navigation and Shared Layout

Shared shell files:

- `src/layouts/BaseLayout.astro`
- `src/components/Header.astro`
- `src/components/Footer.astro`

If you need to change fonts, metadata defaults, footer text, or the global nav behavior, start there.

## Responsive Design Notes

The current migration keeps Emily's original visual direction but improves implementation quality.

Areas intentionally adjusted for responsiveness:

- header navigation collapses to a mobile toggle
- multi-column sections stack on smaller screens
- testimonials page switches from absolute-positioned desktop composition to stacked cards on mobile

When making future changes, check desktop and mobile before merging.

## Deployment

Intended deployment flow:

1. Push changes to GitHub
2. Vercel builds from the connected branch
3. Vercel serves the static `dist/` output

Expected Vercel settings:

- Framework preset: Astro
- Build command: `npm run build`
- Output directory: `dist`

In most cases Vercel should detect these automatically.

## Recommended Workflow

1. Create a branch for each change
2. Run `npm run dev`
3. Make content/style/component updates
4. Run `npm run build` before pushing
5. Open the Vercel preview deployment for final review

## Known Current Constraints

- Some book/resource artwork from the original HTML was not available as clean local assets, so a few resource cards use styled placeholders instead of embedded base64 blobs
- The testimonials page still uses remote Unsplash background imagery inherited from the original design
- Google Analytics is not wired in yet
- No CMS/editor workflow exists yet for nontechnical editing

## Future Improvements

Likely next technical tasks:

- add favicon/site metadata refinements
- add analytics
- add Open Graph images and social metadata
- add a lightweight content-editing workflow if Emily needs self-service updates
- localize any remaining remote images if desired
