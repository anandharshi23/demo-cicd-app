# CI/CD Demo App (UI5/HTML5 → Cloud Foundry via GitHub Actions)

This is a minimal static HTML5 app set up to deploy automatically to
SAP BTP Cloud Foundry whenever you push to the `main` branch.

## Folder structure

```
demo-cicd-app/
├── .github/
│   └── workflows/
│       └── deploy.yml      <- GitHub Actions pipeline (auto cf push)
├── webapp/
│   ├── index.html          <- the demo page
│   └── Staticfile          <- empty file, tells CF to use static buildpack
└── manifest.yml            <- CF app config (name, memory, buildpack, path)
```

## One-time setup steps

1. **Create a GitHub repo** (e.g. `demo-cicd-app`) and push this whole folder to it:
   ```bash
   cd demo-cicd-app
   git init
   git add .
   git commit -m "initial commit"
   git branch -M main
   git remote add origin https://github.com/<your-username>/demo-cicd-app.git
   git push -u origin main
   ```

2. **Test manual deploy first** (do this before relying on the pipeline):
   ```bash
   cf login -a <YOUR_CF_API_ENDPOINT> -u <username> -p <password> -o <org> -s <space>
   cf push
   ```
   Confirm the app comes up at the URL CF gives you. Run `cf delete demo-cicd-app -r`
   afterwards if you want the pipeline to do the first real deploy itself —
   optional, just for a clean demo.

3. **Add GitHub Secrets** (repo → Settings → Secrets and variables → Actions):
   - `CF_API`
   - `CF_USERNAME`
   - `CF_PASSWORD`
   - `CF_ORG`
   - `CF_SPACE`

4. **Push a change** to trigger the pipeline:
   ```bash
   # edit webapp/index.html, e.g. change "Build marker: v1" to "v2"
   git add .
   git commit -m "update demo text"
   git push
   ```

5. Go to the GitHub repo's **Actions** tab — watch the workflow run live:
   checkout → install CF CLI → cf login → cf push.

6. Once it finishes, open your app's CF route (shown in `cf apps` or in the
   Actions log) to see the change live — no manual deploy was run.

## Demo script idea

1. Show `webapp/index.html` open in an editor.
2. Change the "Build marker" text or a color.
3. `git push`.
4. Switch to GitHub Actions tab — show the pipeline running.
5. Refresh the live app URL — show the updated content.

That's the whole "manual → automatic" contrast in one minute.
