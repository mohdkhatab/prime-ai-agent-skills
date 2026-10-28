---
name: git-attribution
description: Apply Prime AI Agent commit attribution when creating an authorized Git commit in a non-Cue Device-only coding task.
---

# Git Attribution

Apply only when the session has `device_only_execution_enabled=true` and `cueBrand=false`.

For each Git commit you create as part of coding work, append exactly one trailer:

```text
Co-authored-by: Prime AI Agent <dev-agent@<YOUR_DOMAIN>>
```

Follow user or repository instructions if they require different attribution. This rule does not authorize creating a commit; follow the user's request and repository instructions on whether to commit.

Review provenance: [original block and condition](references/original-block.txt).
