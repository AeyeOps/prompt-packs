# LMAWEP Framework - Complete Documentation

Local Multi-Agent Workflow Execution Process (LMAWEP) - Orchestrate parallel AI development with persistent pods and ephemeral agents, completely local.

## 📖 Complete Framework Navigation

### 🚀 Start Here
- @README.md - User-friendly overview and getting started guide
- @LMAWEP-QUICKSTART.md - Quick implementation guide
- @lmawep-overview.md - Core concepts and Task tool reality
- @lmawep-practical-patterns.md - Universal implementation patterns
- @lmawep-ticket-system.md - Local ticket management system

### 🏗️ Framework Architecture
- @framework/coordination-branch-pattern.md - Shared work coordination strategies
- @framework/lmawep-diagrams.md - Visual architecture and flow diagrams
- @framework/lmawep-prompt-engineering.md - Prompt design patterns and best practices
- @framework/lmawep-scenario-walkthrough.md - Complete example workflows

### 🎭 Role-Based Prompts
- @framework/prompts/README.md - Overview of all specialized roles
- @framework/prompts/orchestrator-prompt.md - Primary orchestrator instructions (LOCAL)
- @framework/prompts/agent-prompt.md - Development agent work instructions (LOCAL)
- @framework/prompts/architect-reviewer-prompt.md - Holistic design review
- @framework/prompts/technical-reviewer-prompt.md - Code quality and standards review
- @framework/prompts/post-mortem-analyst-prompt.md - Workflow analysis and improvement

### 🎫 Local Ticket System
- @lmawep-ticket-system.md - Complete ticket system documentation
- @lmawep - Executable CLI for ticket management

## 🎯 Critical Concepts (Reality Check)

### The Task Tool Reality
1. **NO BACKGROUND WORK** - Task tool = Single message → response → FREEZE
2. **Continuous Invocation Required** - Orchestrator must invoke every 30-60 seconds or work stops
3. **Agents are Stateless** - They remember nothing between invocations
4. **Pods Provide Persistence** - Git worktrees + memory banks maintain context
5. **Orchestrator Never Stops** - Must continuously manage and invoke agents
6. **Fail Fast Protocol** - Report blockers immediately to prevent stalls

### Architecture Overview
```
Orchestrator (You)
    ↓ Invokes agents for specific pods
Agent Invocation (Task tool)
    ↓ Works in designated pod
Pod (Persistent)
    ├── Git Worktree (isolated branch)
    ├── Memory Bank (context files)
    └── Ticket Assignment (local YAML)
```

## 🔑 Key Differences from MAWEP

### Local-Only Operation
- **No GitHub Integration** - Uses local ticket system instead
- **No Remote Dependencies** - Works completely offline
- **File-based Tickets** - YAML files instead of GitHub issues
- **Local Branches** - Feature branches instead of GitHub PRs
- **CLI Management** - Simple `./lmawep` command for operations

### Ticket Management
```bash
# Instead of: gh issue view 101
./lmawep ticket show 101

# Instead of: gh issue list --label ready
./lmawep ticket list --status ready

# Instead of: gh pr create
# Create local branch + update ticket status
```

### Integration Process
- **No PR Creation** - Direct branch management
- **Local Review** - Manual code review of branches
- **Sequential Integration** - Merge branches one by one
- **No CI/CD** - Local testing only

## 📚 Terminology (No Confusion Zone)

- **🎭 Agent**: Ephemeral Task tool execution (single message → response → gone)
- **🏠 Pod**: Persistent git worktree where agents work over time on assigned tickets
- **🎯 Orchestrator**: You - managing state and continuously invoking agents
- **🎫 Ticket**: Local YAML-based work item (replaces GitHub issues)
- **💾 Memory Bank**: Context files in each pod (activeContext.md, progress.md, blockers.md)
- **🔄 Invocation**: Single Task tool message to work in a specific pod

## ⚠️ Common Misconceptions to Avoid

**WRONG**: "I spawned Agent-1 to work on ticket #101" (implies ongoing background work)
**RIGHT**: "I invoked an agent for Pod-1 to continue work on ticket #101. After the response, I need to invoke again."

