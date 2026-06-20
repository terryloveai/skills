# skills

  A collection of reusable agent skills for AI-assisted work.

  ## Install a Skill

  ```bash
  # Interactive — pick which skill and which agent from the menu
  npx skills@latest add terryloveai/skills

  # Non-interactive (Claude Code, no prompts)
  npx skills@latest add terryloveai/skills -a claude-code -y

  # Install globally (all projects on this machine)
  npx skills@latest add terryloveai/skills -g -a claude-code -y
  ```

  Common `-a` values: `claude-code`, `replit`, `cursor`, `codex`, `opencode`.

  ## Skill Index

  | Skill | Description |
  |-------|-------------|
  | [pharma-ba-advisor](./pharma-ba-advisor/) | BioPharma BA/PM advisory mode — Grill → Analyze → PRD → Slice（交付切片） |
  