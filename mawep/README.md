# MAWEP Framework

**M**ulti-**A**gent **W**orkflow **E**xecution **P**rocess - Coordinate multiple AI agents working on GitHub issues in parallel using isolated workspaces.

*A framework for orchestrating parallel development with persistent pod workspaces and ephemeral agents.*

---

## Overview

MAWEP enables parallel development by coordinating multiple AI agents working on different issues simultaneously. The framework provides mission control where an orchestrator manages agents working in isolated pods (persistent git worktrees) to maintain context and prevent conflicts.

**Use Cases:**
- Sprint work with 3-10 independent issues
- Module extraction and refactoring
- Parallel feature development
- Large-scale code organization

## Prerequisites and Limitations

**MAWEP isn't magic** - it's coordination. Before diving in:
- ✅ Use for **independent tasks** that can work in parallel
- ❌ Skip if tasks are tightly coupled or simple
- ✅ Perfect when you have clear issue separation  
- ❌ Overkill for single issues or quick fixes

## How It Works

### The Complete Architecture
```
                    ┌─────────────────┐
                    │   Orchestrator  │ ← You manage this
                    │  (Your Control) │
                    └─────────┬───────┘
                              │
                   "Invoke agent for pod-1"
                              │
                              ▼
┌─────────────────────────────────────────────────────────┐
│                    Agent Invocation                     │ ← Ephemeral
│  "Work on pod-1, check memory-bank, update progress"   │   (Claude Task)
└──────────────────────┬──────────────────────────────────┘
                       │
            Agent works in persistent pod
                       │
                       ▼
    ┌─────────────────────────────────────────────┐
    │               Pod-1 Workspace               │ ← Persistent
    │         (Git Worktree + Memory Bank)        │   (Survives)
    │                                             │
    │  📁 mawep-workspace/worktrees/pod-1/        │
    │  ├── [your project files]                  │
    │  ├── memory-bank/                          │
    │  │   ├── activeContext.md                  │
    │  │   ├── progress.md                       │
    │  │   └── blockers.md                       │
    │  └── .git/ (worktree branch: pod-1-issue-101) │
    └─────────────────────────────────────────────┘
```

### Key Terminology
- **Agent**: Ephemeral Claude Task execution (single message → response → completes)
- **Pod**: Persistent git worktree + memory bank where agents work over time
- **Orchestrator**: You - the conductor invoking agents and tracking state

**Critical Reality**: Agents are like phone calls - they start, do one thing, then hang up. Pods are like offices - they persist and hold all the work context.

## Getting Started

### Step 1: Launch the Orchestrator
Tell Claude Code:
```
Act as MAWEP Orchestrator for parallel development.

Repository: my-org/awesome-project
Issues: #101, #102, #103, #104

Please start by analyzing dependencies and setting up the workspace.
```

### Step 2: Let MAWEP Set Up Shop
The orchestrator will:
1. 📊 **Analyze issue dependencies** - What can run in parallel?
2. 🏗️ **Create the workspace** - Sets up `mawep-workspace/` with state tracking
3. 🌿 **Create git worktrees** - Each pod gets its own isolated workspace (`pod-1`, `pod-2`, etc.)
4. 🎯 **Assign work** - Maps issues to pods based on dependencies

### Step 3: Monitor and Coordinate
- **Monitor progress**: Ask "Show status" anytime
- **Keep it flowing**: Orchestrator handles continuous agent invocation for each pod
- **Handle blockers**: Jump in when pods report issues

---

## Git Worktree Architecture

### Pod Creation Process
```bash
# For each pod, MAWEP runs:
git worktree add mawep-workspace/worktrees/pod-1 -b pod-1-issue-101
git worktree add mawep-workspace/worktrees/pod-2 -b pod-2-issue-102
git worktree add mawep-workspace/worktrees/pod-3 -b pod-3-issue-103

# Creates isolated working directories:
your-project/
├── mawep-workspace/
│   └── worktrees/
│       ├── pod-1/    ← Complete project copy on branch pod-1-issue-101
│       ├── pod-2/    ← Complete project copy on branch pod-2-issue-102
│       └── pod-3/    ← Complete project copy on branch pod-3-issue-103
└── [main project files]  ← Your original main branch
```

### Pod Management Commands
```bash
# Check all active pods
git worktree list

# Example output:
# /opt/project                     deadbeef [main]
# /opt/project/mawep-workspace/worktrees/pod-1  abcd1234 [pod-1-issue-101]
# /opt/project/mawep-workspace/worktrees/pod-2  efgh5678 [pod-2-issue-102]

# Remove completed pod (after merging PR)
git worktree remove mawep-workspace/worktrees/pod-1
git branch -d pod-1-issue-101

# Move pod location (if needed)
git worktree move mawep-workspace/worktrees/pod-1 ../backup/

# Repair corrupted pod
git worktree repair mawep-workspace/worktrees/pod-1
```

### Understanding Pod Isolation
Each pod is a **complete, independent copy** of your project:

```bash
# Pod-1 can have different files than Pod-2:
pod-1/src/auth.ts     ← Working on auth refactor
pod-2/src/auth.ts     ← Original version (different branch)

# Changes don't affect each other until merge time
cd mawep-workspace/worktrees/pod-1
git status  # Shows only pod-1 changes

cd ../pod-2  
git status  # Shows only pod-2 changes
```

