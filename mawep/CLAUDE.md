# MAWEP Framework - Complete Documentation

Multi-Agent Workflow Execution Process (MAWEP) - Orchestrate parallel AI development with persistent pods and ephemeral agents.

## 📖 Complete Framework Navigation

### 🚀 Start Here
- @README.md - User-friendly overview and getting started guide
- @MAWEP-QUICKSTART.md - Quick implementation guide
- @mawep-overview.md - Core concepts and Task tool reality
- @mawep-practical-patterns.md - Universal implementation patterns

### 🏗️ Framework Architecture
- @framework/coordination-branch-pattern.md - Shared work coordination strategies
- @framework/mawep-diagrams.md - Visual architecture and flow diagrams
- @framework/mawep-prompt-engineering.md - Prompt design patterns and best practices
- @framework/mawep-scenario-walkthrough.md - Complete example workflows
- @framework/mawep-framework-design.md - Technical design decisions
- @framework/mawep-implementation-guide.md - Implementation strategies

### 🎭 Role-Based Prompts
- @framework/prompts/README.md - Overview of all specialized roles
- @framework/prompts/orchestrator-prompt.md - Primary orchestrator instructions
- @framework/prompts/agent-prompt.md - Development agent work instructions
- @framework/prompts/architect-reviewer-prompt.md - Holistic design review
- @framework/prompts/technical-reviewer-prompt.md - Code quality and standards review
- @framework/prompts/post-mortem-analyst-prompt.md - Workflow analysis and improvement
- @framework/prompts/example-usage.md - Step-by-step execution examples

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
    └── Issue Assignment
```

## 📚 Terminology (No Confusion Zone)

- **🎭 Agent**: Ephemeral Task tool execution (single message → response → gone)
- **🏠 Pod**: Persistent git worktree where agents work over time on assigned issues
- **🎯 Orchestrator**: You - managing state and continuously invoking agents
- **💾 Memory Bank**: Context files in each pod (activeContext.md, progress.md, blockers.md)
- **🔄 Invocation**: Single Task tool message to work in a specific pod

## ⚠️ Common Misconceptions to Avoid

**WRONG**: "I spawned Agent-1 to work on issue #101" (implies ongoing background work)
**RIGHT**: "I invoked an agent for Pod-1 to continue work on issue #101. After the response, I need to invoke again."

**WRONG**: "Agents coordinate with each other" (agents can't communicate)
**RIGHT**: "Orchestrator coordinates agents by reading pod status and making decisions"

**WRONG**: "Let the agents work while I do something else" (nothing happens without invocation)
**RIGHT**: "I need to continuously orchestrate and invoke agents every minute"

## 🚀 Implementation Flow

### 1. Setup Phase
1. Analyze issue dependencies
2. Create mawep-workspace structure
3. Generate git worktrees for independent issues
4. Initialize memory banks for each pod

### 2. Execution Phase
1. Orchestrator invokes agent for pod-1
2. Agent reads memory bank, works on issue, updates progress
3. Agent freezes after response
4. Orchestrator checks status, decides next action
5. Orchestrator invokes next agent (same pod or different pod)
6. Repeat continuously until all issues complete

### 3. Coordination Phase
- Monitor for breaking changes between pods
- Handle blockers and conflicts
- Coordinate PR creation and review
- Clean up completed pods

## 🎸 Success Patterns

### Pod Naming Convention
- `pod-1`, `pod-2`, `pod-3` (not agent-1, agent-2)
- Each pod maps to one primary GitHub issue
- Pod directories: `mawep-workspace/worktrees/pod-N/`

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
```

## 🔧 Git Worktree Management

MAWEP automatically handles:
```bash
# Pod creation
git worktree add mawep-workspace/worktrees/pod-1 -b pod-1-issue-101

# Pod cleanup after completion
git worktree remove mawep-workspace/worktrees/pod-1
git branch -d pod-1-issue-101
```

## 📋 Implementation Checklist

### Before Starting
- [ ] 3+ independent GitHub issues identified
- [ ] Clean main branch (no uncommitted changes)
- [ ] Sufficient disk space (~200MB per pod)
- [ ] Understanding of continuous orchestration requirement

### During Execution
- [ ] Orchestrator invokes agents every 30-60 seconds
- [ ] Monitor pod memory banks for status updates
- [ ] Handle blockers immediately
- [ ] Coordinate breaking changes between pods
- [ ] Track progress in mawep-state.yaml

### After Completion
- [ ] Review all PRs created by pods
- [ ] Merge successful changes
- [ ] Clean up completed pod worktrees
- [ ] Document lessons learned

## Getting Started

**Start with**: @README.md for user-friendly introduction
**Then use**: @framework/prompts/orchestrator-prompt.md for implementation
**Reference**: Other prompts and guides as needed during execution

Remember: MAWEP is about orchestration, not automation. You're the conductor - stay engaged!