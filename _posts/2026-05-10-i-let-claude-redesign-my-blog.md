---
layout: post
title: "I Let Claude Redesign My Blog. It Crashed My Machine First."
image: assets/images/post-claude-redesign.svg
author: "Amod"
categories: [ tech, ai ]
tags: [ claude, superpowers, jekyll ]
comments: true
---

I started this blog last year. Posted two or three times, lost momentum, and quietly stopped. This year I decided to try again, but the old theme felt off and I wasn't excited to write into something I didn't like looking at.

I wanted a redesign: a clean hero section, a card grid, a proper nav with category dropdowns. The problem is I'm not a UI person. I have opinions, not expertise. Hand-crafting CSS until 2am didn't sound like a good use of energy.

So I tried something different.

### The Tool: Superpowers for Claude Code

I'd been using [Claude Code](https://claude.ai/code), Anthropic's CLI that lets you work with Claude directly in your terminal. I came across the **Superpowers plugin**, a set of workflow skills built on top of it.

One thing worth knowing upfront: **Superpowers is exclusive to Claude Code.** If you use GitHub Copilot or another AI coding tool, there's nothing quite like this available yet. It's not just a skill loader. It's a structured workflow system that covers the entire development process:

- **Brainstorming:** asks clarifying questions one at a time, produces a written design spec
- **Writing plans:** turns the spec into a numbered task list with exact files, steps, and commit instructions
- **Subagent-driven development:** dispatches a fresh Claude instance per task, then runs a spec compliance review and a code quality review before moving on
- **Plus:** TDD, debugging, git worktree management, branch finishing, and more

The core idea is that it imposes discipline on AI-assisted work. You don't just throw prompts at it and hope. You go through a process: spec first, plan second, implementation third, with reviews between each step.

### The Brainstorming Part

The brainstorming skill starts by asking questions one at a time before touching any code. Instead of jumping straight to implementation, it asked things like:

> *"Should the home page feel more like a magazine (visual, image-heavy) or a feed (scannable, text-first)?"*

For someone like me with opinions but no design vocabulary, this was genuinely useful. It gave me a way to express preferences without having to know the right CSS terms for them.

What made it click, though, was the visual companion. The brainstorming skill offered to open a local URL in the browser and show me actual mockups as we went. So instead of describing "I want a hero image with text overlay," I could look at two rendered layouts side by side and just pick. Here's a real moment from the session, deciding between light and dark mode:

![The visual companion previewing the theme toggle feature — showing how the blog looks in both light and dark mode as a user would experience it.]({{ site.baseurl }}/assets/images/post-superpowers-darkmode.png)
*The visual companion previewing the theme toggle — light and dark side by side, exactly as a user would see it.*

It felt like having a UX designer on the other end, except one who could spin up a mockup in seconds and immediately iterate on whatever I didn't like. For someone who thinks visually but can't design, that was the part that made it feel real.

After several exchanges it produced a written design spec: hero post, two-column grid, category-based nav dropdown, dark mode toggle, updated About page. Saved to a file, committed to git. You can read it here if you're curious: [frontend-redesign-design.md](https://github.com/amod0017/amod0017.github.io/blob/master/docs/superpowers/specs/2026-05-10-frontend-redesign-design.md).

From that point everything was measured against it.

### Actually Building It

The writing-plans skill turned the spec into a numbered task list, each task with specific files to touch, exact steps, and commit instructions. [Here's the actual plan it generated.](https://github.com/amod0017/amod0017.github.io/blob/master/docs/superpowers/plans/2026-05-10-frontend-redesign.md) Not "add a grid layout" but "modify `_layouts/home.html`, add `.post-grid` and `.post-card` to `assets/css/main.css`, commit."

Then subagent-driven development took over: it dispatched a fresh Claude instance per task, ran a spec compliance review ("did the code match what the spec said?"), then a code quality review. Only after both passed did it move to the next task.

### Where Things Broke

Here's the honest part.

**1. Docker crashed immediately.**

My local Ruby environment was too old for the required gems. I switched to Docker, the official `jekyll/jekyll` image. On my Apple Silicon Mac, that image has no ARM64 variant, so it runs under Rosetta via `--platform linux/amd64`. That worked fine until the site tried to compile `main.scss`. The `sass-embedded` gem uses a native binary built for x86, and it dies under emulation:

```
Error: EOFError: end of file reached
```

The fix was unglamorous: rename `main.scss` to `main.css`. No SCSS features were being used anyway. The problem dissolved.

**2. Post excerpts were empty on the home page.**

The card grid was pulling previews with `post.excerpt | strip_html`. That works fine unless a post opens with an `<img>` tag, in which case Jekyll's excerpt logic captures just the image, and `strip_html` leaves you with nothing.

Switching to `post.content | strip_html | strip | truncatewords: 25` fixed it for every post, regardless of how each one started.

**3. The nav dropdown closed before you could click anything.**

The dropdown appeared on hover but had a 12px gap between the nav item and the menu (from `top: calc(100% + 12px)`). Moving the mouse down into that dead zone broke the hover chain and the menu vanished. Changing to `top: 100%` made the menu flush, contiguous hover area, problem gone.

To be fair, these are the kinds of issues Superpowers should ideally catch during its spec compliance and code quality reviews. It didn't, and I had to debug them manually. That's worth acknowledging.

### The Result

The whole thing took a few hours. The blog now has a hero post, two-column card grid, category nav, and dark mode. It looks like something I'd have spent a weekend on if I did it manually.

**What worked well:** the spec-first approach. Having a written design document meant the AI had something concrete to check against, not just my vague memory of what I'd asked for two hours earlier. For someone without a design background, being forced to answer specific questions before any code is written is genuinely clarifying.

**What felt awkward:** anything touching the local environment was fully outside the AI's reach. Docker issues, native binary crashes, port conflicts, it could suggest fixes but I had to run the commands. That loop still needs a human.

I haven't used Superpowers on a large project yet, so I can't speak to that. But honestly, looking at the workflow, if you're already using Claude Code for something substantial, I'd strongly recommend trying it. The structure it imposes scales in a way that ad-hoc prompting just doesn't.

If you've used Superpowers or something similar on a bigger codebase, I'd love to hear how it went.

[Message me on LinkedIn](https://www.linkedin.com/in/amod0017/)

---

*Superpowers is a plugin for [Claude Code](https://claude.ai/code), Anthropic's CLI for working with Claude in your terminal.*
