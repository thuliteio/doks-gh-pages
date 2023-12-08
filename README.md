# Deploy Doks to GitHub Pages

Example repo showing how to publish your Doks site to GitHub Pages by using [GitHub Actions](https://github.com/features/actions) to automatically build and deploy your site.

Follow the instructions below to deploy your Doks (or Hyas) site to GitHub pages.

1. Create a new file in your project at `.github/workflows/deploy.yml` and paste in the YAML below.

    ```yaml title="deploy.yml"
    # Deploy your Hyas site to GitHub Pages

    name: GitHub Pages

    on:
      push:
        branches:
          - main

    jobs:
      deploy:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout
            uses: actions/checkout@v4
          - name: Setup Node.js
            uses: actions/setup-node@v4
            with:
              node-version: '18'
              cache: 'npm'
          - name: Install dependencies
            run: npm ci
          - name: Check for linting errors
            run: npm run lint
          - name: Build production website
            run: npm run build
          - name: Deploy to GitHub Pages
            uses: peaceiris/actions-gh-pages@v3
            with:
              github_token: ${{ secrets.GITHUB_TOKEN }}
              publish_dir: ./public
    ```

2. On GitHub, go to your repository's **Settings** tab and find the **Pages** section of the settings.

3. Choose **GitHub Actions** as the **Source** of your site.

4. Commit the new workflow file and push it to GitHub.

5. Copy the **Your site is published at** URL and paste it as `baseurl` in `./config/production/hugo.toml`.

6. Set `canonifyURLs = true` in `./config/production/hugo.toml`.

7. Push the changes to GitHub and wait for the action to finish succesfully (after approximately 30 seconds).

That's it. After a minute or so, you site is avaliable at the **Your site is published at** URL. When you push changes to your Hyas project's repository, the GitHub Action will automatically deploy them for you.
