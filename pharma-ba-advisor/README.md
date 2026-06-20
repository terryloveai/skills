# pharma-ba-advisor

  An agent **skill** that puts a coding/AI assistant into **BioPharma BA/PM advisory mode** for designing, scoping, and governing AI applications in drug development — R&D, clinical trials, CMC, supply chain, and commercial operations.

  It is grounded in three pillars:

  - **DDD** — bounded contexts, ubiquitous language, domain events.
  - **Engineering cybernetics** — four operational concepts: 前馈 (Feedforward), 积分控制 (Integral Control), 层级分离 (Hierarchical Decomposition), 自监控 (Self-Monitoring).
  - **AI Harness governance** — input validation, output guardrails, GxP/FDA/NMPA compliance checkpoints, human override paths.

  ## What it does

  The skill drives one delivery arc — **Grill → Analyze → PRD → Slice（交付切片）** — as four switchable modes:

  | Mode | Purpose |
  |------|---------|
  | **Grill** | Stress-test the design one question at a time, walking the dependency tree, each question with a recommended answer. |
  | **Analyze** | Diagnose the design through the four cybernetic lenses, producing concrete, checkable design requirements. |
  | **PRD** | Synthesize the agreed design into a pharma PRD (with a dedicated Compliance & Validation section). |
  | **Slice** | Break the PRD into tracer-bullet vertical slices, each filed as one issue/工作项, with GxP-aware acceptance criteria. |

  It flags **⚠️ COMPLIANCE RISK** when a design crosses a regulatory red line and **⚠️ LAYER VIOLATION** when the AI is allowed to make a human-owned strategic decision.

  ## Files

  ```
  pharma-ba-advisor/
  ├── SKILL.md                                   # entry point: the four modes + completion criteria
  └── references/
      ├── grilling-pharma.md                     # the design-tree question order + recommended defaults
      ├── cybernetic-four-concepts-pharma.md     # the four-concept framework with pharma examples
      ├── ba-deliverables-pharma.md              # PRD template + 交付切片/issue template
      ├── pharma-domain-map.md                   # bounded-context map across the pharma value chain
      └── gxp-compliance-quick-ref.md            # GxP, 21 CFR Part 11, CSV/IQ/OQ/PQ quick reference
  ```

  ## Installation

  This is a folder-based agent skill. Install it by placing the `pharma-ba-advisor/` directory into your agent's skills folder, then restart/reload the agent so it picks up the new skill.

  - **Replit Agent** → `.agents/skills/pharma-ba-advisor/`
  - **Claude Code** → `.claude/skills/pharma-ba-advisor/`

  ### Install from this repo

  ```bash
  git clone https://github.com/terryloveai/skills.git
  cp -r skills/pharma-ba-advisor <your-project>/.agents/skills/
  ```

  Or add it as a git submodule / sparse checkout if you track skills centrally.

  ## Usage

  Once installed, the agent loads the skill automatically when you describe pharma/biotech AI work, e.g.:

  - "帮我评估一个用于 CMC 批次放行的 AI 系统" → **Analyze** mode
  - "Grill me on this clinical-trial protocol design assistant" → **Grill** mode
  - "把这个药物警戒 AI 写成 PRD" → **PRD** mode
  - "把这份 PRD 拆成交付切片" → **Slice** mode

  The skill responds in your language (Chinese or English) and keeps established regulatory terms in English (GMP, ICH Q10, ALCOA+, 21 CFR Part 11).

  ## License

  MIT — see `LICENSE`. Adapt freely for your own pharma BA/PM workflows.
  