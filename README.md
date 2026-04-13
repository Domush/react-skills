# React Expert Skills Collection

> Agent-optimized development frameworks for modern web and application engineering.

This repository is a collection of high-performance, specialized **Agent Skills** designed for LLMs following the [agentskills.io](https://agentskills.io) specification. These skills provide structured, referenceable knowledge to enable agents to perform expert-level development, planning, and documentation tasks.

## 🚀 Core Skills

The following skill modules are available in the [skills/](skills) directory:

| Skill | Purpose | Details |
| --- | --- | --- |
| [**development-loop**](skills/development-loop/SKILL.md) | A 6-phase problem-solving matrix for analysis, design, and execution. | Use when planning a new feature, refactoring existing code, debugging a complex issue, or executing any multi-step development task — covers structured problem analysis, multi-option solution design, implementation, validation loops, optimization, and documentation |
| [**react**](skills/react/SKILL.md) | Engineering reference for React 19+ and Tailwind CSS v4+ development. | Use when writing React 19+ components, hooks, or pages, or when writing Tailwind CSS v4+ styles, theming, or utility classes — covers current best practices, deprecated patterns, and migration from older versions |
| [**electron**](skills/electron/SKILL.md) | Modern Electron (41+) configuration, IPC security, and build standards. | Use when configuring, building, or troubleshooting Electron 41+, electron-vite 5+, or electron-builder 26+ projects — covers breaking changes, deprecated patterns, IPC security, build configuration, and proper project structure |
| [**markdown**](skills/markdown/SKILL.md) | Clean, lint-compliant authoring standards for GitHub Flavored Markdown. | Use when authoring, editing, or reviewing Markdown files — covers proper syntax, lint rules, formatting best practices, tables, code blocks, and deprecated patterns to avoid |
| [**writing-skills**](skills/writing-skills/SKILL.md) | The "meta-skill" for authoring, testing, and deploying new agent skills. | Use when creating new skills, editing existing skills, or verifying skills work before deployment |

## 🛠️ Usage

### For Agents

When interacting with this repository, agents should:

Use `Method A` to fetch the skills and `Method B` to update the skills. Ensure that skills are checked out to the correct directory for the AI coding app being used. Since not all skills are relevant to every project, agents should be selective in which skills they install. **Always** ask the user which skills they want to install and adjust the git commands to selectively install them.

### For Humans

These skills serve as the codebase's "Standard Operating Procedures." They ensure that every AI agent joining the project follows the same high-quality patterns, reducing technical debt and improving consistency.

**AI-driven install:** Paste this into your AI agent's chat window:

```text
Install the skills from the repository https://github.com/domush/react-skills
```

**Commandline install:** Execute the following command in your terminal:

This command uses the `skills` CLI tool to fetch and install only the selected skills you want directly into your project:

```bash
npx skills add domush/react-skills
```

## 🧠 Design Philosophy

These skills are built using **TDD for Process Documentation**:

- **Test-Driven**: Every skill is tested against baseline agent behavior before deployment.
- **Context-Efficient**: Designed for minimal token usage while maintaining maximum instructional clarity.
- **Atomic**: Focused on single specialized domains to prevent context pollution.

## 🔌 AI Coding App Integration

To use these skills in your project, you must place them in a directory that your AI coding assistant recognizes. We recommend the `*/skills/` naming convention for all setups.

### App-Specific Mapping

| AI Assistant | Skills Directory |
| :--- | :--- |
| **Antigravity** | `.agents/skills/` |
| **Trae** | `.trae/skills/` |
| **OpenCode** | `.opencode/skills/` |
| **Cline** | `.clinerules/skills/` |
| **Cursor** | `.cursor/skills/` |
| **Windsurf** | `.windsurf/skills/` |
| **Claude Code** | `.claude/skills/` |
| **GitHub Copilot** | `.github/skills/` |

> [!TIP]
> **Universal Fallback**: If your AI app is not listed or if you are unsure, use the **`.agent/skills/`** directory. Most modern agents are pre-configured to look for skill definitions in this path.

### 📥 Fetching with Git

Choose the method that best fits your project's workflow:

#### Method A: Git Subtree (Recommended)

Best for maintaining an independent copy of the skills that you can easily update from the upstream repository.

```bash
git subtree add --prefix .agent/skills https://github.com/domush/react-skills main --squash
```

#### Method B: Git Submodule

Best if you want to keep a clean reference to this repository as a dependency.

```bash
git submodule add https://github.com/domush/react-skills .agent/skills
```

#### Method C: Sparse Checkout

Best if you only want the `skills/` directory without the rest of the repository's metadata.

```bash
git clone --filter=blob:none --sparse https://github.com/domush/react-skills .agent/skills
cd .agent/skills
git sparse-checkout set skills
```

### 🧩 VS Code Plugin (AgentSkillsManager)

For the easiest installation and management, use the **AgentSkillsManager** extension in VS Code or compatible editors (Cursor, Trae, etc.):

1. Install **AgentSkillsManager** from the marketplace.
2. Open the **Agent Skills** panel in the Activity Bar.
3. Add this repository URL as a **Source**.
4. Use the interface to **install/uninstall** individual skills directly into your project's `.agent/skills/` folder.

## 📝 Bonus: Make your own skills

To make your own skills, simply tell you AI agent to create a new skill using the [**Writing Skills**](skills/writing-skills/SKILL.md) skill and it'll do all of the heavy lifting for you.

---

*Built with ❤️ for AI-native development workflows.*
