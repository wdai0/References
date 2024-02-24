# Tips with slidev[^1]

[^1]: [slidev](https://github.com/slidevjs/slidev)

Getting started:

* Init a repository

```zsh
npm init slidev@latest
```

* Open and more

```zsh
npx slidev
```

* Export

```zsh
npm i -D playwright-chromium
npx slidev export
npx slidev export --format md
```

* Static hosting

Refer to [GitHub pages hosting](https://sli.dev/guide/hosting).

* Customized files

Use `gmu_styles.css`, `cover.md` for layout and aesthetics. Config `.github/workflow/deploy.yml`.

## Understanding the Workflow Configuration

1. **Environment Section**:

   ```yaml
   environment:
     name: github-pages
     url: ${{ steps.deployment.outputs.page_url }}
   ```

   - `name: github-pages`: This specifies the environment name under which the job runs. GitHub environments can be used for various purposes, including approvals, secrets, and settings specific to the deployment environment.
   - `url: ${{ steps.deployment.outputs.page_url }}`: This dynamically sets the environment's URL to the URL output by the deployment step (`steps.deployment.outputs.page_url`). This is useful for tracking the deployment's URL directly from the GitHub Actions workflow run.

2. **Steps Explained**:
   - `actions/checkout@v4`: Checks out your repository under `$GITHUB_WORKSPACE`, so your workflow can access it.
   - `actions/setup-node@v4`: Sets up a Node.js environment to run your build, specifying that the latest Long-Term Support (LTS) version of Node.js should be used.
   - **Install dependencies**: Runs `npm install` within the `fairness-ml` directory to install the project's dependencies. This is crucial for preparing your project for the build.
   - **Install Slidev theme**: Specifically installs the Slidev theme `slidev-theme-academic`, ensuring that all theme-specific dependencies are present for the build.
   - **Build**: Executes the Slidev build command with the `--base /References/` flag. This flag tells Slidev to adjust the paths in the built output to be relative to the `/References/` subpath, matching the GitHub Pages deployment path. The `working-directory: fairness-ml` part ensures this command is executed in the context of your Slidev project.
   - `actions/configure-pages@v4`: This step configures GitHub Pages with the settings necessary for deployment, preparing the environment based on the workflow's context.
   - `actions/upload-pages-artifact@v3`: Uploads the built site (from `fairness-ml/dist`) as an artifact. This is part of the GitHub Pages deployment process, where the built static files are made ready for deployment.
   - **Deploy**: Uses `actions/deploy-pages@v4` to actually deploy the uploaded artifact to GitHub Pages. The `id: deployment` allows this step to be referenced elsewhere in the workflow, particularly useful for setting the `url` in the environment section.

### Why the `--base` Configuration Matters

The `--base` flag is critical when deploying to a subpath (like `/References/`) on GitHub Pages because it adjusts internal links, script src attributes, and other path-dependent references in your HTML and static files. Without setting this correctly, your site's resources (CSS, JavaScript, images) wouldn't load correctly because the browser would request them from the root path (`/`) instead of the subpath (`/References/`).

This behavior is common in many static site generators and frontend frameworks because they need to know the deployment base URL to correctly prefix resource paths. Since GitHub Pages allows for repository-based hosting at subpaths, specifying the correct base URL aligns the site's resource requests with the actual deployment location.
