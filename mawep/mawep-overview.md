# MAWEP Overview

MAWEP (Multi-Agent Workflow Execution Process) is a pure prompt-based orchestration framework that uses Claude Code's Task tool to coordinate parallel AI agent development.

## What MAWEP Actually Is

MAWEP is:
- **A set of prompts and patterns** for multi-agent coordination
- **Built on Claude Code's Task tool** - no external dependencies
- **State managed with simple YAML files** - no databases needed
- **Agent isolation via git worktrees** - clean parallel development

## What MAWEP is NOT

MAWEP is NOT:
- A Python framework
- A distributed system requiring Redis
- A complex infrastructure project
- Something you need to "install" or "deploy"

## How It Works

1. **One Orchestrator** - A Claude Code instance acting as coordinator
2. **Multiple Agents** - Spawned via Task tool to work on issues
3. **State Tracking** - Simple YAML file tracks assignments
4. **Continuous Invocation** - Orchestrator polls agents every 30-60 seconds
5. **Git Worktrees** - Each agent works in isolation

## When to Use MAWEP

Perfect for:
- Parallel development of 3+ related GitHub issues
- Large refactoring with clear task boundaries
- Feature development with independent components
- Any scenario where human developers would divide and conquer

## Key Insight

MAWEP's power comes from its simplicity. By using only Claude Code's native Task tool, it avoids the complexity of distributed systems while achieving effective parallel coordination.

## ⚠️ Critical Understanding: Task Tool Behavior ⚠️

Many orchestrators incorrectly believe agents work in the background. Here's what ACTUALLY happens:

```
Timeline of Reality:
==================
10:00:00 | Orchestrator invokes Agent-1 with Task tool
10:00:01 | Agent-1 receives message
10:00:05 | Agent-1 sends response
10:00:05 | Agent-1 COMPLETELY STOPS ❌
10:00:05 | 
   to    | ⏸️ NOTHING IS HAPPENING ⏸️
10:00:30 | 
10:00:30 | Orchestrator invokes Agent-1 again
10:00:31 | Agent-1 receives message (no memory of before)
10:00:35 | Agent-1 sends response  
10:00:35 | Agent-1 COMPLETELY STOPS AGAIN ❌
```

**Mental Model**:
- Task tool = Sending a text message
- Agent = Person who reads it, replies once, then puts phone away
- Without new messages, they do NOTHING

This is why continuous invocation every 30-60 seconds is MANDATORY, not optional.

## Getting Started

1. Read the orchestrator prompt
2. Review example usage
3. Tell Claude Code to act as orchestrator
4. Provide GitHub issues to work on

That's it. No setup, no configuration, just prompts.