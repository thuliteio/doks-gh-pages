# Deploy Doks to GitHub Pages

Example repo showing how to publish your Doks site to GitHub Pages by using [GitHub Actions](https://github.com/features/actions) to automatically build and deploy your site.

Follow the instructions below to deploy your Doks (or Hyas) site to GitHub pages.

1. Create a new file in your project at `.github/workflows/deploy.yml` and paste in the YAML below.

    ```yaml title="deploy.yml"
    # Deploy your Hyas site to GitHub Pages
    name: GitHub Pages

    on:
      # Runs on pushes targeting the default branch
      push:
        branches:
          - main

      # Allows you to run this workflow manually from the Actions tab
      workflow_dispatch:

    # Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
    permissions:
      contents: read
      pages: write
      id-token: write

    # Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
    # However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
    concurrency:
      group: "pages"
      cancel-in-progress: false

    # Default to bash
    defaults:
      run:
        shell: bash

    jobs:
      # Build job
      build:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout
            uses: actions/checkout@v4
          - name: Setup Node.js
            uses: actions/setup-node@v4
            with:
              node-version: '18'
              cache: 'npm'
          - name: Setup Pages
            id: pages
            uses: actions/configure-pages@v4
          - name: Install dependencies
            run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
          - name: Build production website
            env:
              # For maximum backward compatibility with Hugo modules
              HUGO_ENVIRONMENT: production
              HUGO_ENV: production
            run: |
              npm run build \
                -- \
                --gc \
                --minify \
                --baseURL "${{ steps.pages.outputs.base_url }}/" 
          - name: Upload artifact
            uses: actions/upload-pages-artifact@v2
            with:
              path: ./public

      # Deployment job
      deploy:
        environment:
          name: github-pages
          url: ${{ steps.deployment.outputs.page_url }}
        runs-on: ubuntu-latest
        needs: build
        steps:
          - name: Deploy to GitHub Pages
            id: deployment
            uses: actions/deploy-pages@v3
    ```

2. On GitHub, go to your repository's **Settings** tab and find the **Pages** section of the settings.

3. Choose **GitHub Actions** as the **Source** of your site.

4. Commit the new workflow file and push it to GitHub.

5. Copy the **Your site is published at** URL and paste it as `baseurl` in `./config/production/hugo.toml`.

6. Set `canonifyURLs = true` in `./config/production/hugo.toml`.

7. Push the changes to GitHub and wait for the action to finish succesfully (after approximately 30 seconds).

That's it. After a minute or so, you site is avaliable at the **Your site is published at** URL. When you push changes to your Hyas project's repository, the GitHub Action will automatically deploy them for you.
