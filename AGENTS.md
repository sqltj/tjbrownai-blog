# Repository Guidelines

## Project Structure & Module Organization
- `content/`: Markdown posts and section pages (e.g., `content/posts/future-of-ai.md`).
- `data/`: Site data files that power Toha sections and menus.
- `layouts/`: Local template overrides and shortcodes.
- `assets/`: Hugo Pipes assets (SCSS, JS, images) processed at build time.
- `static/`: Static files copied verbatim (images live in `static/img/`).
- `themes/toha/`: Upstream Toha theme; changes here should follow `themes/toha/AGENTS.md`.
- `public/` and `resources/`: Generated output; do not hand-edit.
- `hugo.yaml`: Main site configuration.

## Build, Test, and Development Commands
- `hugo server`: Run the local dev server with live reload at `http://localhost:1313`.
- `hugo server -D`: Preview drafts in `content/`.
- `hugo`: Build the production site into `public/`.
- `hugo new content posts/my-post.md`: Scaffold a new post with front matter.

## Coding Style & Naming Conventions
- Markdown posts use YAML front matter delimited by `---`.
- Prefer kebab-case filenames for posts and assets (`future-of-ai.md`, `ai-trends.png`).
- Keep front matter fields aligned with existing posts (`title`, `date`, `description`, `tags`, `categories`).
- Use short, descriptive headings and keep paragraphs focused for readability.
- Theme edits should follow Toha's conventions and linting guidance in `themes/toha/AGENTS.md`.

## Testing Guidelines
- This repo has no automated test suite.
- Validate changes by running `hugo server` locally and a clean `hugo` build before publishing.
- For theme changes, use the theme's recommended checks (see `themes/toha/AGENTS.md`).

## Commit & Pull Request Guidelines
- Use Conventional Commits style: `feat:`, `fix:`, `docs:`, `test:`; scopes are optional (`feat(skills): ...`).
- Keep subjects short and action-oriented; include issue/PR references when available.
- PRs should describe the content or layout change, link related issues, and include screenshots for visual updates.

### Branch Workflow (REQUIRED for ALL changes)
**All new work must use feature branches and submit PRs before merging to main.** This includes blog posts, features, fixes, and documentation changes. See `CLAUDE.md` for detailed conventions:

1. **Never commit directly to `main`** — Always create a feature branch
2. **Feature branch naming:**
   - Blog posts: `feature/blog-post-NN-descriptive-title`
   - Features: `feature/add-x-feature`
   - Fixes: `fix/description`
   - Docs: `feature/docs-description`
3. **Commit to your feature branch** with conventional commit messages
4. **Push and open a PR** for review
5. **Merge via PR**, not local merge

Example:
```bash
git checkout -b feature/blog-post-01-intro
# ... make changes ...
git commit -m "feat: add blog post 01 - Introduction"
git push -u origin feature/blog-post-01-intro
# Then open PR on GitHub
```

See `CLAUDE.md` for full workflow documentation and conventions.

## Content & Configuration Tips
- Add images under `static/img/` and reference them as `/img/<name>` in Markdown.
- For section or menu changes, update `data/` files and `hugo.yaml` instead of hardcoding content in templates.
