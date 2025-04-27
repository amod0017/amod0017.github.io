---
layout: post
title: "Creating Your Blog with a Jekyll Theme"
author: "Amod"
categories: [ tech ]
tags: [ blogging, jekyll, setup ]
comments: true
---

Starting a personal blog or a small developer page doesn't have to be complicated. I recently set up this blog using a free Jekyll theme, and it was much simpler than I expected.

Here’s a quick outline of the steps I followed:

### 1. Choosing a Theme

I selected the [Mediumish Jekyll Theme](https://github.com/wowthemesnet/mediumish-theme-jekyll) because it was clean, minimal, and free to use.

You can explore many free themes on GitHub by searching "Jekyll blog theme."

### 2. Setting Up the Environment

To get Jekyll working locally on Windows:
- Install [Ruby with DevKit](https://rubyinstaller.org/)
- Install Bundler and Jekyll:

  ```bash
  gem install bundler jekyll
  ```

- Clone your theme repo:

  ```bash
  git clone https://github.com/wowthemesnet/mediumish-theme-jekyll.git
  cd mediumish-theme-jekyll
  ```

- Install dependencies:

  ```bash
  bundle install
  ```

- Serve your blog locally:

  ```bash
  bundle exec jekyll serve
  ```

### 3. Customizing the Blog

After the setup, I:
- Cleaned the navigation menu to keep it simple: Home, Tech Blog, Projects, About Me
- Deleted demo posts
- Updated the About page
- Added a personal favicon
- Removed the default theme branding (like "Mediumish" footer text)

### 4. Publishing

I pushed the final version to a GitHub repository named `yourusername.github.io`, so it gets hosted automatically with GitHub Pages.

---

It was a fun process.  
If you're looking to start your own site without dealing with heavy CMS platforms, Jekyll is definitely worth a try.
