# LMAWEP Quick Start

> ⚠️ **REALITY CHECK**: AI agents complete tasks 10-15x faster than human estimates. 
> A "day's work" often completes in 30-45 minutes. Don't over-allocate time!
>
> **Quick Reference**:
> - Module extraction: 5-10 min
> - Complex creation: 10-15 min  
> - 4-ticket sprint: 30-45 min
> - 8-ticket sprint: 60-90 min
>
> **🎭 PERFORMANCE MODE WARNING**: If you see "completed in record time!" claims without branch URLs, the orchestrator is performing, not delivering.

Start LMAWEP by telling Claude Code:

```
I want you to act as the LMAWEP Orchestrator for parallel local development.

Project: /path/to/your-project
Tickets: 101, 102, 103, 104

Follow the orchestrator instructions

Start by:
1. Creating a lmawep-workspace directory
2. Fetching ticket details with ./lmawep CLI
3. Building dependency graph
4. Creating pods for parallel work
```

## What is LMAWEP?

LMAWEP (Local Multi-Agent Workflow Execution Process) lets you coordinate multiple AI agents working on local tickets in parallel, all from a single Claude Code console, without any remote dependencies.

## Key Resources

- **Start Here**: Example usage walkthrough
- **Orchestrator Guide**: Complete orchestrator instructions
- **Agent Instructions**: Development agent guidelines
- **Ticket System**: Local ticket management
- **Architecture Diagrams**: Visual system architecture

## Core Concepts

1. **You** start one Claude Code session as the orchestrator
2. **Orchestrator** creates pods and invokes agents via Task tool
3. **Pods** are persistent git worktrees where agents work
4. **No background work** - orchestrator continuously monitors
5. **State tracking** in simple YAML file
6. **Local tickets** instead of GitHub issues

## Example State File

The orchestrator creates `lmawep-state.yaml`:

```yaml
pods:
  pod-1:
    status: working
    current_ticket: 101
    worktree_path: ./worktrees/pod-1
    
tickets:
  101:
    title: "Add authentication"
    status: assigned
    assigned_to: pod-1
    dependencies: []
```

## Local Ticket System

LMAWEP uses a file-based ticket system:

```bash
# Initialize ticket system
./lmawep init

# Create tickets
./lmawep ticket create "Add user authentication" feature high
./lmawep ticket create "Add permissions system" feature medium

# List tickets
./lmawep ticket list

# Show ticket details
./lmawep ticket show 101
```

## Monitoring Command

Ask the orchestrator anytime:
```
Show me the current status of all pods and tickets.
```

## Key Differences from MAWEP

1. **Local Only**: No GitHub integration required
2. **File-based Tickets**: YAML files instead of GitHub issues
3. **Local Branches**: Feature branches instead of GitHub PRs
4. **No Remote Push**: All work stays local until manual integration
5. **CLI Management**: Simple `./lmawep` command for ticket operations

## Quick Setup

1. **Copy LMAWEP files** to your project directory
2. **Make CLI executable**: `chmod +x ./lmawep`
3. **Initialize**: `./lmawep init`
4. **Create tickets**: `./lmawep ticket create "My first ticket"`
5. **Start orchestrator**: Tell Claude Code to act as LMAWEP Orchestrator

## Example Workflow

```bash
# 1. Setup
./lmawep init
./lmawep ticket create "Add authentication" feature high
./lmawep ticket create "Add logging" task medium

# 2. Start orchestrator (in Claude Code)
"Act as LMAWEP Orchestrator. Work on tickets 1, 2"

# 3. Monitor progress
"Show status"

# 4. Integration
"Show all completed branches and integrate them"
```

## Benefits of Local Workflow

- ✅ **No external dependencies** - works completely offline
- ✅ **Full control** - no rate limits or API restrictions
- ✅ **Security** - sensitive code never leaves your machine
- ✅ **Speed** - no network latency or remote operations
- ✅ **Simplicity** - straightforward file-based management
- ✅ **Version controlled** - all tickets tracked in git

## See Full Documentation

For complete documentation, see the LMAWEP CLAUDE.md file and framework directory.