# React Expert Skills Collection

> Agent-optimized development frameworks for modern web and application engineering.

This repository is a collection of high-performance, specialized **Agent Skills** designed for LLMs following the [agentskills.io](https://agentskills.io) specification. These skills provide structured, referenceable knowledge to enable agents to perform expert-level development, planning, and documentation tasks.

## 🚀 Core Skills

The following skill modules are available in the [skills/](skills) directory:

| Skill | Purpose |
| --- | --- |
| [**development-loop**](skills/development-loop/SKILL.md) | A 6-phase problem-solving matrix for analysis, design, and execution. |
| [**react**](skills/react/SKILL.md) | Engineering reference for React 19+ and Tailwind CSS v4+ development. |
| [**electron**](skills/electron/SKILL.md) | Modern Electron (41+) configuration, IPC security, and build standards. |
| [**markdown**](skills/markdown/SKILL.md) | Clean, lint-compliant authoring standards for GitHub Flavored Markdown. |
| [**writing-skills**](skills/writing-skills/SKILL.md) | The "meta-skill" for authoring, testing, and deploying new agent skills. |

## 🛠️ Usage

### For Agents

When interacting with this repository, agents should:

1. **Identify Triggers**: Match the task against the `description` fields in each `SKILL.md`.
2. **Load Reference**: Use `view_file` to read the relevant `SKILL.md` content.
3. **Follow Standards**: Adhere strictly to the methodologies and patterns defined in the skill (e.g., the TDD cycle for documentation or the 6-phase development loop).

### For Humans

These skills serve as the codebase's "Standard Operating Procedures." They ensure that every AI agent joining the project follows the same high-quality patterns, reducing technical debt and improving consistency.

## 🧠 Design Philosophy

These skills are built using **TDD for Process Documentation**:

- **Test-Driven**: Every skill is tested against baseline agent behavior before deployment.
- **Context-Efficient**: Designed for minimal token usage while maintaining maximum instructional clarity.
- **Atomic**: Focused on single specialized domains to prevent context pollution.

## 🔌 AI Coding App Integration

To use these skills in your project, you must place them in a directory that your AI coding assistant recognizes. We recommend the `*/skills/` naming convention for all setups.

### App-Specific Mapping

| AI Assistant | Skills Directory | Notes |
| :--- | :--- | :--- |
| **Antigravity** | `.agents/skills/` | Cross-tool standard compatibility. |
| **Trae** | `.trae/skills/` | Project-level skills injection. |
| **OpenCode** | `.opencode/skills/` | Supports specific skill sets. |
| **Cline** | `.clinerules/` | Cline requires this path; name files as skills. |
| **Cursor** | `.cursor/skills/` | Can also use `.cursorrules` in root. |
| **Windsurf** | `.windsurf/skills/` | Can also use `.windsurfrules` in root. |
| **Claude Code** | `.claude/skills/` | Replaces top-level `CLAUDE.md`. |
| **GitHub Copilot** | `.github/skills/` | Replaces `.github/copilot-instructions.md`. |

> [!TIP]
> **Universal Fallback**: If your AI app is not listed or if you are unsure, use the **`.agent/skills/`** directory. Most modern agents are pre-configured to look for skill definitions at this path.

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
