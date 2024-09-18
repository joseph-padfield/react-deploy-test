# React GitHub Pages deploy

- Create a new repo and clone.
- In root directory - `create vite@latest`
- Checkpackage.json for:
````
{
  "scripts": {
    "build": "vite build",
    "preview": "vite preview"
  }
}
````
- Build the app - `npm run build`
- Test locally - `npm run preview`<br>
(You can change the port in package.json: ``"preview": "vite preview --port 8080"``)
- Set the correct `base` in `vite.config.js`: <br>
	for this example: 
	````
	export default defineConfig({
	  plugins: [react()],
	  base: '/react-deploy-test/',
	})
	````
- In Github Pages configuration, choose soure of deployment as "GitHub Actions" and insert following:
````
# Simple workflow for deploying static content to GitHub Pages
name: Deploy static content to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ['main']

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets the GITHUB_TOKEN permissions to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: 'pages'
  cancel-in-progress: true

jobs:
  # Single deploy job since we're just deploying
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Set up Node
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'
      - name: Install dependencies
        run: npm ci
      - name: Build
        run: npm run build
      - name: Setup Pages
        uses: actions/configure-pages@v4
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          # Upload dist folder
          path: './dist'
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
````
- Create a file called `.gitlab-ci.yml` in the project root. Insert:
````
image: node:16.5.0
pages:
  stage: deploy
  cache:
    key:
      files:
        - package-lock.json
      prefix: npm
    paths:
      - node_modules/
  script:
    - npm install
    - npm run build
    - cp -a dist/. public/
  artifacts:
    paths:
      - public
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
````
## Now when you push your code, your site will deploy.