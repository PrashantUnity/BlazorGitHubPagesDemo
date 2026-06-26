# Blazor WASM on GitHub Pages

Sample [Blazor WebAssembly](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) project that deploys to [GitHub Pages](https://pages.github.com/) using GitHub Actions.

**In-app guide:** run locally and open **Publishing Guide** in the nav, or see [Pages/PublishGuide.razor](Pages/PublishGuide.razor).

**Extended walkthrough (with video):** [codefrydev.in — Blazor WASM on GitHub Pages](https://codefrydev.in/Updates/blog/blazor/deploytogithub/)

## Quick start

```bash
dotnet new blazorwasm -o BlazorGitHubPagesDemo
cd BlazorGitHubPagesDemo
dotnet run
```

## Deploy to GitHub Pages

### 1. Add the workflow

Copy [.github/workflows/dotnet.yaml](.github/workflows/dotnet.yaml) into your repo. On each push to `main`, it:

1. Publishes the Blazor app to `release/wwwroot`
2. Sets `<base href="/YourRepoName/">` in `index.html` (required for project Pages URLs)
3. Copies `index.html` to `404.html` (SPA deep-link fallback)
4. Adds `.nojekyll` (so `_framework` and other `_` paths are published)
5. Pushes `release/wwwroot` to the `gh-pages` branch

### 2. Customize the workflow

| Setting | Update in workflow |
|--------|---------------------|
| Default branch | `on.push.branches` |
| .NET SDK | `dotnet-version` (this repo uses `9.0.x`) |
| Project file | `dotnet publish` `.csproj` name |
| Repo name in URL | `sed` step — replace `BlazorGitHubPagesDemo` with your GitHub repository name |

### 3. GitHub settings

1. Push to GitHub.
2. **Settings → Actions → General** — set workflow permissions to **Read and write**.
3. **Settings → Pages** — source: **Deploy from a branch**, branch: **gh-pages**.
4. After the first successful Actions run, visit `https://<username>.github.io/<repository>/`.

### Optional: native WASM libraries

If you use packages that need native WASM builds (e.g. SkiaSharp, SQLite), add before publish:

```yaml
- name: Install .NET WASM Build Tools
  run: dotnet workload install wasm-tools
```

## Local vs deployed base path

- **Local:** `wwwroot/index.html` keeps `<base href="/" />`.
- **CI:** the workflow rewrites the base href to `/YourRepoName/` for GitHub Pages.

Do not commit the production base href unless you only deploy from that path locally.

## Links

- [Host and deploy Blazor WebAssembly (Microsoft)](https://learn.microsoft.com/aspnet/core/blazor/host-and-deploy/webassembly)
- [GitHub Pages docs](https://docs.github.com/en/pages)
