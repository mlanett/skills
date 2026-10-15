---
name: pr-review-loop
description: "Take a change from draft PR to reviewed and ready: self-review with be-code-review, mark ready, then address and resolve the review bot's comments. Use when opening a PR you want driven through review."
---

# PR Review Loop

Drive one pull request from draft to ready for a human. Review it yourself first, so the first round of comments needs no wait, then let the review bot have it and answer what it says.

## 1. Open the PR as a draft

Push the branch and open a **draft** pull request. Write the description with `writing-pull-requests` if it is available.

If a PR for the branch is already open, use it and skip to step 2.

## 2. Self-review with be-code-review

Always run `be-code-review`, including on specs, docs, and config, against the PR's diff (`gh pr diff`, or the diff against the base branch). Run it in a subagent so the review does not share the author's context and assumptions.

For each finding:

- **Violation** — fix it.
- **Judgement call** — fix it if you agree. If not, note why in one line; you will need that reasoning if a bot raises the same point.
- **Hand off to a design review** — do not widen the PR. Mention it in the PR description as an open question for the human reviewer.

Run the repo's own fast checks (lint, format, typecheck, the changed package's tests), commit, and push.

## 3. Mark the PR ready for review

`gh pr ready`, or the GitHub tool's equivalent. This triggers the review bot.

## 4. Wait for the review bot

The review bot runs once, when the PR leaves draft. Wait about **15 minutes** for it, without polling in a tight loop.

- **Claude Code on the web / Projects:** call `subscribe_pr_activity` for the PR and end your turn. The review wakes the session.
- **Local CLI:** wait 15 minutes (or `gh pr checks --watch` until the review check finishes), then read the reviews and comments.

If nothing has arrived after 15 minutes, say so and stop.

## 5. Address the bot's comments

Read every open review thread. Treat each bot finding as a claim to verify, not an order.

- **Real and in proportion** — fix it.
- **Optional, nit, or style** — fix it if it is plainly right and cheap; otherwise leave it.
- **Wrong, or not worth its code** — leave it, and say why.

Run the fast checks again, commit, and push once for the whole batch, not once per comment. The bot does not review again, so this is the only round.

## 6. Reply and resolve

On every bot thread, reply in one line before resolving it:

- what you changed, naming the commit; or
- why you left it as is.

Then resolve the thread.

## Human comments

Humans mostly bring opinions, which the author has to weigh; the bot is better at finding real problems. So on a thread a human opened:

- Reply to explore it: answer questions, explain the reasoning behind the code, lay out the trade-offs, or say what a change would involve.
- Do not change code for it, and do not resolve it. Leave both to the author and the reviewer.
- The one exception is a clear bug: fix that, whoever pointed it out, and say so in the thread.

## Report

End with a short summary: the PR link, what be-code-review and the bot found and what you changed, any thread you left as is and why, and any human thread waiting on the author.
