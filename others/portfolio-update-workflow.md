# Portfolio Git Workflow

## Standard Workflow

For most updates, use the following workflow:

# 1. Review changes
```
git status
git diff
```
# 2. Run linting
```
npm run lint
```
# 3. Check Astro/TypeScript and build production site
```
npm run build
```
# 4. Test production build locally
```
npm run preview
```
# 5. Stage changes
```
git add .
```
# 6. Review staged changes
```
git status
git diff --cached
```
# 7. Commit
```
git commit -m "Describe your change"
```
# 8. Push to GitHub
```
git push origin main
```
## Content-Only Workflow

# 1. Review changes
```
git status
git diff
```
# 2. Validate and build
```
npm run build
```
# 3. Stage changes
```
git add .
```
# 4. Review staged changes
```
git diff --cached
```
# 5. Commit
```
git commit -m "Update blog post"
```
# 6. Push to GitHub
git push origin main
