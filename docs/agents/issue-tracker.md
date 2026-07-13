# Issue tracker: GitHub

Issues and PRDs for this repository live as GitHub issues in `RafaelGCPP/ARevival`. Use the `gh` CLI for issue operations and infer the repository from the configured Git remote.

## Conventions

- Create an issue with `gh issue create`.
- Read an issue and its discussion with `gh issue view <number> --comments`, including its labels.
- List issues with `gh issue list`, selecting the state and labels needed by the active skill.
- Comment with `gh issue comment <number>`.
- Apply or remove labels with `gh issue edit <number> --add-label` or `--remove-label`.
- Close with `gh issue close <number>`, adding a resolution comment when appropriate.

Use a temporary body file or another safely quoted mechanism for multiline content. Do not place secrets or sensitive information in command arguments, issue bodies, or comments.

## Pull requests as a triage surface

PRs as a request surface: **no**.

External pull requests do not enter the issue triage queue. PR review remains a separate workflow. GitHub shares one number space across issues and pull requests, so resolve ambiguous references before acting.

## Skill operations

When a skill says “publish to the issue tracker”, create a GitHub issue. When it says “fetch the relevant ticket”, read the complete issue body, labels, and comments.

Specs created by `to-spec` receive the `ready-for-agent` label. Tickets created by `to-tickets` use the configured triage vocabulary in `docs/agents/triage-labels.md`.

## Wayfinding operations

The `wayfinder` map is a GitHub issue labelled `wayfinder:map`. Its tickets are child issues whenever GitHub sub-issues are available.

- Map: one issue containing Destination, Notes, Decisions so far, Not yet specified, and Out of scope.
- Child ticket: a sub-issue labelled with one of `wayfinder:research`, `wayfinder:prototype`, `wayfinder:grilling`, or `wayfinder:task`.
- Blocking: use GitHub native issue dependencies when available. The dependency endpoint requires the blocker’s numeric database ID, not its issue number or node ID.
- Fallback: if sub-issues or native dependencies are unavailable, link children from a task list in the map and place `Part of #<map>` and `Blocked by: #<issue>` in ticket bodies.
- Frontier: open children with no open blockers and no assignee, in map order.
- Claim: assign the selected issue to the driving developer before doing work.
- Resolve: post the answer as a comment, close the ticket, then append a short linked context pointer to the map’s Decisions so far.

Do not modify or close a parent issue when a child ticket is published or resolved unless the active skill explicitly requires updating the map body.

