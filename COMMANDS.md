# ThinkingNotes.dev - Build & Deploy Commands

## Local Development
```bash
# Build and preview locally with one command
make serve
# Visit: http://localhost:8000
# Press CTRL-C to stop
```

## Deploy to Production
```bash
# Three simple commands:
make deploy
git commit -m "content: your commit message"
git push origin master

# What make deploy does:
# 1. Builds site with production config
# 2. Copies output to root
# 3. Stages files for git commit
```

## Writing New Posts

1. Create markdown file in `content/`:
```bash
touch "content/My New Post.md"
```

2. Add front matter:
```markdown
Title: Your Post Title
Date: 2025-10-28 12:00
Category: Technology
Tags: tag1, tag2
Slug: my-new-post
Summary: Brief summary

Your content here...
```

3. Test locally: `make serve`
4. Deploy: `make deploy` → `git commit` → `git push`

## Quick Reference

| Command | Purpose |
|---------|---------|
| `make serve` | Build + preview locally |
| `make deploy` | Build + stage for production |
| `make clean` | Remove generated files |
| `make html` | Build production site |

## Repository Info
- **Live Site:** https://thinkingnotes.dev
- **GitHub:** https://github.com/christianvuye/christianvuye.github.io
- **Theme:** Flex (in themes/Flex/)
- **Python Version:** 3.12
- **Package Manager:** uv

## Commit Conventions
- `content:` - Blog posts
- `feat:` - New features
- `fix:` - Bug fixes
- `chore:` - Maintenance
- `build:` - Site rebuilds

## Full Workflow Example
```bash
# 1. Write a new post
vim content/my-new-post.md

# 2. Preview locally
make serve
# Check http://localhost:8000, press CTRL-C when done

# 3. Deploy
make deploy
git commit -m "content: add new post about XYZ"
git push origin master

# Done! Live in 2 minutes at https://thinkingnotes.dev
```
