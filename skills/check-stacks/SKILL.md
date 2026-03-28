---
name: check-stacks
description: List active CloudFormation stacks and check health
---

# /check-stacks

1. List stacks: `aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --region us-east-1`
2. Check for stuck/failed states (DELETE_IN_PROGRESS, ROLLBACK_COMPLETE, etc.)
3. Report the project's nested stack tree health
