---
description: Send a message to Slack via Incoming Webhook (when the project has a notify command)
---

# /slack

1. Accept the message content from the user (or compose it from context if asked to "notify about X")
2. Format links as Slack mrkdwn: `<url|display text>`
3. Send via the project's notify command if available (e.g. `mise run notify:slack "<message>"`)
4. Confirm delivery (check response code)
5. Common presets (when the project supports them):
   - "notify deployment" — compose a deployment message with current PR, version tag, flag state, and release link
   - "notify release" — compose a release message with version, changelog entry, and flag toggle instructions
