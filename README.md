# TJ Brown | Tech Blog

This is a personal tech blog built with [Hugo](https://gohugo.io/) and the [Toha](https://github.com/hugo-themes/toha) theme. It features a professional, high-tech design focused on AI and software engineering.

## 🚀 Getting Started

### Prerequisites

You need to have Hugo installed on your machine. If you're on macOS using Homebrew:

```bash
brew install hugo
```

### Local Development

To run the site locally with live reloading:

```bash
hugo server
```

The site will be available at [http://localhost:1313](http://localhost:1313).

## 📝 Managing Content

### Creating a New Blog Post

To create a new post, use the following command:

```bash
hugo new content blog/my-new-post.md
```

This will create a new file in `content/blog/my-new-post.md` with the default front matter.

### Post Front Matter

Each post should have a header (front matter) like this:

```yaml
---
title: "My New Post"
date: 2026-02-11T12:00:00Z
description: "A brief summary of the post."
tags: ["AI", "Tech"]
categories: ["Software"]
draft: false
---
```

### Adding Images

To include images in your posts:

1.  Place your image file in `static/img/`.
2.  Reference it in your markdown file using the following syntax:

```markdown
![Description of image](/img/your-image.png)
```

Alternatively, you can use Hugo's [Page Bundles](https://gohugo.io/content-management/page-bundles/) by creating a folder for your post and keeping the image inside that folder.

### Adding Code Blocks

Use triple backticks followed by the language identifier for syntax highlighting:

```markdown
```python
def hello_world():
    print("Hello, AI!")
```
```

## 🛠 Configuration

- **General Settings**: `config/_default/hugo.toml`
- **Theme Customization**: `config/_default/params.toml`
- **Author & Socials**: `config/_default/languages.en.toml`
- **Navigation Menus**: `config/_default/menus.en.toml`

## 🏗 Building for Production

To generate the static files for deployment:

```bash
hugo
```

The output will be in the `public/` directory.

## 🎨 Theme Details

This site uses the **Toha** theme. For advanced features, refer to the [Toha Documentation](https://toha-docs.netlify.app/posts/).
