# Docusaurus Blog Project

A developer portfolio and learning blog built with Docusaurus.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Quickstart](#quickstart)
- [Description](#description)
- [Configuration Steps](#configuration-steps)
- [Further References](#further-references)

## Prerequisites
- Node.js
- pnpm installed globally

## Quickstart

1. Clone repository: `git clone https://github.com/Developer-Akademie-DevSecOpsKurs/dev-blog-template`
2. Copy `example.env` to `.env` and fill in required environment variables
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
   - Created Docusaurus project from the provided template
   - Installed dependencies using pnpm

2. **Environment configuration**
   - Added `GIT_REPOSITORY_URL` to `example.env`
   - Created a configuration variable in `docusaurus.config.ts` that reads the environment variable and provides a fallback value

3. **Docusaurus configuration**
   - Updated the site branding in `docusaurus.config.ts` (`title`, `tagline`, `url`)
   - Configured repository url dynamically via environment variable
   - Updated navbar (title, repository link)
   - Updated footer structure and removed Community section

4. **Deployment setup**
   - Configured the project for GitHub Pages deployment
   - Enabled GitHub Pages using GitHub Actions in the repository settings
   - Automatic deployment is triggered on pushes to `main`

## Further References

- https://docusaurus.io/
- https://docs.github.com/en/actions
- https://pages.github.com/