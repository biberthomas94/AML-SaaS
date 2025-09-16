# Browser-only bulk creation of GitHub Issues

This kit lets you create **all issues at once** using **GitHub Actions** from the web UI (no local installs).

## What’s inside
- `.github/workflows/bulk-issues.yml` — runs the bulk creation action
- `.github/workflows/add-to-project.yml` — auto-adds issues with label `backlog` to your chosen Project
- `config/data.csv` — your issues (title/labels/description)
- `config/template.md.mustache` — how the issue body is rendered

## One-time setup (all in the browser)
1. Create (or open) your repo on GitHub.
2. Click **Add file → Upload files** and drag the four files above into the repo (preserve folders).
3. Go to **Settings → Secrets and variables → Actions → New repository secret** and add:
   - `PERSONAL_ACCESS_TOKEN` — a PAT with `repo` scope (fine-grained: Issues write; Projects write).
   - `ADD_TO_PROJECT_PAT` — a PAT with `repo` + `projects` (used by the add-to-project workflow).
   - `PROJECT_URL` — **not** a token; put the URL of your Project board (e.g. `https://github.com/orgs/YourOrg/projects/1`).
4. Open `config/data.csv` in GitHub’s web editor and replace `<your-username-or-org>/<your-repo>` with your actual repo path.

## Run it (still in the browser)
1. Go to the **Actions** tab → **Bulk issue creator (browser)** → **Run workflow** → keep `write=false` to preview.
2. Open the latest run → check the preview output.
3. Re-run the workflow with `write=true` to actually create all issues.
4. The **Auto-add issues to project** workflow will attach new issues (with the `backlog` label) to your project.

Tips:
- You can edit `config/data.csv` in the browser to add/remove items or tweak labels before running.
- If you don’t have a Project yet, create one via the **Projects** tab first.