**WRONG**: "Agents coordinate with each other" (agents can't communicate)
**RIGHT**: "Orchestrator coordinates agents by reading pod status and making decisions"

**WRONG**: "Let the agents work while I do something else" (nothing happens without invocation)
**RIGHT**: "I need to continuously orchestrate and invoke agents every minute"

## 🚀 Implementation Flow

### 1. Setup Phase
1. Initialize LMAWEP with `./lmawep init`
2. Create local tickets with `./lmawep ticket create`
3. Analyze ticket dependencies
4. Create lmawep-workspace structure
5. Generate git worktrees for independent tickets
6. Initialize memory banks for each pod

### 2. Execution Phase
1. Orchestrator invokes agent for pod-1
2. Agent reads memory bank, works on ticket, updates progress
3. Agent freezes after response
4. Orchestrator checks status, decides next action
5. Orchestrator invokes next agent (same pod or different pod)
6. Repeat continuously until all tickets complete

### 3. Integration Phase
- Monitor for breaking changes between pods
- Handle blockers and conflicts
- Coordinate branch creation and review
- Sequential branch integration
- Clean up completed pods

## 🎸 Success Patterns

### Pod Naming Convention
- `pod-1`, `pod-2`, `pod-3` (not agent-1, agent-2)
- Each pod maps to one primary local ticket
- Pod directories: `lmawep-workspace/worktrees/pod-N/`

### Memory Bank Structure
```
pod-1/memory-bank/
├── activeContext.md     # Current work focus
├── progress.md          # Completed tasks and next steps
├── blockers.md          # Issues requiring orchestrator intervention
├── productContext.md    # Project understanding
├── systemPatterns.md    # Codebase patterns and conventions
└── techContext.md       # Technical decisions and architecture
```

### Status Communication Format
```
STATUS: working|complete|blocked|needs-review
PROGRESS: [Specific accomplishments this session]
BLOCKERS: [Issues preventing progress]
NEXT: [Planned next actions]
BRANCH: [Feature branch name when ready]
```

## 🔧 Local Workflow Management

### Ticket Operations
```bash
# Create tickets
./lmawep ticket create "Add authentication" feature high

# List and filter
./lmawep ticket list --status ready
./lmawep ticket list --priority high

# Update status
./lmawep ticket update 101 status working
./lmawep ticket update 101 assigned_to pod-1
```

### Git Worktree Management
LMAWEP automatically handles:
```bash
# Pod creation
git worktree add lmawep-workspace/worktrees/pod-1 -b pod-1-ticket-101

# Pod cleanup after completion
git worktree remove lmawep-workspace/worktrees/pod-1
git branch -d pod-1-ticket-101
```

## 📋 Implementation Checklist

### Before Starting
- [ ] 3+ independent local tickets created
- [ ] Clean main branch (no uncommitted changes)
- [ ] Sufficient disk space (~200MB per pod)
- [ ] Understanding of continuous orchestration requirement
- [ ] LMAWEP CLI initialized and working

### During Execution
- [ ] Orchestrator invokes agents every 30-60 seconds
- [ ] Monitor pod memory banks for status updates
- [ ] Handle blockers immediately
- [ ] Coordinate breaking changes between pods
- [ ] Track progress in lmawep-state.yaml
- [ ] Update ticket statuses with CLI

### After Completion
- [ ] Review all branches created by pods
- [ ] Merge successful changes sequentially
- [ ] Clean up completed pod worktrees
- [ ] Update all ticket statuses to complete
- [ ] Document lessons learned

## 🔄 Local Integration Protocol

### Branch Management
1. **Each pod creates feature branch**: `feature/101-add-auth`
2. **Clear commit history**: Conventional commit messages
3. **Ready for integration**: No merge conflicts with main
4. **Sequential merge**: One branch at a time with testing

### Quality Gates
- All tests pass locally
- Code follows project standards
- Documentation updated
- No merge conflicts
- Clear commit messages

## Getting Started

**Start with**: @README.md for user-friendly introduction
**Then use**: @framework/prompts/orchestrator-prompt.md for implementation
**Reference**: Other prompts and guides as needed during execution

Remember: LMAWEP is about orchestration, not automation. You're the conductor - stay engaged!

## 🔧 Setup Instructions

1. **Copy LMAWEP to your project**:
   ```bash
   cp -r /path/to/LMAWEP/* /your/project/
   chmod +x ./lmawep
   ```

2. **Initialize ticket system**:
   ```bash
   ./lmawep init
   ```

3. **Create your first tickets**:
   ```bash
   ./lmawep ticket create "My first ticket" task medium
   ```

4. **Start orchestrating**:
   ```
   Tell Claude Code: "Act as LMAWEP Orchestrator. Work on tickets 1, 2, 3."
   ```

The future of local AI development coordination starts here.