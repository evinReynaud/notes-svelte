# Svelte + Vite Demo
This is a demo project for Svelte and Vite based on [this arcticle](https://medium.com/the-web-tub/making-a-simple-note-app-using-svelte-and-vite-27edfa91a591)

# Extra steps
I added a few things compared to the previous article.

## Setup vite server port
Add the following line to `vite.config.ts`:
```typescript
// imports

export default defineConfig({
    // Other config
    server: {port: 4200}
})
```

## Fix global style bad practice
See [vite plugin for svelte FAQ](https://github.com/sveltejs/vite-plugin-svelte/blob/main/docs/faq.md#where-should-i-put-my-global-styles).
The global styles should be in their own file `global.scss` that is imported in the `main.ts` file.

## Deploy to GitHub Pages
Init git repo
```shell
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:evinReynaud/notes-svelte.git
git push -u origin main
```

Update `vite.config.ts` to add the base url:
```typescript
// imports

export default defineConfig({
    // Other config
    base: '/notes-svelte/'
})
```

Add GithHub workflow: create a file `.github/workflows/deploy-to-pages.yml` with the following content:
```yaml
name: deploy-to-pages
on:
  push:
    tags:
      - v*
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: |
          npm install
          npm run build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```
For more details see [the GitHub Actions documentation](https://docs.github.com/en/actions)
