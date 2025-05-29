# LMAWEP Overview

**🎭 REALITY CHECK**: Thinking LMAWEP will "automate everything"? Wrong. LMAWEP requires constant orchestration. If that sounds like work, use sequential development instead.

LMAWEP (Local Multi-Agent Workflow Execution Process) is a pure prompt-based orchestration framework that uses Claude Code's Task tool to coordinate parallel AI agent development, completely locally without any remote dependencies.

## What LMAWEP Actually Is

LMAWEP is:
- **A set of prompts and patterns** for multi-agent coordination
- **Built on Claude Code's Task tool** - no external dependencies
- **State managed with simple YAML files** - no databases needed
- **Agent isolation via git worktrees** - clean parallel development
- **Local ticket system** - file-based instead of GitHub issues
- **Completely offline** - no network requirements

## What LMAWEP is NOT

LMAWEP is NOT:
- A Python framework
- A distributed system requiring Redis
- A complex infrastructure project
- Something you need to "install" or "deploy"
- Dependent on GitHub or any remote services
- A replacement for proper version control

## How It Works

1. **One Orchestrator** - A Claude Code instance acting as coordinator
2. **Multiple Pods** - Persistent git worktrees assigned to tickets
3. **Multiple Agents** - Ephemeral Task executions working within pods
4. **State Tracking** - Simple YAML file tracks pod assignments
5. **Continuous Invocation** - Orchestrator invokes agents in pods every 30-60 seconds
6. **Horizontal Scaling** - Each pod works on different tickets to minimize conflicts
7. **Local Tickets** - File-based ticket system for work management

### When to Use LMAWEP

✅ **Use LMAWEP when:**
- Sprint can complete in < 2 hours (remember: AI is 10-15x faster)
- Tickets have clear dependency structure  
- Modules can be designed standalone
- Integration complexity is manageable
- You need local-only development (security, air-gapped, etc.)
- You want full control without external dependencies

❌ **Avoid LMAWEP when:**
- Heavy inter-module dependencies during development
- Continuous cross-pod communication needed
- Integration would exceed development time
- Simple sequential work is more appropriate
- You need GitHub integration (use MAWEP instead)

## Key Insight

LMAWEP's power comes from its simplicity. By using only Claude Code's native Task tool and local file systems, it avoids the complexity of distributed systems and external dependencies while achieving effective parallel coordination.

## ⚠️ Critical Understanding: Task Tool Behavior ⚠️

Many orchestrators incorrectly believe agents work in the background. Here's what ACTUALLY happens:

```
Timeline of Reality:
==================
10:00:00 | Orchestrator invokes Agent via Task tool targeting Pod-1
10:00:01 | Agent receives message, works in Pod-1 worktree
10:00:05 | Agent sends response, updates Pod-1 state
10:00:05 | Agent COMPLETELY STOPS ❌
10:00:05 | 
   to    | ⏸️ NOTHING IS HAPPENING ⏸️
10:00:30 | 
10:00:30 | Orchestrator invokes new Agent targeting Pod-1 again  
10:00:31 | New Agent receives message (reads Pod-1 state for context)
10:00:35 | New Agent sends response, updates Pod-1 state
10:00:35 | Agent COMPLETELY STOPS AGAIN ❌
```

**Mental Model**:
- Task tool = Sending a text message
- Agent = Person who reads it, replies once, then puts phone away
- Without new messages, they do NOTHING

This is why continuous invocation every 30-60 seconds is MANDATORY, not optional.

## Local Ticket System

LMAWEP replaces GitHub issues with a lightweight file-based system:

### Ticket Structure
```yaml
id: 101
title: "Add user authentication"
description: |
  Implement JWT-based authentication
  - Login/logout endpoints
  - Session management
status: ready  # ready, assigned, working, blocked, review, complete
priority: high
dependencies: []
assigned_to: null
created_at: "2024-01-15T10:30:00Z"
```

### CLI Operations
```bash
# Initialize system
./lmawep init

# Create tickets
./lmawep ticket create "Add authentication" feature high

# List tickets
./lmawep ticket list --status ready

# Update status
./lmawep ticket update 101 status working
```

## Local Workflow Benefits

1. **No External Dependencies**: Works completely offline
2. **Full Security**: Code never leaves your machine
3. **No Rate Limits**: No API restrictions or GitHub quotas
4. **Speed**: No network latency or remote operations
5. **Control**: Complete ownership of process and data
6. **Simplicity**: File-based management is transparent
7. **Version Controlled**: All tickets and state can be committed

## Getting Started

1. Copy LMAWEP files to your project
2. Make CLI executable: `chmod +x ./lmawep`
3. Initialize: `./lmawep init`
4. Create tickets: `./lmawep ticket create "My task"`
5. Tell Claude Code to act as orchestrator
6. Provide ticket numbers to work on

That's it. No setup, no configuration, no external accounts - just prompts and local files.

## Comparison with MAWEP

| Feature | MAWEP | LMAWEP |
|---------|-------|---------|
| Issue Tracking | GitHub Issues | Local YAML files |
| Pull Requests | GitHub PRs | Local feature branches |
| Dependencies | GitHub CLI, internet | None (fully local) |
| Security | Data on GitHub | Data stays local |
| Rate Limits | GitHub API limits | None |
| Offline Work | No | Yes |
| Setup Complexity | Medium (GitHub setup) | Low (just files) |
| Integration | GitHub workflow | Manual branch merging |

## Architecture Principles

1. **Stateless Agents**: Each invocation is independent
2. **Persistent Pods**: Worktrees maintain context between invocations
3. **File-based State**: YAML files for all tracking
4. **Git Isolation**: Worktrees prevent conflicts
5. **Continuous Orchestration**: Manual coordination required
6. **Local Control**: No external service dependencies

LMAWEP brings the power of coordinated parallel AI development to any environment, regardless of connectivity or external service availability.