# Obsidian documentation template for skills

Every skill documentation MUST include the following sections:

## Required sections

### 1. Frontmatter (YAML)
- tags: claude-skill
- skill: name
- version: current version
- updated: date
- status: active / in-development / deprecated

### 2. Overview
2–3 sentences: what does the skill do, what problem does it solve, who is it for?

### 3. Feature set
Bullet points listing every capability. What can the skill actually do?

### 4. Structure & files
Tree view of the skill directory with an explanation of EVERY file:
- What does this file do?
- When is it loaded/executed?

### 5. Prerequisites
What must be installed/configured for the skill to work?
(Python version, API keys, tools, etc.)

### 6. Usage
Concrete examples of how to trigger the skill:
- Which phrases/commands trigger it?
- What happens step by step afterwards?
- At least 2 usage examples with expected output

### 7. Configuration (if applicable)
What parameters/options are available? How can the behavior be tuned?

### 8. Installation
Exact command to install from the GitHub repo.

### 9. Version history
Chronological list of all versions with date and change description.
Newest version first.

## Quality criteria

- Every section must be filled (or explicitly marked as "N/A")
- At least 2 concrete usage examples
- Technical details must be accurate and complete
- Understandable for non-developers
- In English
