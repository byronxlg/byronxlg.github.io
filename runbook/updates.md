---
project: projects-page
reviewed: 2026-09-23
deploy_path: generated-and-pushed-by-management
rollback_minutes: 20
---

# Updates

There is no change to make in this repo. `bin/fleet page --push` in `byronxlg/management`
renders the page and commits it here as `fleet page: registry or state changed`, every tick;
GitHub Pages rebuilds from `main` in about 30 seconds. A hand edit here lasts until the next
tick and is then overwritten, so every change is made in management and published by the tick.

## How a change reaches production

| Change to | Pipeline | Trigger | Lands in prod when | Evidence |
| --- | --- | --- | --- | --- |
| a project's blurb, site or video (`projects.yaml` `page:`) | `bin/fleet page --push` | merged in management | the next tick, within 20 min | the card on https://byronxlg.com/ |
| a project going live or being retired (`status:`) | same | merged in management | the next tick | the card appears or goes |
| up/down state (`state/status.json`) | same | the tick's health run | the next tick | the state line on the card |
| layout or styling (`templates/site/index.html`) | same | merged in management | the next tick | the page |
| a launch video (`brag/brag.mp4` in a project) | same, read off that checkout's upstream ref | merged in that project | the next tick | the card plays the new video |
| this runbook, `CLAUDE.md` | pushed here by hand | push to `main` | on GitHub immediately | the file |

## Post-deploy smoke test

```sh
curl -s -o /tmp/page.html -w '%{http_code}\n' https://byronxlg.com/
git -C ~/repos/byronxlg.github.io show HEAD:index.html | cmp -s - /tmp/page.html && echo "live == committed"
```

Pass: 200 and `live == committed`. This is the same probe `bin/fleet health` runs every tick
for the `projects-page` project.

## Rollback

The page is not a source of truth, so a rollback is a fix in management plus a tick. Revert the
management commit that changed the registry or the template, then `bin/fleet page --push` (or
wait for the tick). Reverting a commit in *this* repo publishes an older page for at most 20
minutes, until the next render overwrites it: only worth doing if the live page is actively
broken and the fix in management is not immediate.

## Scheduled maintenance

| What | Cadence | How | Validated by |
| --- | --- | --- | --- |
| Pages HTTPS certificate | renews itself; checked at the weekly fleet review | `gh api repos/byronxlg/byronxlg.github.io/pages -q .https_certificate` | `state: approved`, `expires_at` in the future |
| Apex A records against GitHub's published Pages addresses | yearly, or when GitHub announces a change | PR in `byronxlg/pdw` `infra/main.tf`, apply workflow | `dig +short byronxlg.com A` |
| The `page:` blocks (blurbs going stale) | at the weekly fleet review | edit `projects.yaml` in management | the cards read true |

## Things that are risky to change

- The `CNAME` file: it is rewritten by every render from `PAGE_DOMAIN` in `bin/fleet`. Removing
  it there unsets the custom domain and the certificate has to be re-approved.
- `.nojekyll`: without it Pages runs Jekyll over the generated files.
- Anything hand-committed at the repo root: `_push_if_changed` commits with `git add -A`, so a
  stray file here is published, and `index.html`, `CNAME` and `assets/**` are overwritten.
