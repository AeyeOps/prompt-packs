# LMAWEP Framework 🎯

**L**ocal **M**ulti-**A**gent **W**orkflow **E**xecution **P**rocess - Like having a killer development crew that doesn't step on each other's code, all working locally! 

*Coordinate agents working in persistent pod workspaces on local tickets in parallel while keeping your sanity intact.*

---

## 🚀 What's This All About?

Ever wanted to split up a big development sprint and have multiple AI agents tackle different pieces simultaneously, all working locally without any remote dependencies? LMAWEP makes it happen without the chaos. Think of it as **your project's local mission control** - one orchestrator invokes agents who work in dedicated pods (persistent git worktrees) so they never lose context or step on each other.

**Perfect for:**
- 🔥 Sprint work with 3-10 independent tickets
- 🎯 Module extraction and refactoring  
- ⚡ Parallel feature development
- 🏗️ Large-scale code organization
- 🔒 Local-only development environments
- 🛡️ Air-gapped or secure environments

## ⚠️ Reality Check First!

**LMAWEP isn't magic** - it's coordination. Before diving in:
- ✅ Use for **independent tasks** that can work in parallel
- ❌ Skip if tasks are tightly coupled or simple
- ✅ Perfect when you have clear ticket separation  
- ❌ Overkill for single tickets or quick fixes
- ✅ Great for local development without remote dependencies
- ❌ Not needed if you require GitHub integration

## 🎮 How It Works (The Flow)

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
    │  📁 lmawep-workspace/worktrees/pod-1/       │
    │  ├── [your project files]                  │
    │  ├── memory-bank/                          │
    │  │   ├── activeContext.md                  │
    │  │   ├── progress.md                       │
    │  │   └── blockers.md                       │
    │  ├── .lmawep/                              │
    │  │   └── tickets/                          │
    │  │       └── ticket-101.yaml               │
    │  └── .git/ (worktree branch: pod-1-ticket-101) │
    └─────────────────────────────────────────────┘
```

### Key Terminology (No Confusion Zone!)
- **🎭 Agent**: Ephemeral Claude Task execution (single message → response → *poof* gone)
- **🏠 Pod**: Persistent git worktree + memory bank where agents work over time
- **🎯 Orchestrator**: You - the conductor invoking agents and tracking state
- **🎫 Ticket**: Local YAML-based work item (replaces GitHub issues)

**Critical Reality**: Agents are like phone calls - they start, do one thing, then hang up. Pods are like offices - they persist and hold all the work context.

## 🛠️ Getting Started (Your First Mission)

### Step 1: Launch the Orchestrator
Tell Claude Code:
```
Act as LMAWEP Orchestrator for parallel local development.

Project: /path/to/my-awesome-project
Tickets: 101, 102, 103, 104

Please start by analyzing dependencies and setting up the workspace.
```

### Step 2: Let LMAWEP Set Up Shop
The orchestrator will:
1. 📊 **Analyze ticket dependencies** - What can run in parallel?
2. 🏗️ **Create the workspace** - Sets up `lmawep-workspace/` with state tracking
3. 🌿 **Create git worktrees** - Each pod gets its own isolated workspace (`pod-1`, `pod-2`, etc.)
4. 🎯 **Assign work** - Maps tickets to pods based on dependencies

### Step 3: Watch the Magic (But Stay Involved!)
- 📺 **Monitor progress**: Ask "Show status" anytime
- 🔄 **Keep it flowing**: Orchestrator handles continuous agent invocation for each pod
- 🚨 **Handle blockers**: Jump in when pods report issues

---

## 🔧 Local Ticket System Deep Dive

LMAWEP uses a simple file-based ticket system instead of GitHub issues:

### Ticket Creation
```bash
# Create new ticket
./lmawep ticket create "Add user authentication" --priority high --estimate 2h

# Creates: .lmawep/tickets/ticket-101.yaml
```

### Ticket Structure
```yaml
id: 101
title: "Add user authentication"
description: |
  Implement JWT-based authentication system
  - Add login/logout endpoints
  - Create middleware for protected routes
  - Add session management
status: ready  # ready, assigned, working, blocked, complete
priority: high  # low, medium, high, critical
estimate: "2h"
assigned_to: null  # or pod-1, pod-2, etc.
dependencies: []  # list of ticket IDs this depends on
created: "2024-01-15T10:30:00Z"
assignee_notes: ""
blockers: []
```

### Ticket Commands
```bash
# List all tickets
./lmawep ticket list

