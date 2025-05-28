# MAWEP Module

Multi-Agent Workflow Execution Process (MAWEP) - A complete framework for orchestrating parallel AI agent development.

## All MAWEP Documentation

### Core Documentation
- @README.md - Module overview and navigation
- @MAWEP-QUICKSTART.md - Quick start guide
- @mawep-overview.md - What MAWEP actually is (Task tool based)
- @mawep-practical-patterns.md - Universal implementation patterns

### Framework Documentation
- @framework/coordination-branch-pattern.md - Shared work coordination
- @framework/mawep-diagrams.md - Visual architecture diagrams
- @framework/mawep-prompt-engineering.md - Prompt design patterns
- @framework/mawep-scenario-walkthrough.md - Example workflows

### Role Prompts
- @framework/prompts/README.md - Overview of all roles
- @framework/prompts/orchestrator-prompt.md - Orchestrator instructions
- @framework/prompts/agent-prompt.md - Development agent instructions
- @framework/prompts/architect-reviewer-prompt.md - Holistic PR review
- @framework/prompts/technical-reviewer-prompt.md - Code quality review
- @framework/prompts/post-mortem-analyst-prompt.md - Workflow analysis
- @framework/prompts/example-usage.md - Step-by-step execution guide

## Key Concepts

1. **NO BACKGROUND WORK** - Task tool = Single message only, agents FREEZE after responding
2. **Continuous Invocation Required** - Orchestrator must invoke every 30-60 seconds or NOTHING happens
3. **Agents are Stateless** - They remember nothing between invocations
4. **Pods** - Persistent git worktrees where agents work sequentially on issues
5. **Memory Bank** - Persistent context in pods
6. **Fail Fast** - Report blockers immediately
7. **Single Orchestrator** - One instance that MUST keep invoking

## Terminology Clarity

- **Agent**: Ephemeral Task tool execution (like a thread fulfilling a specific request)
- **Pod**: Persistent git worktree where multiple agents work over time on an issue
- **Orchestrator**: Manages pods and assigns agents to them

## ⚠️ Common Misconception to Avoid ⚠️

**WRONG**: "I spawned Agent-1 to work on issue #101" (implies ongoing work)
**RIGHT**: "I sent a message to Pod-1 about issue #101. An agent responded and is now frozen until I invoke again."

The Task tool is NOT like starting a background process. It's like sending an email and getting a reply. After the reply, NOTHING happens until you send another email.

## Usage

When implementing MAWEP in a project:
1. Start with the MAWEP Quick Start guide
2. Read the example usage for detailed walkthrough
3. Use the orchestrator prompt as your main guide
4. Reference other role prompts as needed

## Remember

- All references above are complete - no need to navigate further
- This is the single source of truth for MAWEP framework documentation
- Project-specific implementations may have local `docs/framework/` directories