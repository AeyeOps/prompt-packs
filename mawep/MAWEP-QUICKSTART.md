# MAWEP Quick Start

Start MAWEP by telling Claude Code:

```
I want you to act as the MAWEP Orchestrator for parallel GitHub development.

Repository: [your-org/your-repo]
Issues: #101, #102, #103, #104

Follow the orchestrator instructions

Start by:
1. Creating a mawep-workspace directory
2. Fetching issue details with gh
3. Building dependency graph
4. Creating pods for parallel work
```

## What is MAWEP?

MAWEP (Multi-Agent Workflow Execution Process) lets you coordinate multiple AI agents working on GitHub issues in parallel, all from a single Claude Code console.

## Key Resources

- **Start Here**: Example usage walkthrough
- **Orchestrator Guide**: Complete orchestrator instructions
- **Agent Instructions**: Development agent guidelines
- **Architecture Diagrams**: Visual system architecture

## Core Concepts

1. **You** start one Claude Code session as the orchestrator
2. **Orchestrator** creates pods and invokes agents via Task tool
3. **Pods** are persistent git worktrees where agents work
4. **No background work** - orchestrator continuously monitors
5. **State tracking** in simple YAML file

## Example State File

The orchestrator creates `mawep-state.yaml`:

```yaml
pods:
  pod-1:
    status: working
    current_issue: 101
    worktree_path: ./worktrees/pod-1
    
issues:
  101:
    title: "Add authentication"
    status: assigned
    assigned_to: pod-1
    dependencies: []
```

## Monitoring Command

Ask the orchestrator anytime:
```
Show me the current status of all pods and issues.
```

## See Full Documentation

For complete documentation, see the MAWEP CLAUDE.md file.