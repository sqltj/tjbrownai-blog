# CLAUDE.md - Project Workflow & Conventions

## Branching & PR Strategy

### ✅ ALL Work Uses Feature Branches and PRs

**Rule:** Never commit directly to `main`. **Everything** goes through feature branches and pull requests, including:
- Blog posts
- Features
- Fixes
- Documentation changes

This includes CLAUDE.md itself, documentation updates, configuration changes—everything.

### Naming convention:
- Blog posts: `feature/blog-post-NN-descriptive-title`
- Features: `feature/add-x-feature`
- Fixes: `fix/description-of-fix`
- Docs: `feature/docs-description`

**Example:**
```bash
# Create a new feature branch
git checkout -b feature/blog-post-07-advanced-metric-views

# Make changes
# ... edit files ...

# Commit to the branch
git add content/posts/07-advanced-metric-views.md
git commit -m "feat: add blog post 07 - Advanced Metric Views"

# Push and open a PR
git push -u origin feature/blog-post-07-advanced-metric-views
# Then open PR on GitHub for review
```

### ✅ DO: Submit PRs for Review Before Merging

**Process:**
1. Create feature branch (see above)
2. Commit changes to feature branch
3. Push branch: `git push -u origin feature/...`
4. Open PR on GitHub
5. Review the PR (check for quality, consistency, correctness)
6. Merge to main only after review

**PR Checklist:**
- [ ] All changes committed to feature branch (not main)
- [ ] Commit messages are clear and descriptive
- [ ] Content is complete and publication-ready
- [ ] No merge conflicts with main
- [ ] Files are in correct location

### ❌ DON'T: Commit Directly to Main

```bash
# ❌ WRONG: Direct changes to main
git checkout main
git add content/posts/new-post.md
git commit -m "add new post"

# ✅ RIGHT: Use feature branch
git checkout -b feature/blog-post-new
git add content/posts/new-post.md
git commit -m "feat: add blog post - New Post"
git push -u origin feature/blog-post-new
# Then open PR
```

---

## Blog Post Series: Databricks Metric Views

### Overview
6-part blog post series on "From Power BI Semantic Models to Databricks Metric Views," targeting Power BI developers transitioning to Databricks.

### Posts & Branches

| Post | Branch | Status |
|------|--------|--------|
| 01 - Introduction | `feature/blog-post-01-metric-views-intro` | ✅ Ready for review |
| 02 - Grain Control | `feature/blog-post-02-grain-control` | ✅ Ready for review |
| 03 - Dimension Cardinality | `feature/blog-post-03-dimension-cardinality` | ✅ Ready for review |
| 04 - Measures & Child Views | `feature/blog-post-04-measures-child-views` | ✅ Ready for review |
| 05 - Governance & Security | `feature/blog-post-05-governance-security` | ✅ Ready for review |
| 06 - Dashboards & Genie | `feature/blog-post-06-dashboards-genie` | ✅ Ready for review |

### Content Requirements for Each Post

- [ ] Front matter: title, date, draft status, categories, tags
- [ ] Opening hook/context
- [ ] Recap from previous post
- [ ] Main content with clear sections
- [ ] Code examples (YAML, SQL) with annotations
- [ ] Real AdventureWorksDW examples
- [ ] Natural anti-pattern callouts (woven in, not separate section)
- [ ] Next steps / transition to next post
- [ ] References & resources section

---

## Project Structure

```
tjbrownai-blog/
├── content/
│   ├── posts/           ← Blog posts go here
│   └── en/              ← Hugo content structure
├── CLAUDE.md            ← This file (project conventions)
├── AGENTS.md            ← Repository guidelines
├── .github/
│   └── copilot-instructions.md ← GitHub-specific instructions
├── hugo.toml            ← Hugo configuration
└── README.md            ← Project overview
```

---

## Key Principles

### 1. One Branch = One Logical Change
Each feature branch should represent one cohesive change. For blog posts, that's one post per branch.

### 2. Commit Messages Are Clear
Follow conventional commits:
- `feat:` for new features/content
- `fix:` for fixes
- `docs:` for documentation
- `refactor:` for code reorganization

Example: `feat: add blog post 01 - Introduction to Metric Views`

### 3. PRs Exist for Review, Not Just Merging
Before merging any PR:
- Review content for clarity and accuracy
- Check formatting and consistency
- Verify code examples work/are correct
- Ensure blog post fits the series narrative

### 4. Draft Status
When creating blog posts, set `draft: true` in front matter until ready to publish. This prevents them from appearing in listings before review.

---

## Workflow for Creating New Blog Posts

1. **Create branch:** `git checkout -b feature/blog-post-NN-title`
2. **Write post:** Create file in `content/posts/NN-descriptive-title.md`
3. **Include front matter:**
   ```yaml
   ---
   title: "Post Title"
   date: 2026-02-18
   draft: true
   categories: ["Category"]
   tags: ["tag1", "tag2"]
   ---
   ```
4. **Commit to branch:** `git add content/posts/...` and `git commit -m "feat: add blog post NN - Title"`
5. **Push branch:** `git push -u origin feature/blog-post-NN-title`
6. **Open PR:** Create PR on GitHub with description
7. **Review:** Self-review for content, code, clarity
8. **Merge:** After review, merge to main
9. **Deploy:** Hugo builds and deploys on merge to main (if CI/CD is configured)

---

## Common Conventions

### Blog Post Naming
- File: `NN-descriptive-lowercase-title.md` (e.g., `01-introduction-metric-views.md`)
- Branch: `feature/blog-post-NN-descriptive-title`
- Front matter title: "Human Readable Title With Capitals"

### Categories
Blog posts in this series use:
- `Databricks`
- `Semantic Modeling`

### Tags
Relevant tags for Databricks Metric Views series:
- `metric-views`
- `databricks`
- `semantic-layer`
- `dimensional-modeling`
- `governance`
- `data-modeling`
- `power-bi`

---

## Tools & Workflows

### Viewing Changes Before Commit
```bash
git diff content/posts/  # See what changed
git status               # See what's staged
```

### Pushing to Remote
```bash
git push -u origin feature/blog-post-01-metric-views-intro
# Only first time; after that, use: git push
```

### Creating PR via CLI
```bash
gh pr create --title "Blog post 01: Introduction to Metric Views" \
  --body "Adds introductory post mapping Power BI concepts to Databricks Metric Views"
```

---

## Notes for Claude

When working on this project:

1. **Always create a feature branch** for new work (blog posts, features, fixes, docs)
2. **Commit to the feature branch**, not main
3. **Use the PR workflow** — each logical change gets its own PR for review
4. **Keep commit messages clear** — future readers (including you) will thank you
5. **Mark blog posts as drafts** until they're ready to publish
6. **Follow the blog post structure** outlined above
7. **Use the databricks-metric-views skill** for technical accuracy on metric views
8. **Reference AdventureWorksDW** in examples for consistency across the series

---

## References

- [Conventional Commits](https://www.conventionalcommits.org/)
- [Hugo Documentation](https://gohugo.io/documentation/)
- [GitHub Pull Requests](https://docs.github.com/en/pull-requests)
- [Databricks Metric Views](https://docs.databricks.com/en/metric-views/)
