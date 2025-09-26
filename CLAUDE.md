# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an Astro-based personal blog website built with the "Yi" theme. The site is a content-focused blog that supports multiple languages (English, Chinese Simplified, Chinese Traditional, Czech), dark mode, RSS feeds, and various content types including blog posts and feed entries.

## Development Commands

### Core Commands
- `npm run dev` or `pnpm dev` - Start development server (localhost:4321)
- `npm run build` or `pnpm build` - Build production site to ./dist
- `npm run preview` or `pnpm preview` - Preview production build
- `astro` - Access Astro CLI directly

### Package Manager
The project uses `pnpm` as the preferred package manager (see README.md). Always use `pnpm` commands when available.

## Architecture & Configuration

### Main Configuration Files
- `src/consts.ts` - Primary configuration file containing:
  - Site metadata (title, description, author, avatar)
  - Navigation categories and menu structure
  - Personal links and social media
  - Comment system settings (Waline/Giscus)
  - Analytics configuration (Umami/Google Analytics)
  - Donation settings
  - Friendship links

- `astro.config.js` - Astro framework configuration with:
  - Site URL and base path settings
  - MDX and Tailwind integrations
  - Expressive Code configuration with line numbers and collapsible sections
  - Custom remark/rehype plugins for extended Markdown functionality

### Content Structure
- `src/content/blog/` - Blog posts (Markdown/MDX files)
- `src/content/feed/` - Feed entries (shorter content pieces)
- `src/content/config.ts` - Content collection schemas using Zod validation

### Content Schema (Blog Posts)
Blog posts support these frontmatter fields:
- `title` (required) - Post title
- `date` (required) - Publication date
- `description` (optional) - Post description
- `tags` (optional) - Array or string of tags
- `category` (optional) - Post category
- `sticky` (optional) - Pin priority (higher = more prominent)
- `mathjax`, `mermaid` (optional) - Enable special rendering
- `draft` (optional) - Hide from production
- `toc`, `donate`, `comment` (optional) - Feature toggles
- `ogImage` (optional) - Social media image

### Styling System
- **Tailwind CSS** with custom configuration in `tailwind.config.js`
- **CSS Custom Properties** for theming (skin colors for light/dark mode)
- **Custom breakpoints**: sm(600px), md(720px), lg(840px), xl(960px), 2xl(1080px)
- **Typography**: JetBrains Mono font for code display
- **Component styles** in `src/styles/` directory

### Plugin Architecture
Custom remark/rehype plugins in `src/plugins/`:
- `remark-modified-time.mjs` - Add file modification timestamps
- `remark-asides.js` - Create aside/callout blocks
- `remark-collapse.js` - Collapsible content sections
- `remark-github-card.js` - GitHub repository cards
- `remark-button.js` - Custom button elements
- `lazy-load-image.js` - Image lazy loading optimization

### Internationalization (i18n)
- Language files in `src/i18n/` for en, zh-cn, zh-Hant, cs
- Language setting in `src/consts.ts` config object
- Content localization through frontmatter and templates

### Deployment Configuration
- **Vercel**: Ready for deployment with `vercel.json` config
- **GitHub Pages**: Requires baseUrl configuration in `src/consts.ts`
- **Site URL**: Set in both `astro.config.js` and `src/consts.ts` for proper sitemap/RSS generation

## Key Development Notes

### Content Creation
- Blog posts go in `src/content/blog/` as .md or .mdx files
- Feed entries go in `src/content/feed/` as .md files
- All content must include required frontmatter fields per schema

### Theme Customization
- Colors and theming via CSS custom properties in Tailwind config
- Icon system uses Remix Icon library
- Profile and navigation configured in `src/consts.ts`

### Analytics & Comments
- Multiple analytics providers supported (Umami, Google Analytics, Busuanzi)
- Comment systems: Waline or Giscus
- All configured through `src/consts.ts`

### URL Structure
- Blog: `/blog/[page]` (paginated)
- Individual posts: `/blog/[slug]`
- Archive: `/archive/[page]` (by date)
- Categories: `/category/[category]`
- Tags: `/tags/[tag]`
- Search: `/search` with JSON API endpoint

### TypeScript Configuration
- Path aliases: `@/*` maps to `src/*`
- JSX support with Solid.js import source
- Astro environment types included