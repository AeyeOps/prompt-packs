# LMAWEP Framework Prompts

This directory contains all the specialized role prompts for the LMAWEP (Local Multi-Agent Workflow Execution Process) framework.

## Role-Based Prompts

### Core Roles

- **orchestrator-prompt.md** - Primary orchestrator instructions for coordinating multiple pods working on local tickets
- **agent-prompt.md** - Development agent work instructions for implementing tickets in isolated pods

### Review Roles (Adapted from MAWEP)

- **architect-reviewer-prompt.md** - Holistic design review for multi-pod integration
- **technical-reviewer-prompt.md** - Code quality and standards review
- **post-mortem-analyst-prompt.md** - Workflow analysis and improvement

## Key Adaptations for Local Workflow

### Orchestrator Changes
- Replaced `gh issue` commands with `./lmawep ticket` commands
- Replaced GitHub PR creation with local branch management
- Updated state tracking for local ticket system
- Modified integration process for local branches

### Agent Changes  
- Removed GitHub PR creation workflow
- Focus on local branch preparation
- Updated ticket status using LMAWEP CLI
- Adapted completion criteria for local integration

## Usage Patterns

### Starting an Orchestrator
```
I want you to act as the LMAWEP Orchestrator for parallel local development.

Project: /path/to/your-project
Tickets: 101, 102, 103, 104

Follow the orchestrator instructions from framework/prompts/orchestrator-prompt.md
```

### Invoking Agents
```
Task: Implement Ticket #101
Prompt: You are an agent working in pod-1. Follow the agent instructions from framework/prompts/agent-prompt.md.

Your assignment:
- Ticket: #101 - Add user authentication  
- Pod: ./worktrees/pod-1
- Branch: feature/101-add-auth
```

## Prompt Engineering Notes

- All prompts emphasize the ephemeral nature of Task tool invocations
- Continuous orchestration patterns are mandatory
- Local-first approach with no external dependencies
- Clear distinction between agents (ephemeral) and pods (persistent)
- Trust-building through clear branch creation and documentation

## Integration with Local Ticket System

All prompts are designed to work with the LMAWEP CLI:
- `./lmawep ticket show 101` instead of `gh issue view 101`
- `./lmawep ticket update 101 status working` for status changes
- Local branch management instead of GitHub PRs
- File-based dependency tracking

## Framework Consistency

These prompts maintain consistency with MAWEP patterns while adapting for local-only operation:
- Same 8-stage sprint process
- Same pod isolation and memory bank patterns
- Same breaking change communication protocols
- Same quality gates and completion criteria

The key difference is replacing all GitHub operations with local file and git operations.