# Show ticket details  
./lmawep ticket show 101

# Update ticket status
./lmawep ticket update 101 --status working

# Add dependency
./lmawep ticket depend 102 101  # 102 depends on 101
```

## 🔧 Git Worktree Deep Dive (The Technical Stuff)

### Pod Creation (What LMAWEP Does Automatically)
```bash
# For each pod, LMAWEP runs:
git worktree add lmawep-workspace/worktrees/pod-1 -b pod-1-ticket-101
git worktree add lmawep-workspace/worktrees/pod-2 -b pod-2-ticket-102
git worktree add lmawep-workspace/worktrees/pod-3 -b pod-3-ticket-103

# Creates isolated working directories:
your-project/
├── lmawep-workspace/
│   └── worktrees/
│       ├── pod-1/    ← Complete project copy on branch pod-1-ticket-101
│       ├── pod-2/    ← Complete project copy on branch pod-2-ticket-102
│       └── pod-3/    ← Complete project copy on branch pod-3-ticket-103
└── [main project files]  ← Your original main branch
```

### Pod Management Commands (For Manual Control)
```bash
# Check all active pods
git worktree list

# Example output:
# /opt/project                     deadbeef [main]
# /opt/project/lmawep-workspace/worktrees/pod-1  abcd1234 [pod-1-ticket-101]
# /opt/project/lmawep-workspace/worktrees/pod-2  efgh5678 [pod-2-ticket-102]

# Remove completed pod (after merging branch)
git worktree remove lmawep-workspace/worktrees/pod-1
git branch -d pod-1-ticket-101

# Move pod location (if needed)
git worktree move lmawep-workspace/worktrees/pod-1 ../backup/

# Repair corrupted pod
git worktree repair lmawep-workspace/worktrees/pod-1
```

### Understanding Pod Isolation
Each pod is a **complete, independent copy** of your project:

```bash
# Pod-1 can have different files than Pod-2:
pod-1/src/auth.ts     ← Working on auth refactor
pod-2/src/auth.ts     ← Original version (different branch)

# Changes don't affect each other until merge time
cd lmawep-workspace/worktrees/pod-1
git status  # Shows only pod-1 changes

cd ../pod-2  
git status  # Shows only pod-2 changes
```

### Memory Bank Pattern (Context Persistence)
```
pod-1/memory-bank/
├── activeContext.md    # "Working on extracting logging module from __main__.py"
├── progress.md         # "✅ Extracted constants.py ⏳ Working on utils/helpers.py"
├── blockers.md         # "Need clarification on error handling patterns"
└── systemPatterns.md   # "This codebase uses typer for CLI, pathlib for paths"
```

**Why This Matters**: When you invoke an agent for pod-1 tomorrow, it reads these files and knows exactly where it left off!

---

## 📁 Complete Project Structure

```
your-project/
├── lmawep-workspace/              # ← LMAWEP's mission control
│   ├── lmawep-state.yaml         # Pod assignments and status
│   ├── sprint-2-assignments.md   # Human-readable assignments
│   └── worktrees/               # Isolated development pods
│       ├── pod-1/               # Git worktree for ticket #101
│       │   ├── [full project]   # Complete project copy
│       │   └── memory-bank/     # Agent context files
│       ├── pod-2/               # Git worktree for ticket #102
│       │   ├── [full project]   # Complete project copy  
│       │   └── memory-bank/     # Agent context files
│       └── pod-3/               # Git worktree for ticket #103
├── .lmawep/                     # Local ticket system
│   ├── config.yaml              # LMAWEP configuration
│   ├── tickets/                 # Individual ticket files
│   │   ├── ticket-101.yaml      # Ticket details
│   │   ├── ticket-102.yaml
│   │   └── ticket-103.yaml
│   └── templates/               # Ticket templates
└── [your regular project files] # Main branch (untouched during work)
```

## 🎯 Success Patterns

### 📢 Pod Status Communication
When agents work in pods, they report in this format:
```
STATUS: working|complete|blocked|needs-review
PROGRESS: Extracted logging module, added tests, ready for integration
BLOCKERS: None
NEXT: Waiting for manual review, then working on config module
```

### 🔔 Breaking Change Alerts
When one pod changes something others need:
```
🚨 BREAKING CHANGE ALERT
POD: pod-1
FILE: src/auth/types.ts  
CHANGE: Added required 'role' field to User interface
AFFECTS: Any code creating User objects
MIGRATION: Set role='user' for existing records
```

### 🔄 Pod Lifecycle Management
```bash
# Typical pod lifecycle:
1. git worktree add worktrees/pod-1 -b pod-1-ticket-101
2. Agent works in pod-1/ over multiple invocations
3. Agent completes work and commits to local branch
4. Local review and integration into main
5. git worktree remove worktrees/pod-1
6. git branch -d pod-1-ticket-101
```

## 🎸 Advanced Moves

### Coordination Branch Pattern
For shared interfaces and breaking changes:
```bash
# Create coordination branch with shared contracts
git checkout -b lmawep-coordination
mkdir .lmawep-shared
echo "export interface User { id: string; role: string; }" > .lmawep-shared/types.ts
git add .lmawep-shared/ && git commit -m "Add shared type contracts"

