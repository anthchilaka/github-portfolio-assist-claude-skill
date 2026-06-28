# 🚀 GitHub Portfolio Assist | Claude Skill | Workflow Automation

## 📋 Executive Summary
Manually pushing portfolio projects to GitHub — repo setup, README writing, file commits — eats time that should go toward the analysis itself. This skill automates that workflow end-to-end inside Claude: it checks GitHub MCP connectivity, audits the project folder, generates a README using a proven hiring-manager-tested framework, and pushes only after explicit human review. Built for a working data analyst who ships multiple portfolio pieces and needs the packaging step to stop being a bottleneck.

## 🎯 Business Problem
A strong project means nothing if its README doesn't sell it in the first 10 seconds of a recruiter's scan. Most portfolio repos fail not on analysis quality but on presentation — missing context, buried results, no clear "why should I care." This skill enforces a consistent, recruiter-tested README structure across every project pushed, while keeping a human-in-the-loop checkpoint so nothing goes live unreviewed.

## 🔧 Methodology
Skill-based workflow built on Claude's Skill framework, triggered on push/publish intent. Checks MCP auth state, audits required files, generates README content from a fixed 7-section template (title, exec summary, business problem, methodology, skills, results, next steps), then gates on explicit user confirmation before calling GitHub MCP tools to create/update the repo.

## 💡 Skills Demonstrated
- **Workflow design:** multi-step gated automation, pre-flight connectivity checks, human-in-the-loop confirmation gates
- **Claude Skills framework:** skill authoring, trigger phrasing, structured markdown instruction sets
- **GitHub MCP integration:** repo creation, file commit/push, PAT-based auth setup (Claude Code CLI + Claude Desktop/Docker paths)
- **Documentation strategy:** applying a structured README framework (Jess Ramos 7-tip model) consistently across a portfolio

## 📊 Results & Recommendations
- Standardizes README quality across all pushed repos instead of ad hoc writing per project
- Removes a recurring manual step (repo setup + README drafting) from the portfolio pipeline
- **Recommendation:** extend the skill to also auto-update the GitHub profile README's pinned-repo list when a new project goes live
- **Recommendation:** add a lightweight repo-naming convention check (kebab-case enforcement) before push, to keep the portfolio visually consistent

## 🔭 Next Steps
With more time: add automatic LinkedIn post drafting triggered from the same push event, and a "stale link checker" that periodically verifies all previously pushed repo links still resolve. Current limitation — the skill has no test suite of its own; correctness depends on the operator reviewing the generated README before confirming push.

---

## 🛠️ How to use this skill

This repo doesn't ship a binary `.skill` file — the full instructions are below as plain text so you can copy them straight into your own setup, in either Claude Code or Claude Desktop.

### Step 1 — Create the skill

In Claude Code, create a file at `~/.claude/skills/github-assist/SKILL.md` (Claude Desktop: same idea via the Skills settings, or place it in your Cowork skills folder) and paste in the full instruction set below.

### Step 2 — Connect GitHub MCP

**Path A — Claude Code (terminal)**
1. Generate a GitHub Personal Access Token: GitHub → Settings → Developer Settings → Fine-grained tokens
   - Permissions needed: `Contents` (read/write), `Metadata` (read), `Administration` (read/write — required for repo creation), `Workflows` (read/write)
2. Run:
   ```
   claude mcp add -s user --transport http github https://api.githubcopilot.com/mcp -H "Authorization: Bearer YOUR_PAT_HERE"
   ```
3. Restart Claude Code, then ask: *"What GitHub tools do you have?"*

**Path B — Claude Desktop / Cowork**
1. Install and start [Docker Desktop](https://www.docker.com/products/docker-desktop)
2. Generate a PAT (same scopes as above)
3. Open `%APPDATA%\Claude\claude_desktop_config.json` and add inside `"mcpServers"`:
   ```json
   "github": {
     "command": "docker",
     "args": ["run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN", "ghcr.io/github/github-mcp-server"],
     "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "YOUR_PAT_HERE" }
   }
   ```
4. Fully restart Claude Desktop, then ask: *"What GitHub tools do you have?"*

### Step 3 — Trigger it

Tell Claude something like *"push this project to GitHub"* and the skill takes over — auditing the folder, drafting a README, and waiting for your sign-off before anything goes live.

---

## 📄 Full skill instructions (`SKILL.md`)

```markdown
# GitHub Assist

## Purpose
Push specific projects from local build folders to GitHub, following
the Jess Ramos portfolio framework and a set of personal README standards.
Covers repo creation, README generation, and file push via the GitHub MCP.

**This skill never pushes automatically.** Always confirm before
committing anything.

---

## Step 0 — Is the GitHub MCP Connected?

Before anything else, check whether GitHub MCP tools are available in the
current session (look for tools named `github_*` or similar).

**If connected:** proceed to Step 1.

**If NOT connected:** do not attempt to push. Surface the relevant setup
guide (see "How to use this skill" above), state which setup path applies
to the current environment, and stop. The push can only happen once MCP is live.

---

## Step 1 — Identify the Project

Ask:
- Which folder?
- New repo or update to an existing one?
- Public or Private? (default: Public)

---

## Step 2 — Audit the Project Folder

Confirm the folder contains:
- [ ] Primary deliverable (notebook, .pbix, dashboard PDF, code files)
- [ ] Data file(s) — CSV, Excel, or a noted live link
- [ ] `README.md` — if missing, generate it using Step 3 below

---

## Step 3 — Generate or Review README.md

Follow this structure exactly. Do not invent details — if information is
missing, ask before writing.

### README Structure (Jess Ramos Framework)

**1. Title** — Format: `[Analysis Type] | [Tool] | [Industry]`. Use a
title that hooks the hiring manager who needs exactly that type of analysis.

**2. Executive Summary** — 3–5 sentences: business problem, solution used,
quantified impact, 1–2 next steps.

**3. Business Problem** — What is being solved and why it matters.

**4. Methodology** — Analytical approach. **Hard cap: 500 characters.**

**5. Skills Demonstrated** — List only tools actually used, with specific
sub-skills (e.g. SQL: CTEs, window functions; Power BI: DAX, data modelling).

**6. Results & Business Recommendations** — Key findings + 2–3 concrete,
stakeholder-framed recommendations.

**7. Next Steps** — What you'd do with more time; note data limitations.

---

## Step 4 — Review README

Stop and present the generated README for review.
**Do not commit until explicitly confirmed.**

---

## Step 5 — Push to GitHub

Using the GitHub MCP tools:
1. Create repo if new — kebab-case naming
2. Commit and push all files in the project folder
3. Return the live GitHub URL

---

## Step 6 — Post-Push Checklist

- [ ] Live URL confirmed
- [ ] Test the link in an incognito/private browser window before sharing
  it anywhere — broken links in job applications are a silent killer
- [ ] Add the link to: CV/resume and LinkedIn profile

---

## Portfolio Quality Rules

- No generic datasets (Titanic, Iris, classic course datasets)
- Every project must tie back to a real or realistic business problem
- Preferred format: data science notebook OR clean, documented code files
- The README is the product — treat it as a sales page
- Do not over-engineer: match analytical complexity to the business problem
```

---

## 📜 License
MIT — see [LICENSE](LICENSE). Use it, adapt it, ship your own version.
