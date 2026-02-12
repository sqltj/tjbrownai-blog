# TJ Brown | Tech Blog

This is a personal tech blog built with [Hugo](https://gohugo.io/) and the [Blowfish](https://blowfish.page/) theme. It features a professional, high-tech design focused on AI and software engineering.

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

This site uses the **Blowfish** theme. For advanced features like KaTeX math, Mermaid diagrams, or Chart.js, refer to the [Blowfish Documentation](https://blowfish.page/docs/).
