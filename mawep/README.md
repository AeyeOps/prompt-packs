# MAWEP Framework Module

Multi-Agent Workflow Execution Process (MAWEP) - A complete framework for orchestrating parallel AI agent development.

## Quick Start

When you see "MAWEP" in a project or are spawned as a MAWEP agent:

1. **Identify Your Role** from the Task invocation:
   - Contains "orchestrat" → You're the orchestrator
   - Contains "agent" + issue → You're a development agent  
   - Contains "review" → You're a reviewer

2. **Find Your Instructions**:
   - Look for `docs/framework/prompts/[role]-prompt.md` in the project
   - Check `mawep-workspace/` for assignments and state

3. **Remember Critical Concepts**:
   - 🔴 **NO BACKGROUND WORK** - You only run when invoked
   - 🔄 **CONTINUOUS INVOCATION** - Orchestrator must invoke every 30-60 seconds
   - 💾 **SAVE STATE OFTEN** - Use memory-bank, you could restart anytime
   - ⚡ **FAIL FAST** - Report blockers immediately

## What is MAWEP?

MAWEP enables 2-10 AI agents to work on related tasks in parallel while maintaining quality and consistency. It provides:

- **Orchestration** - Single control point managing all agents
- **Isolation** - Agents work in separate git worktrees
- **Coordination** - Structured communication protocols
- **State Management** - Persistent context across invocations
- **Quality Gates** - Built-in review processes

## Core Architecture

```
┌─────────────┐         ┌─────────────┐         ┌─────────────┐
│ Orchestrator│         │   Agent 1   │         │   Agent 2   │
│  (Master)   │ ──────> │  (Worker)   │         │  (Worker)   │
└─────────────┘         └─────────────┘         └─────────────┘
      │                        │                        │
      │                        ▼                        ▼
      │                 ┌─────────────┐         ┌─────────────┐
      │                 │  Worktree 1 │         │  Worktree 2 │
      │                 └─────────────┘         └─────────────┘
      ▼
┌─────────────┐
│ State File  │ ←────── mawep-state.yaml
└─────────────┘
```

## Essential Patterns

### 📁 Memory Bank Pattern

Persist context in your worktree:
```
worktree/memory-bank/
├── activeContext.md    # Current work and decisions
├── technicalNotes.md   # Implementation details
└── blockers.md         # Current impediments
```

### 🔍 Assignment Discovery

Find your work in this order:
1. `*assignment*.md` or `*sprint*.md` files
2. `mawep-workspace/` directory
3. Task invocation message

### 📢 Status Reporting
**Format**: 
```
STATUS: working|complete|blocked
PROGRESS: One-line summary
BLOCKERS: None|Specific issue
NEXT: What you're doing next
```

### 🚨 Breaking Changes

When you change something others depend on:
```
BREAKING CHANGE ALERT
FILE: path/to/file
CHANGE: What changed
AFFECTS: Who needs to know
MIGRATION: How to update
```

### 🌿 Coordination Branch

Shared interfaces go in `.mawep/` directory on coordination branch.

## Role Documentation

### Core Roles
- **Orchestrator** - Coordinates all agents
- **Development Agent** - Implements assigned work
- **Architect Reviewer** - Holistic PR review
- **Technical Reviewer** - Code quality review
- **Post-Mortem Analyst** - Extract learnings

### Quick Start Guide
- **Example Usage** - Step-by-step MAWEP execution
- **Roles Overview** - Understanding all roles

## Framework Documentation

- **Overview** - Framework introduction
- **Design** - Architecture and design
- **Implementation Guide** - Setup instructions
- **Diagrams** - Visual architecture
- **Prompt Engineering** - Prompt design patterns
- **Scenario Walkthrough** - Example workflows

## Pattern Documentation

- **Practical Patterns** - All implementation patterns
- **Coordination Branch** - Shared work pattern

## Project Integration

Projects using MAWEP typically have:
```
project/
├── docs/framework/           # Comprehensive MAWEP docs
│   └── prompts/             # Role-specific instructions
├── mawep-workspace/         # Active work
│   ├── mawep-state.yaml    # Current state
│   ├── sprint-*.md         # Assignments
│   └── worktrees/          # Agent workspaces
```

## Anti-Patterns to Avoid

❌ **DON'T** assume you continue working between invocations
❌ **DON'T** modify other agents' worktrees
❌ **DON'T** reorganize files when making small changes
❌ **DON'T** continue when blocked - report immediately
❌ **DON'T** skip memory-bank updates

## Remember

MAWEP agents are stateless between invocations. Your memory-bank and commits are your only persistence. The orchestrator is your lifeline - without continuous invocation, you freeze.

---
*This is the complete MAWEP module. For project-specific implementation, check the project's `docs/framework/` directory.*