# Agents

Files in this directory use YAML frontmatter to describe the agent:

```markdown
---
name: my-agent
description: Does X when asked
model: <model-id>
tools: [Read, Edit, Bash]
---

You are a specialist in ...
```

The sync step translates unsupported frontmatter fields to the closest equivalent for each platform.
