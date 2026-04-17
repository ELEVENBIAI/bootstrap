# SKILL.md authoring guide

## Frontmatter fields

### Required

```yaml
---
name: my-skill                # kebab-case, matches the directory name
description: |                # what the skill does AND when it triggers
  Description of the skill. Use when the user asks for X.
  Triggers include phrases like "do X", "help with Y".
version: 1.0.0                # semantic versioning
---
```

### Optional

```yaml
requires_secrets:              # required API keys
  - key: API_KEY_NAME
    service: Service name
    url: https://example.com/api-keys
    description: What the key is for
    hint: "Starts with 'sk-', 40 characters"
    instructions: |
      1. Go to example.com
      2. Create an API key
      3. Copy it
    required: true

agent: general-purpose         # Run in subagent (general-purpose|Explore|Plan|Bash)
model: sonnet                  # Subagent model (sonnet|opus|haiku)
context: fork                  # Pass full context to subagent
user-invocable: false          # Prevent invocation via slash command
disable-model-invocation: true # Prevent automatic triggering
```

## Body guidelines

- Use imperative/infinitive ("Create the file" not "You should create the file")
- Keep under 300 lines — move detail to `references/`
- Only include what Claude doesn't already know
- Prefer concise examples over long explanations

## API-key pattern for scripts

Scripts that need API keys should embed a `load_env()`:

```python
import os
from pathlib import Path

def load_env():
    current = Path(__file__).resolve()
    for _ in range(10):
        current = current.parent
        env_path = current / ".env"
        if env_path.exists():
            with open(env_path) as f:
                for line in f:
                    line = line.strip()
                    if line and not line.startswith("#") and "=" in line:
                        key, value = line.split("=", 1)
                        os.environ.setdefault(key.strip(), value.strip().strip('"\''))
            return True
    return False
```

## Progressive disclosure patterns

### Pattern 1: Overview with references

```markdown
## Quickstart
[core workflow]

## Advanced features
- **Feature A**: See [references/feature-a.md](references/feature-a.md)
- **Feature B**: See [references/feature-b.md](references/feature-b.md)
```

### Pattern 2: Domain organization

```
skill/
├── SKILL.md (overview + navigation)
└── references/
    ├── area-a.md
    ├── area-b.md
    └── area-c.md
```

Claude only reads the relevant reference file.

### Pattern 3: Conditional detail

```markdown
## Basic usage
[simple instructions]

**For advanced cases**: See [references/advanced.md](references/advanced.md)
```

## Common pitfalls to avoid

- Description too vague (must include trigger conditions)
- SKILL.md too long (>300 lines — split into references)
- Deeply nested references (max one level deep from SKILL.md)
- Including README.md or other human-readable docs
- Duplicating information between SKILL.md and references
- Name with spaces or uppercase (use kebab-case)