# Pods can reference this for consistency
# pod-1: import { User } from '../../../.lmawep-shared/types.ts'
```

### Pod Recovery Strategies
```bash
# Pod got corrupted or confused?
git worktree remove worktrees/pod-1      # Remove the workspace
git branch -D pod-1-ticket-101           # Delete the branch  
git worktree add worktrees/pod-1 -b pod-1-ticket-101  # Start fresh
# Agent reads memory-bank/ to restore context
```

### Performance Considerations
- **Each pod**: ~100-200MB (full project copy)
- **Recommended max**: 5-7 active pods (disk space)
- **Network**: No network required (fully local)
- **IDE**: Open multiple VS Code windows, one per pod

## 🚫 What Not To Do (Learn from Others' Pain)

❌ **Don't assume agents work continuously** - They stop after each response  
❌ **Don't have pods modify each other's files** - Stay in your lane!  
❌ **Don't skip the memory bank** - Agents have amnesia between invocations  
❌ **Don't ignore blockers** - Escalate to orchestrator immediately  
❌ **Don't use for tightly coupled work** - Sequential is sometimes better  
❌ **Don't forget to clean up** - Remove completed pods to save disk space

## 🔍 When Things Go Sideways

### Pod Stuck or Confused?
```bash
# Debug checklist:
1. cd lmawep-workspace/worktrees/pod-1
2. cat memory-bank/activeContext.md     # What was it working on?
3. git log --oneline -n 5               # What did it accomplish?
4. git status                           # What's uncommitted?
5. Re-invoke agent with clearer context
```

### Integration Conflicts?
```bash
# Conflict resolution:
1. Pause affected pods (update lmawep-state.yaml)
2. cd worktrees/pod-1 && git fetch origin main
3. git merge origin/main  # Resolve conflicts manually
4. Update memory-bank/blockers.md
5. Resume pod work
```

### Lost Track of Everything?
- Check `lmawep-state.yaml` - source of truth for all assignments
- Run `git worktree list` - see all active pods
- Look at each `pod-N/memory-bank/progress.md` for status
- Use `./lmawep ticket list` to see all tickets

## 🎊 Ready to Rock?

LMAWEP works best when you:
- 🎯 **Have clear, independent tickets** (3+ tickets ideal)
- 🏗️ **Need parallel development** (sprint work, refactoring)
- 🎮 **Want to stay in control** (orchestrator = you)
- 💪 **Have separated concerns** (minimal cross-dependencies)
- 🔒 **Need local-only development** (no remote dependencies)

**Start small**, get comfortable with 2-3 pods, then scale up as you learn the rhythm!

### Quick Start Checklist
- [ ] 3+ independent local tickets ready
- [ ] Clean main branch (no uncommitted changes)
- [ ] ~1GB free disk space (for pod worktrees)
- [ ] Claude Code session ready
- [ ] Tell Claude: "Act as LMAWEP Orchestrator..."

---

*Like a well-oiled 80s synth setup - each track (pod) plays its part while the producer (orchestrator) keeps the whole album (project) in perfect harmony.* 🎵✨

**Need the complete prompt library?** This README just scratches the surface - check out the full framework documentation for orchestrator prompts, agent instructions, post-mortem analysis patterns, and advanced coordination strategies!