# Antigravity Rules for this Project

This workspace is managed and developed using the concepts of **spec-craft** and **spec-kit**.

## General Principles

- **Single Source of Truth**: The project policies, architecture, roadmap, and tasks are managed inside the `obsidian/` directory (e.g., [obsidian/project.md](file:///home/akihiro/文書/develop/git/akuroiwa/akihiro-tech-writing/obsidian/project.md)).
- **Rule of Double Check**: Before starting any writing or code editing task, the AI agent **MUST** read and verify the project policies in the `obsidian/` directory.
- **Spec-Driven Process**: We follow a spec-driven development (SDD) process where specifications are clarified and agreed upon with the USER before implementation.
- **Workflow Execution**:
  - We do not run the heavy `spec-kit` command loops (Specify -> Plan -> Implement) asynchronously to avoid token limits.
  - Instead, the AI agent (Antigravity/agy) reads the strategy from `obsidian/`, coordinates with the USER to define the tasks, and executes edits directly in the repository, treating Obsidian as the strategic roadmap.

## Task Execution Guidelines

1. **Alignment & Discussion**:
   - Before writing or modifying any article, hold an interactive alignment session with the USER to define the scope and refine the task.
   - For software introductions (Zenn Books), construct a "Development Timeline" based on git logs and provided materials (`dev-log/`, `dev-source/`).
2. **Preview & Verification**:
   - Always run the preview command (e.g., `npx qiita preview`, `npx zenn preview`, etc.) to generate previews of the articles.
   - Present the preview output/links to the USER and request their review before making any git commits.
3. **Version Control**:
   - The AI agent is allowed to make git commits locally.
   - **NEVER** git push to the remote repository (`main` branch) without explicit permission from the USER.
