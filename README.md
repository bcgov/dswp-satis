# DSWP Satis

![License](https://img.shields.io/badge/License-GPLv3-blue.svg)

A public Composer repository for BC Government WordPress plugins and themes, hosted on GitHub Pages and built with GitHub Actions.

## Overview

This repository uses [Satis](https://github.com/composer/satis) to generate a static Composer repository from GitHub packages. The built output is deployed to GitHub Pages and can be consumed by any Composer-enabled project.

## Implementation Guide

### 1. Create the GitHub Repository

1. Go to GitHub and create a new repository under the `bcgov` organization
2. Name it `dswp-satis` (or your preferred name)
3. Make it **public** (required for free GitHub Pages)

### 2. Push the Code

```bash
cd github-satis
git init
git add .
git commit -m "Initial Satis configuration"
git remote add origin git@github.com:bcgov/dswp-satis.git
git push -u origin main
```

### 3. Configure GitHub Pages

1. Go to **Settings** → **Pages**
2. Under **Source**, select **Deploy from a branch**
3. Set **Branch** to `gh-pages` and folder to `/ (root)`
4. Click **Save**

### 4. Create Required Secret

Go to **Settings** → **Secrets and variables** → **Actions** and add:

#### SATIS_PAT (GitHub Personal Access Token)

1. Go to [GitHub Settings → Developer settings → Personal access tokens](https://github.com/settings/personal-access-tokens)
2. Create a new token with **read-only** access to repository contents
3. Add as `SATIS_PAT`

### 5. Trigger the First Build

Push to `main` or go to **Actions** → **Build Satis Repository** → **Run workflow**

### 6. Verify

- Check the **Actions** tab for a successful build
- Verify the static repository is served at `https://bcgov.github.io/dswp-satis/`

## Configuration Files

### satis.json

The main Satis configuration file. Contains:

- **name**: Repository identifier
- **homepage**: GitHub Pages URL
- **archive**: Download configuration (zip format, stored in `dist/`)
- **repositories**: List of BC Government WordPress packages from GitHub
- **require-all**: Includes all versions from listed repositories
- **output-dir**: Build output directory (`docs/`)

### .github/workflows/build.yml

GitHub Actions workflow that:

1. Installs PHP 8.5 and Composer
2. Installs Satis via `composer create-project`
3. Writes authentication secrets to `~/.composer/auth.json`
4. Builds the static repository
5. Deploys to `gh-pages` branch

### auth.json.template

Reference template showing the expected format for Composer authentication. The actual `auth.json` is generated during the CI build from repository secrets.

## Consuming the Repository

Add to your project's `composer.json`:

```json
{
  "repositories": [
    {
      "type": "composer",
      "url": "https://bcgov.github.io/dswp-satis"
    }
  ]
}
```

Then require packages normally:

```bash
composer require bcgov/wordpress-utils
```

## Adding New Packages

To add a new package to the repository:

1. Edit `satis.json`
2. Add a new entry to the `repositories` array:
   ```json
   { "type": "vcs", "url": "https://github.com/bcgov/your-package.git" }
   ```
3. Commit and push to `main`
4. The workflow will automatically rebuild and include the new package

## Troubleshooting

### Build Fails with Authentication Error

- Verify the secret is correctly set: `SATIS_PAT`
- Check that token has not expired

### Packages Not Appearing

- Check the workflow logs for errors
- Verify the repository URL is correct in `satis.json`
- Ensure the package has a valid `composer.json`

### GitHub Pages Not Loading

- Verify Pages is enabled in repository settings
- Check that the `gh-pages` branch exists and contains the built files
- Wait a few minutes for DNS propagation

## References

- [Composer Documentation](https://getcomposer.org/doc/)
- [Satis Documentation](https://getcomposer.org/doc/articles/handling-private-packages-with-satis.md)
- [GitHub Actions](https://docs.github.com/en/actions)
- [GitHub Pages](https://docs.github.com/en/pages)
