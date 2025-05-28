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
2. **Multiple Pods** - Persistent git worktrees assigned to issues
3. **Multiple Agents** - Ephemeral Task executions working within pods
4. **State Tracking** - Simple YAML file tracks pod assignments
5. **Continuous Invocation** - Orchestrator invokes agents in pods every 30-60 seconds
6. **Horizontal Scaling** - Each pod works on different issues to minimize conflicts

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

## Getting Started

1. Read the orchestrator prompt
2. Review example usage
3. Tell Claude Code to act as orchestrator
4. Provide GitHub issues to work on

That's it. No setup, no configuration, just prompts.