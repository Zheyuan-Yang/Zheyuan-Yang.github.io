# Zheyuan Yang's Personal Blog

A personal blog built with [Hexo](https://hexo.io/) and deployed to GitHub Pages.

## Quick Start

### Local Development

1. **Install dependencies:**
   ```bash
   npm install
   ```

2. **Start local server:**
   ```bash
   npm run server
   ```
   Visit `http://localhost:4000` to preview your blog.

3. **Create new content:**
   ```bash
   # Create a new post
   hexo new post "My Post Title"
   
   # Create a new page
   hexo new page "page-name"
   ```

## Deployment

**📖 For comprehensive deployment instructions, see [DEPLOYMENT.md](./DEPLOYMENT.md)**

### Quick Deploy (Automated)

Simply push your changes to the `dev` branch:
```bash
git add .
git commit -m "Your changes"
git push origin dev
```

GitHub Actions will automatically build and deploy your site to `https://zheyuan-yang.github.io/`

## Project Structure

```
.
├── _config.yml           # Hexo configuration
├── source/              # Source files for your blog
│   ├── _posts/         # Blog posts
│   └── images/         # Image assets
├── themes/             # Hexo themes
│   └── cube/          # Current theme
└── public/            # Generated static files (don't commit)
```

## Available Commands

| Command | Description |
|---------|-------------|
| `npm run server` | Start local development server |
| `npm run build` | Generate static files |
| `npm run clean` | Clean generated files |
| `npm run deploy` | Deploy to GitHub Pages manually |

## Resources

- [Hexo Documentation](https://hexo.io/docs/)
- [Deployment Guide](./DEPLOYMENT.md)
- [Hexo Commands Reference](https://hexo.io/docs/commands)