### Memory Bank Pattern
```
pod-1/memory-bank/
├── activeContext.md    # "Working on extracting logging module from __main__.py"
├── progress.md         # "✅ Extracted constants.py ⏳ Working on utils/helpers.py"
├── blockers.md         # "Need clarification on error handling patterns"
└── systemPatterns.md   # "This codebase uses typer for CLI, pathlib for paths"
```

**Why This Matters**: When you invoke an agent for pod-1 tomorrow, it reads these files and knows exactly where it left off!

---

## Project Structure

```
your-project/
├── mawep-workspace/              # ← MAWEP's mission control
│   ├── mawep-state.yaml         # Pod assignments and status
│   ├── sprint-2-assignments.md   # Human-readable assignments
│   └── worktrees/               # Isolated development pods
│       ├── pod-1/               # Git worktree for issue #101
│       │   ├── [full project]   # Complete project copy
│       │   └── memory-bank/     # Agent context files
│       ├── pod-2/               # Git worktree for issue #102
│       │   ├── [full project]   # Complete project copy  
│       │   └── memory-bank/     # Agent context files
│       └── pod-3/               # Git worktree for issue #103
└── [your regular project files] # Main branch (untouched during work)
```

## Success Patterns

### Pod Status Communication
When agents work in pods, they report in this format:
```
STATUS: working|complete|blocked|needs-review
PROGRESS: Extracted logging module, added tests, created PR #205
BLOCKERS: None
NEXT: Waiting for PR review, then working on config module
```

### Breaking Change Alerts
When one pod changes something others need:
```
🚨 BREAKING CHANGE ALERT
POD: pod-1
FILE: src/auth/types.ts  
CHANGE: Added required 'role' field to User interface
AFFECTS: Any code creating User objects
MIGRATION: Set role='user' for existing records
```

### Pod Lifecycle Management
```bash
# Typical pod lifecycle:
1. git worktree add worktrees/pod-1 -b pod-1-issue-101
2. Agent works in pod-1/ over multiple invocations
3. Agent creates PR from pod-1-issue-101 → main
4. PR gets reviewed and merged
5. git worktree remove worktrees/pod-1
6. git branch -d pod-1-issue-101
```

## Advanced Patterns

### Coordination Branch Pattern
For shared interfaces and breaking changes:
```bash
# Create coordination branch with shared contracts
git checkout -b mawep-coordination
mkdir .mawep
echo "export interface User { id: string; role: string; }" > .mawep/types.ts
git add .mawep/ && git commit -m "Add shared type contracts"

# Pods can reference this for consistency
# pod-1: import { User } from '../../../.mawep/types.ts'
```

### Pod Recovery Strategies
```bash
# Pod got corrupted or confused?
git worktree remove worktrees/pod-1      # Remove the workspace
git branch -D pod-1-issue-101            # Delete the branch  
git worktree add worktrees/pod-1 -b pod-1-issue-101  # Start fresh
# Agent reads memory-bank/ to restore context
```

### Performance Considerations
- **Each pod**: ~100-200MB (full project copy)
- **Recommended max**: 5-7 active pods (disk space)
- **Network**: Each pod can `git push` independently
- **IDE**: Open multiple VS Code windows, one per pod

## Common Pitfalls

❌ **Don't assume agents work continuously** - They stop after each response  
❌ **Don't have pods modify each other's files** - Stay in your lane!  
❌ **Don't skip the memory bank** - Agents have amnesia between invocations  
❌ **Don't ignore blockers** - Escalate to orchestrator immediately  
❌ **Don't use for tightly coupled work** - Sequential is sometimes better  
❌ **Don't forget to clean up** - Remove completed pods to save disk space

## Troubleshooting

### Pod Stuck or Confused?
```bash
# Debug checklist:
1. cd mawep-workspace/worktrees/pod-1
2. cat memory-bank/activeContext.md     # What was it working on?
3. git log --oneline -n 5               # What did it accomplish?
4. git status                           # What's uncommitted?
5. Re-invoke agent with clearer context
```

### Integration Conflicts?
```bash
# Conflict resolution:
1. Pause affected pods (update mawep-state.yaml)
2. cd worktrees/pod-1 && git fetch origin main
3. git merge origin/main  # Resolve conflicts manually
4. Update memory-bank/blockers.md
5. Resume pod work
```

### Lost Track of Everything?
- Check `mawep-state.yaml` - source of truth for all assignments
- Run `git worktree list` - see all active pods
- Look at each `pod-N/memory-bank/progress.md` for status

## Implementation Checklist

MAWEP works best when you:
- **Have clear, independent issues** (3+ issues ideal)
- **Need parallel development** (sprint work, refactoring)
- **Want to stay in control** (orchestrator = you)
- **Have separated concerns** (minimal cross-dependencies)

**Start small**, get comfortable with 2-3 pods, then scale up as you learn the rhythm!

### Quick Start Checklist
- [ ] 3+ independent GitHub issues ready
- [ ] Clean main branch (no uncommitted changes)
- [ ] ~1GB free disk space (for pod worktrees)
- [ ] Claude Code session ready
- [ ] Tell Claude: "Act as MAWEP Orchestrator..."

---


**Need the complete prompt library?** This README just scratches the surface - check out the full framework documentation for orchestrator prompts, agent instructions, post-mortem analysis patterns, and advanced coordination strategies!