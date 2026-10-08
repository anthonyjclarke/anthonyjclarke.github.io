# anthonyjclarke.github.io

The hub for every CYD project's browser installer:
<https://anthonyjclarke.github.io/>.

It is one static page. `index.html` reads `projects.json`, a list of repo
names, and fetches each project's `/<repo>/index.json`. Each project card
shows the boards, with an ESP Web Tools 10.4.0 install button pointing at
that project's manifests.

---

## Why it holds no firmware

Every project deploys its own GitHub Pages at `anthonyjclarke.github.io/<repo>/`,
which is the same origin as this hub. The buttons therefore point straight at
`/<repo>/manifest-*.json`, with no CORS and no copies. A project release
updates the hub on the next page load, and the hub never needs a rebuild.

---

## Add a project

1. The project releases through
   [cyd-web-installer](https://github.com/anthonyjclarke/cyd-web-installer)'s
   reusable workflow, so `/<repo>/index.json` exists.
2. Add the repo name to `projects.json`, in display order.
3. Commit to `main`. `.github/workflows/pages.yml` checks the list and
   deploys.

A project listed before its first release shows "No installer published
yet" instead of breaking the page.

---

## Try it locally

The page fetches `/<repo>/index.json` from its own origin. To test, put a
project's live `index.json` and manifests under a folder named after the repo,
next to `index.html`. Then serve the folder:

```bash
python3 -m http.server 8000
```

One-time setup: **Settings → Pages → Source** = GitHub Actions.
