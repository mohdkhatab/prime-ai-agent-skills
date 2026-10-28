# Contributing to Prime AI Agent Skills

Thank you for your interest in contributing to **Prime AI Agent Skills**! This repository provides a modular, production-ready skills architecture for modern autonomous AI agents.

## How to Add or Improve a Skill

1. **Fork the Repository**
2. **Create a Feature Branch**
   ```bash
   git checkout -b feature/new-skill-name
   ```
3. **Skill Directory Structure**
   Each skill resides in `skills/<skill-name>/` and must follow this structure:
   ```text
   skills/<skill-name>/
   ├── SKILL.md                 # Primary instruction file with YAML frontmatter
   ├── scripts/                 # (Optional) Execution scripts (Node.js, Python, Shell)
   ├── references/              # (Optional) Detailed guides, specifications, routing docs
   └── templates/               # (Optional) Code templates, starters, or boilerplates
   ```
4. **Frontmatter Standards**
   The `SKILL.md` must begin with YAML frontmatter:
   ```yaml
   ---
   name: your-skill-name
   description: Clear 1-3 sentence summary of when and how an agent should trigger this skill.
   ---
   ```
5. **Configuration Guidelines**
   - Use generic placeholders (`<YOUR_LINK>`, `<YOUR_API_URL>`, `<YOUR_DOMAIN>`) for external endpoints.
   - Do not commit hardcoded credentials or proprietary secrets.
6. **Submit a Pull Request**
   Open a PR against the `main` branch with a clear description of the new capability or bug fix.

---
Maintained with ❤️ by [Mohd khatab](https://github.com/mohdkhatab)
