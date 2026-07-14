# Rules

Files in this directory are injected into or placed in relevant locations depending on the platform — some platforms inline rules into a main config file, others write them as discrete files in a dedicated directory.

Use frontmatter to scope a rule to specific folders or file types:

```markdown
---
description: Only applies inside src/
globs: src/
---

Always prefer named exports.
```
