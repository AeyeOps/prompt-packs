# MAWEP Framework 🎯

**M**ulti-**A**gent **W**orkflow **E**xecution **P**rocess - Like having a killer development crew that doesn't step on each other's code! 

*Coordinate multiple AI agents working on GitHub issues in parallel while keeping your sanity intact.*

---

## 🚀 What's This All About?

Ever wanted to split up a big development sprint and have multiple AI agents tackle different pieces simultaneously? MAWEP makes it happen without the chaos. Think of it as **your project's mission control** - one orchestrator keeping everyone in sync while agents work in their own isolated zones.

**Perfect for:**
- 🔥 Sprint work with 3-10 independent issues
- 🎯 Module extraction and refactoring
- ⚡ Parallel feature development
- 🏗️ Large-scale code organization

## ⚠️ Reality Check First!

**MAWEP isn't magic** - it's coordination. Before diving in:
- ✅ Use for **independent tasks** that can work in parallel
- ❌ Skip if tasks are tightly coupled or simple
- ✅ Perfect when you have clear issue separation  
- ❌ Overkill for single issues or quick fixes

## 🎮 How It Works (The Basics)

### The Power Structure
```
┌─────────────────┐    "Yo pods, status report!"
│   Orchestrator  │ ──────────────────────────┐
│  (The Director) │                            │
└─────────────────┘                            │
        │                                      ▼
        │              ┌─────────────┐   ┌─────────────┐
        │              │    Pod-1    │   │    Pod-2    │  
        │              │(Issue #101) │   │(Issue #102) │
        │              └─────────────┘   └─────────────┘
        ▼                     │                 │
┌─────────────────┐          │                 │
│ mawep-state.yaml│          ▼                 ▼
│  (The Truth)    │   ┌─────────────┐   ┌─────────────┐
└─────────────────┘   │ Git Worktree│   │ Git Worktree│
                      └─────────────┘   └─────────────┘
```

### Key Terminology (No Confusion Zone!)
- **🎭 Agent**: Ephemeral Task tool execution (single message → response → *poof* gone)
- **🏠 Pod**: Persistent git worktree where agents work over time (like `pod-1`, `pod-2`)
- **🎯 Orchestrator**: The conductor keeping everything in sync

**Critical Reality**: Agents are like phone calls - they start, do one thing, then hang up. Pods are like offices - they persist and hold the work.

## 🛠️ Getting Started (Your First Mission)

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
3. 🌿 **Spawn git worktrees** - Each pod gets its own isolated workspace (`pod-1`, `pod-2`, etc.)
4. 🎯 **Assign work** - Maps issues to pods based on dependencies

### Step 3: Watch the Magic (But Stay Involved!)
- 📺 **Monitor progress**: Ask "Show status" anytime
- 🔄 **Keep it flowing**: Orchestrator handles continuous agent invocation
- 🚨 **Handle blockers**: Jump in when pods report issues

## 📁 What You'll See in Your Project

```
your-project/
├── mawep-workspace/           # ← MAWEP's mission control
│   ├── mawep-state.yaml      # Current status of all pods/issues
│   ├── sprint-assignments.md  # What's assigned to whom
│   └── worktrees/            # Isolated workspaces
│       ├── pod-1/            # Working on issue #101
│       ├── pod-2/            # Working on issue #102
│       └── pod-3/            # Working on issue #103
└── [your regular project files]
```

## 🎯 Success Patterns

### 💾 Memory Bank Pattern
Each pod maintains context between agent visits:
```
pod-1/memory-bank/
├── activeContext.md    # What am I working on?
├── progress.md         # What's done/what's next?
└── blockers.md         # What's stopping me?
```

### 📢 Status Communication
Pods report in this format:
```
STATUS: working|complete|blocked
PROGRESS: Extracted logging module, added tests
BLOCKERS: None
NEXT: Creating PR and running integration tests
```

### 🔔 Breaking Change Alerts
When one pod changes something others need:
```
🚨 BREAKING CHANGE ALERT
FILE: src/auth/types.ts
CHANGE: Added required 'role' field to User interface
AFFECTS: Any code creating User objects
MIGRATION: Set role='user' for existing records
```

## 🎸 Advanced Moves

### Coordination Branch Pattern
For shared interfaces and breaking changes:
- Create `.mawep/` directory on a coordination branch
- Store shared type definitions and interfaces
- Pods reference this for contracts

### Custom Agent Instructions
Tailor agent behavior per project:
- Add project-specific patterns to agent prompts
- Include testing requirements and code style
- Set up specialized review processes

## 🚫 What Not To Do (Learn from Others' Pain)

❌ **Don't assume agents work in background** - They freeze after each response  
❌ **Don't have pods modify each other's code** - Stay in your lane!  
❌ **Don't skip the memory bank** - Agents have amnesia between calls  
❌ **Don't ignore blockers** - Escalate immediately  
❌ **Don't use for tightly coupled work** - Sequential is sometimes better  

## 🔍 When Things Go Sideways

### Pod Stuck or Confused?
1. Check the pod's memory bank for context
2. Look at recent git commits for progress
3. Re-invoke with clearer instructions
4. Consider reassigning the issue

### Integration Conflicts?
1. Pause affected pods
2. Coordinate the fix manually
3. Use breaking change alerts
4. Resume once resolved

### Lost Track of Everything?
Check `mawep-state.yaml` - it's the source of truth for all pod/issue assignments.

## 🎊 Ready to Rock?

MAWEP works best when you:
- 🎯 **Have clear, independent issues** (3+ issues ideal)
- 🏗️ **Need parallel development** (sprint work, refactoring)
- 🎮 **Want to stay in control** (orchestrator = you)
- 💪 **Have separated concerns** (minimal cross-dependencies)

**Start small**, get comfortable with 2-3 pods, then scale up as you learn the rhythm!

---

*Like a well-oiled 80s synth setup - each track (pod) plays its part while the producer (orchestrator) keeps the whole album (project) in perfect harmony.* 🎵✨

**Need more details?** Check out the complete framework documentation in the prompt pack - everything from orchestrator instructions to post-mortem analysis patterns!