# byronxlg.com

The projects page: every live project from the fleet registry, its 20-second launch video and
its current state. Generated, not hand-edited: `bin/fleet page --push` in `byronxlg/management`
renders `index.html` from `projects.yaml`, `state/status.json` and each project's `brag/`, and
pushes here when something changed (the operator tick runs it). Served by GitHub Pages at
https://byronxlg.com/ (DNS in `byronxlg/pdw` `infra/main.tf`).

To change the page: the template is `templates/site/index.html` and the per-project text is the
`page:` block in `projects.yaml`, both in the management repo.
