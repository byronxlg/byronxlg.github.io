# byronxlg.github.io

The public projects page at https://byronxlg.com/. **Generated, never hand-edited.**
`bin/fleet page --push` in [`byronxlg/management`](https://github.com/byronxlg/management)
renders `index.html`, `CNAME` and `assets/<project>/` from `projects.yaml`,
`state/status.json` and each project's `brag/`, and pushes here every operator tick.
An edit made here is gone at the next tick.

To change the page, change it in management:

| What you want to change | Where it lives (management) |
| --- | --- |
| Layout, styling, the header | `templates/site/index.html` |
| A project's blurb, site link, video | its `page:` block in `projects.yaml` |
| Which projects appear | `status: live` in `projects.yaml` (`page.listed: false` opts out) |
| The renderer | `bin/fleet` `cmd_page` |

Operating this repo (who watches it, how a change reaches the live site, how to roll back):
[runbook/](runbook/). It is a tier 3 project in the fleet registry under the name
`projects-page`.
