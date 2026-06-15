# trilocore-docs

Documentation site for Trilocore — how to use **BEVM** (security workbench) and
**Janus** (AI analyst). Served at **doc.trilocore.com**.

Static HTML, no build step. Pages:
- `index.html` — overview + getting started
- `bevm.html` — BEVM workbench guide (panel by panel)
- `janus.html` — Janus AI analyst guide
- `assets/docs.css` — shared dark theme (matches trilocore.com)

## Hosting (GitHub Pages)
`CNAME` is set to `doc.trilocore.com`. Enable Pages on this repo (deploy from
`main`, root), then add a DNS **CNAME** record `doc → kronotitans-ltd.github.io`
(or an A/ALIAS per GitHub Pages docs). Until DNS is pointed, the site is
reachable at the repo's `github.io` URL.

## Edit
Plain HTML/CSS — edit and push; Pages redeploys automatically.
