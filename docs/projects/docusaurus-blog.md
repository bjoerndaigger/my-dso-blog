# Docusaurus Blog Project

A developer portfolio and learning blog built with Docusaurus.

## TOC

- [Prerequisites](#prerequisites)
- [Quickstart](#quickstart)
- [Description](#description)
- [Configuration Steps](#configuration-steps)
- [Further References](#further-references)

## Prerequisites
- Node.js
- pnpm installed globally

## Quickstart

1. Clone repository
2. Copy `example.env` to `.env` and fill in your values
3. Install dependencies with `pnpm install`
4. Start local development with `pnpm start`
5. Test production build with `pnpm build`
6. Deploy via GitHub Actions (automatic on push to main branch)

## Description

This project is based on a Docusaurus template and extended into a personal developer blog and portfolio.

The main focus of the configuration was:
- adapting branding (title, tagline, favicon, navbar)
- integrating environment-based configuration using `.env`
- enabling GitHub Pages deployment
- customizing blog and documentation editing links
- structuring navigation and footer content

## Configuration Steps

The setup was performed in several steps:

1. **Project initialization**
   - Created Docusaurus project from template
   - Installed dependencies using pnpm

2. **Environment configuration**
   - Added `.env` variables for repository and deployment settings
   - Introduced fallback values in `docusaurus.config.ts`

3. **Docusaurus configuration**
   - Updated `title`, `tagline`, and base `url`
   - Configured repository url dynamically via environment variable
   - Adjusted navbar and footer structure
   - Removed unused sections (Community links)

4. **Deployment setup**
   - Activated automatic deployment to GitHub Pages on push to main branch

## Further References

- https://docusaurus.io/
- https://docs.github.com/en/actions
- https://pages.github.com/