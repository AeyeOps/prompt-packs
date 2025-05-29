# LMAWEP Local Ticket System

## Overview

LMAWEP uses a lightweight, file-based ticket tracking system to replace GitHub issues. This system provides robust ticket management while remaining completely local and requiring no external dependencies.

## Design Principles

- **File-based**: All tickets stored as YAML files for easy version control
- **Human-readable**: YAML format is both machine and human parseable  
- **Lightweight**: No database or external services required
- **Git-friendly**: All ticket files can be committed and tracked
- **Robust**: Dependency tracking, status management, and history

## Directory Structure

```
.lmawep/
├── config.yaml                 # LMAWEP configuration
├── tickets/                    # Individual ticket files
│   ├── ticket-001.yaml
│   ├── ticket-002.yaml
│   └── ticket-003.yaml
├── templates/                  # Ticket templates
│   ├── feature.yaml
│   ├── bug.yaml
│   └── task.yaml
├── sequences/                  # ID generation
│   └── ticket-sequence.txt     # Next ticket ID
└── history/                    # Ticket change history
    ├── ticket-001.log
    └── ticket-002.log
```

## Ticket Format

### Basic Ticket Structure

```yaml
# .lmawep/tickets/ticket-001.yaml
id: 1
title: "Add user authentication system"
description: |
  Implement JWT-based authentication for the application.
  
  ## Acceptance Criteria
  - [ ] Login endpoint with email/password
  - [ ] JWT token generation and validation
  - [ ] Protected route middleware
  - [ ] Session management
  - [ ] Logout functionality
  
  ## Additional Notes
  Follow existing security patterns in the codebase.

type: feature          # feature, bug, task, chore, docs
status: ready          # ready, assigned, working, blocked, review, complete
priority: high         # low, medium, high, critical
estimate: "4h"         # Human-readable estimate
assigned_to: null      # null or pod-1, pod-2, etc.
dependencies: []       # List of ticket IDs this depends on
blocks: []            # List of ticket IDs that depend on this
labels:               # Optional categorization
  - authentication
  - security
  - backend

# Metadata
created_by: "orchestrator"
created_at: "2024-01-15T10:30:00Z"
updated_at: "2024-01-15T10:30:00Z"
assignee_notes: ""    # Notes from the agent working on this

# Tracking
time_estimates:
  initial: "4h"
  revised: null
actual_time: null
blockers:             # Current blocking issues
  - type: dependency
    description: "Waiting for database schema changes"
    created_at: "2024-01-15T11:00:00Z"

# Integration
branch_name: null     # Set when work starts: pod-1-ticket-001
worktree_path: null   # Set when assigned: ./lmawep-workspace/worktrees/pod-1
pod_memory_bank: null # Path to pod's memory bank

# Completion
completed_at: null
integration_notes: ""
```

### Status Lifecycle

```
ready → assigned → working → review → complete
  ↓         ↓         ↓        ↓
blocked   blocked   blocked  blocked
  ↓         ↓         ↓        ↓
ready    assigned   working  review
```

**Status Definitions:**
- `ready`: Available for assignment, all dependencies met
- `assigned`: Assigned to a pod but work not yet started
- `working`: Active development in progress
- `blocked`: Cannot proceed due to external blocker
- `review`: Work complete, pending review/integration
- `complete`: Fully implemented and integrated

### Dependency Types

```yaml
dependencies:
  - id: 2
    type: hard        # Must be complete before this can start
    reason: "Needs database schema from ticket 2"
  - id: 3  
    type: soft        # Preferred to be complete but not required
    reason: "Could benefit from logging framework"
  - id: 4
    type: interface   # Needs shared interface definition
    reason: "Shares User model with ticket 4"
```

## CLI Interface

### Basic Commands

```bash
# Initialize LMAWEP in current directory
./lmawep init

# Create new ticket
./lmawep ticket create "Add user authentication" --type feature --priority high

# List tickets
./lmawep ticket list
./lmawep ticket list --status ready
./lmawep ticket list --assigned-to pod-1

# Show ticket details
./lmawep ticket show 1
./lmawep ticket show 1 --format yaml

# Update ticket
./lmawep ticket update 1 --status working
./lmawep ticket update 1 --assigned-to pod-1
./lmawep ticket update 1 --add-blocker "Waiting for API documentation"

# Add dependencies
./lmawep ticket depend 3 1    # Ticket 3 depends on ticket 1
./lmawep ticket undepend 3 1  # Remove dependency

# Search and filter
./lmawep ticket search "authentication"
./lmawep ticket list --priority high --status ready
```

### Advanced Commands

```bash
# Dependency analysis
./lmawep ticket deps-graph          # Show dependency tree
./lmawep ticket deps-ready          # Show tickets ready to start
./lmawep ticket deps-blocked        # Show dependency-blocked tickets

# Pod management integration
./lmawep pod assign 1 pod-1         # Assign ticket 1 to pod-1
./lmawep pod unassign 1             # Unassign ticket from pod
./lmawep pod list                   # Show all pod assignments

# Sprint planning
./lmawep sprint create "Sprint 1" 1 2 3 4    # Create sprint with tickets
./lmawep sprint status                       # Show sprint progress
./lmawep sprint complete                     # Mark sprint complete

# History and reporting
./lmawep ticket history 1           # Show change history for ticket 1
./lmawep report time                # Time tracking report
./lmawep report completion          # Completion rate statistics
```

## Implementation Strategy

### Core CLI Script

Create `./lmawep` executable script:

```bash
#!/bin/bash
# LMAWEP - Local Multi-Agent Workflow Execution Process CLI

LMAWEP_DIR=".lmawep"
TICKETS_DIR="$LMAWEP_DIR/tickets"
CONFIG_FILE="$LMAWEP_DIR/config.yaml"

# Ensure LMAWEP is initialized
check_init() {
    if [ ! -d "$LMAWEP_DIR" ]; then
        echo "Error: LMAWEP not initialized. Run './lmawep init' first."
        exit 1
    fi
}

# Get next ticket ID
next_ticket_id() {
    local seq_file="$LMAWEP_DIR/sequences/ticket-sequence.txt"
    if [ ! -f "$seq_file" ]; then
        echo "1" > "$seq_file"
        echo "1"
    else
        local next_id=$(($(cat "$seq_file") + 1))
        echo "$next_id" > "$seq_file"
        echo "$next_id"
    fi
}

# Main command dispatcher
case "$1" in
    "init")
        # Initialize LMAWEP structure
        ;;
    "ticket")
        # Ticket management commands
        ;;
    "pod")
        # Pod management commands
        ;;
    "sprint")
        # Sprint management commands
        ;;
    *)
        echo "Usage: ./lmawep {init|ticket|pod|sprint} [options]"
        exit 1
        ;;
esac
```

### Python Implementation Alternative

For more robust functionality, a Python implementation could provide:

```python
#!/usr/bin/env python3
"""
LMAWEP CLI - Local Multi-Agent Workflow Execution Process
"""

import yaml
import click
from pathlib import Path
from datetime import datetime
from typing import List, Dict, Optional

class LMAWEPTicketSystem:
    def __init__(self, project_root: Path = None):
        self.root = project_root or Path.cwd()
        self.lmawep_dir = self.root / ".lmawep"
        self.tickets_dir = self.lmawep_dir / "tickets"
        self.config_file = self.lmawep_dir / "config.yaml"
        
    def init(self):
        """Initialize LMAWEP directory structure"""
        self.lmawep_dir.mkdir(exist_ok=True)
        self.tickets_dir.mkdir(exist_ok=True)
        # Create other directories and default files
        
    def create_ticket(self, title: str, description: str = "", 
                     ticket_type: str = "task", priority: str = "medium") -> int:
        """Create a new ticket"""
        ticket_id = self._next_ticket_id()
        ticket = {
            'id': ticket_id,
            'title': title,
            'description': description,
            'type': ticket_type,
            'status': 'ready',
            'priority': priority,
            'created_at': datetime.now().isoformat(),
            # ... other fields
        }
        
        ticket_file = self.tickets_dir / f"ticket-{ticket_id:03d}.yaml"
        with open(ticket_file, 'w') as f:
            yaml.dump(ticket, f, default_flow_style=False)
            
        return ticket_id

@click.group()
def cli():
    """LMAWEP - Local Multi-Agent Workflow Execution Process"""
    pass

@cli.command()
def init():
    """Initialize LMAWEP in current directory"""
    system = LMAWEPTicketSystem()
    system.init()
    click.echo("LMAWEP initialized successfully")

@cli.group()
def ticket():
    """Ticket management commands"""
    pass

@ticket.command()
@click.argument('title')
@click.option('--type', default='task', help='Ticket type')
@click.option('--priority', default='medium', help='Priority level')
def create(title, type, priority):
    """Create a new ticket"""
    system = LMAWEPTicketSystem()
    ticket_id = system.create_ticket(title, ticket_type=type, priority=priority)
    click.echo(f"Created ticket {ticket_id}: {title}")

if __name__ == "__main__":
    cli()
```

## Integration with LMAWEP Workflow

### Orchestrator Integration

The orchestrator reads tickets instead of GitHub issues:

```python
# Instead of: gh issue view 101
# Use: ./lmawep ticket show 101 --format yaml

def load_ticket(ticket_id: int):
    result = subprocess.run(['./lmawep', 'ticket', 'show', str(ticket_id), '--format', 'yaml'], 
                          capture_output=True, text=True)
    return yaml.safe_load(result.stdout)

def get_ready_tickets():
    result = subprocess.run(['./lmawep', 'ticket', 'list', '--status', 'ready', '--format', 'yaml'],
                          capture_output=True, text=True)
    return yaml.safe_load(result.stdout)
```

### Agent Integration

Agents work with local tickets:

```python
# Instead of: gh pr create
# Use: Local branch management + ticket updates

def complete_ticket(ticket_id: int, branch_name: str):
    # Update ticket status
    subprocess.run(['./lmawep', 'ticket', 'update', str(ticket_id), '--status', 'review'])
    
    # Add completion notes
    subprocess.run(['./lmawep', 'ticket', 'update', str(ticket_id), 
                   '--add-note', f'Completed on branch {branch_name}'])
```

## Benefits of Local Ticket System

1. **No External Dependencies**: Works completely offline
2. **Version Controlled**: All tickets tracked in git
3. **Human Readable**: YAML format is easy to read and edit
4. **Lightweight**: No database or server required
5. **Flexible**: Easy to extend and customize
6. **Robust**: Dependency tracking and status management
7. **Integrated**: Seamlessly works with LMAWEP workflow

## Migration from GitHub Issues

For projects migrating from GitHub issues to LMAWEP:

```bash
# Export existing GitHub issues to LMAWEP format
./lmawep import github-issues --repo org/project --milestone "Sprint 1"

# Convert GitHub issue numbers to LMAWEP ticket IDs
./lmawep import map-ids github-issues.json
```

This local ticket system provides the same functionality as GitHub issues while maintaining complete local control and no external dependencies.