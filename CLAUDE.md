# CLAUDE.md — Publish these charts as a public GitHub Pages site

This directory is a **static, single-repo site** of interactive Plotly charts (no build
step). The root `index.html` shows all three charts; each chart is **also** published in
its own subfolder so it can be embedded on its own:

```
index.html        → landing page (all three charts)
burden/index.html → energy burden by income quintile
demand/index.html → U.S. electricity demand forecast
scatter/index.html→ state demand vs. real price change
```

When the user asks you to publish it, push it to a **new public GitHub repo** and enable
**GitHub Pages**, then hand back the live URLs and `<iframe>` snippets.

- **Intended repo name:** `freedom-act-charts`
- **Each subfolder becomes its own embeddable URL** (that's how iframes work — they point
  at a URL/path, not a repo, so one repo serves several embeddable charts).

## Step 0 — Publish safety (required; do this first)
This will be **public**. Run this from the directory and confirm it prints `clean`:
```bash
grep -rniE 'FREOPP|/Users/|ALI[_ -]?Books|api[_-]?key|secret|token' . --exclude-dir=.git --exclude=CLAUDE.md && echo "FOUND — STOP" || echo "clean"
```
If anything matches, stop and tell the user. Do not push.

## Step 1 — Auth
```bash
gh auth status            # if not logged in, tell the user to run: gh auth login
OWNER=$(gh api user --jq .login)
```

## Step 2 — Commit
```bash
git init -b main
git add -A
git commit -m "Interactive energy affordability charts"
```

## Step 3 — Create the public repo and push
```bash
gh repo create freedom-act-charts --public --source=. --remote=origin --push \
  --description "Interactive energy affordability charts"
```

## Step 4 — Enable GitHub Pages (build from main, root)
```bash
echo '{"source":{"branch":"main","path":"/"}}' \
  | gh api --method POST "/repos/$OWNER/freedom-act-charts/pages" --input -
```
If this errors with "already exists," ignore it. If it fails for another reason, tell the
user to enable Pages manually: **repo Settings → Pages → Build and deployment → Deploy
from a branch → `main` → `/ (root)` → Save.**

## Step 5 — Verify (Pages can take 1–2 min on first deploy)
```bash
sleep 75
for p in "" burden/ demand/ scatter/; do
  printf "%-10s " "/$p"
  curl -s -o /dev/null -w "%{http_code}\n" "https://$OWNER.github.io/freedom-act-charts/$p"
done
```
`200` on each = live. A `404` right after enabling usually just means the build hasn't
finished — wait and re-check.

## Step 6 — Report back to the user
Give them the landing URL plus the three embed snippets:

- **Landing page:** `https://<OWNER>.github.io/freedom-act-charts/`
- **Embed snippets** (replace `<OWNER>`):
  ```html
  <!-- Energy burden by income -->
  <iframe src="https://<OWNER>.github.io/freedom-act-charts/burden/"
          width="100%" height="440" style="border:0;" loading="lazy"
          title="Energy burden by income"></iframe>

  <!-- U.S. electricity demand forecast -->
  <iframe src="https://<OWNER>.github.io/freedom-act-charts/demand/"
          width="100%" height="420" style="border:0;" loading="lazy"
          title="U.S. electricity demand forecast"></iframe>

  <!-- State demand vs. real price -->
  <iframe src="https://<OWNER>.github.io/freedom-act-charts/scatter/"
          width="100%" height="600" style="border:0;" loading="lazy"
          title="State demand vs. real price change"></iframe>
  ```

## Do not
- Do not change the charts' data or text in any `index.html` unless the user asks.
- Do not make the repo private — Pages on private repos requires a paid plan.
- The local folder path is irrelevant to the public repo (only the files here are pushed).
- Note: the root `index.html` inlines all three charts directly, so its copy of the chart
  data is separate from the subfolder pages. If the user changes a chart's data, update it
  in both the subfolder page **and** the root landing page.
