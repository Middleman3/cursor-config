---
name: slack
description: Send a message to Slack using the project’s configured notifier (webhook, CLI, or script)
---

# /slack

1. Accept the message from the user (or compose it from context if asked to "notify about X").
2. Format links as Slack mrkdwn: `<url|display text>`.
3. Send using **this project’s** notification mechanism (examples: `mise run notify:slack "<message>"`, a `Makefile` target, or a small script referenced in `package.json`). If none exists, say so and offer to paste the message for manual send.
4. Confirm delivery when the API returns success.
5. **Presets** (when the project supports them):
   - **Deployment** — PR link, version tag, environment, feature-flag state, release link.
   - **Release** — version, changelog snippet, link to toggle flags or continue rollout.
