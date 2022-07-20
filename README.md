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

## Add Eslint
See [this article](https://codechips.me/eslint-svelte-typescript/)

### Setup pretier
```shell
npm add -D prettier prettier-plugin-svelte
```

Create a file named `.prettierrc.cjs` in the root directory with the following content:
```javascript
module.exports = {
  arrowParens: 'avoid',
  singleQuote: true,
  printWidth: 90,
  plugins: ['prettier-plugin-svelte'],
  semi: true,
  svelteSortOrder: 'options-styles-scripts-markup',
  svelteStrictMode: false,
  svelteBracketNewLine: true,
  svelteIndentScriptAndStyle: true,
  trailingComma: 'none'
}
```

### Setup Eslint
```shell
npm add -D eslint eslint-plugin-svelte3
npm add -D @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

Create a file named `.eslintrc.cjs` in the root directory with the following content:
```javascript
module.exports = {
  parser: '@typescript-eslint/parser',
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:@typescript-eslint/recommended-requiring-type-checking'
  ],
  parserOptions: {
    ecmaVersion: 2020,
    sourceType: 'module',
    tsconfigRootDir: __dirname,
    project: ['./tsconfig.json'],
    extraFileExtensions: ['.svelte']
  },
  env: {
    es6: true,
    browser: true
  },
  overrides: [
    {
      files: ['*.svelte'],
      processor: 'svelte3/svelte3'
    }
  ],
  settings: {
    'svelte3/typescript': require('typescript'),
    // ignore style tags in Svelte because of Tailwind CSS
    // See https://github.com/sveltejs/eslint-plugin-svelte3/issues/70
    'svelte3/ignore-styles': () => true
  },
  plugins: ['svelte3', '@typescript-eslint'],
  ignorePatterns: ['node_modules']
}
```

Add the following commands to the scripts section in your package.json.
```
"format": "prettier --write ./src/**/*.{js,svelte,html,ts}",
"lint": "eslint './src/**/*.{js,ts,svelte}'",
"lint:fix": "eslint --fix './src/**/*.{js,ts,svelte}'",
"prelint": "npm run format"
```
