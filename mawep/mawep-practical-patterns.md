# MAWEP Practical Patterns

These patterns emerged from real-world MAWEP usage and complement the architectural design. They are implementation-agnostic and work for any project type.

## Pattern: Memory Bank

**Problem**: Agents lose context between invocations
**Solution**: Persistent markdown files in worktree

```
worktree/
└── memory-bank/
    ├── activeContext.md      # Current state and decisions
    ├── technicalNotes.md     # Implementation details
    └── blockers.md           # Current impediments
```

**Benefits**:
- Context survives agent restarts
- Orchestrator can read agent state
- Historical record of decisions

**When to Update**:
- Making significant decisions
- Encountering blockers
- Before ending a work session
- After receiving breaking changes

## Pattern: Assignment Discovery

**Problem**: Agents need to find their work
**Solution**: Standardized assignment file patterns

**Discovery Order**:
1. Look for assignment files: `*assignment*.md`, `*sprint*.md`
2. Check parent directories up to project root
3. Look in `mawep-workspace/` or similar coordination directories
4. Check the issue/PR description from your Task invocation

**Assignment File Format**:
```yaml
## Agent-1 Assignment
- Issue: #123
- Title: Implement feature X
- Branch: feature/123-feature-x
- Worktree: ./worktrees/agent-1
- Dependencies: None
- Critical: No
```

## Pattern: Shared File Modification Protocol

**Problem**: Multiple agents modifying the same file cause conflicts
**Solution**: Structured modification approach

**Rules**:
1. Add changes at the TOP of relevant sections
2. Make minimal edits - don't reorganize existing code
3. Add attribution comments: `# Agent-[ID]: Added for issue #[NUM]`
4. Pull coordination branch (if exists) before major changes

**Example**:
```python
# imports section
from new_module import NewClass  # Agent-1: Added for issue #123
from existing_module import ExistingClass
```

## Pattern: Coordination Branch

**Problem**: Agents need shared interfaces/contracts
**Solution**: Dedicated git branch for coordination

**Structure**:
```
.mawep/
├── interfaces.ts       # Shared interfaces
├── breaking-changes.md # Log of breaking changes
├── agent-status.yaml   # Agent progress tracking
└── contracts/          # API contracts between modules
```

**Usage**:
1. Each agent tracks coordination branch
2. Push shared interfaces immediately
3. Pull before using shared contracts
4. Document breaking changes

## Pattern: Heartbeat Status

**Problem**: Orchestrator needs quick status checks
**Solution**: Standardized status format

```
STATUS: working|complete|blocked
PROGRESS: One-line summary
BLOCKERS: None|Specific blocker
NEXT: What you're doing next
```

**Example**:
```
STATUS: working
PROGRESS: Implemented auth service, testing edge cases
BLOCKERS: None
NEXT: Writing integration tests
```

## Pattern: Breaking Change Protocol

**Problem**: Changes affect other agents' work
**Solution**: Immediate notification pattern

**Format**:
```
BREAKING CHANGE ALERT
FILE: path/to/file
CHANGE: What changed
AFFECTS: Who needs to know
MIGRATION: How to update
```

**Example**:
```
BREAKING CHANGE ALERT
FILE: src/models/user.ts
CHANGE: Added required 'role' field to User interface
AFFECTS: Any code creating User objects
MIGRATION: Set role='user' for existing User creations
```

## Pattern: Continuous Invocation Awareness

**Problem**: Agents have no autonomy between invocations
**Solution**: Design for stateless operation

**Principles**:
- Save state frequently to memory-bank
- Make progress visible in commits
- Use clear status messages
- Assume you may be restarted at any time

## Pattern: Dependency Tracking

**Problem**: Agents blocked by incomplete dependencies
**Solution**: Explicit dependency awareness

**Check Before Starting**:
1. Review mawep-state.yaml for dependencies
2. Check if dependency PRs are merged
3. Pull latest changes if dependencies complete
4. Report blocked status if dependencies pending

## Pattern: Worktree Hygiene

**Problem**: Agents interfering with each other's work
**Solution**: Strict worktree isolation

**Rules**:
- Only work in assigned worktree
- Never modify other agents' worktrees
- Keep worktree's memory-bank updated
- Test in worktree before pushing

## Pattern: Role Self-Discovery

**Problem**: Agent doesn't know its role when spawned
**Solution**: Parse invocation context

**Discovery Steps**:
1. Check Task tool invocation message
2. Look for keywords: "orchestrat", "agent", "review"
3. Find appropriate prompt document
4. Load role-specific context

**Role Mapping**:
- "orchestrat" → Orchestrator role
- "agent" + issue → Development agent
- "review" → Reviewer role
- "architect" → Architecture reviewer
- "technical" → Technical reviewer

## Anti-Patterns to Avoid

### 1. Background Work Assumption
**Don't**: Assume agents continue working when not invoked
**Do**: Save all state, assume restart

### 2. Global File Reorganization
**Don't**: Reorganize imports/structure when adding code
**Do**: Make minimal, targeted changes

### 3. Silent Failure
**Don't**: Continue working when blocked
**Do**: Report blocked status immediately

### 4. Dependency Ignorance
**Don't**: Start work without checking dependencies
**Do**: Verify all dependencies resolved first

### 5. Context Loss
**Don't**: Keep important decisions in memory only
**Do**: Document in memory-bank immediately