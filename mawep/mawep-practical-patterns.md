# MAWEP Practical Patterns

**🎭 REALITY CHECK**: Looking for "best practices" to copy-paste? Stop. These patterns only work if you understand WHY they exist.

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

## Pattern: Pod Assignment Discovery

**Problem**: Agents need to find which pod they're working in
**Solution**: Standardized assignment file patterns

**Discovery Order**:
1. Look for assignment files: `*assignment*.md`, `*sprint*.md`
2. Check parent directories up to project root
3. Look in `mawep-workspace/` or similar coordination directories
4. Check the issue/PR description from your Task invocation

**Assignment File Format**:
```yaml
## Pod-1 Assignment
- Issue: #123
- Title: Implement feature X
- Branch: feature/123-feature-x
- Worktree: ./worktrees/pod-1
- Dependencies: None
- Critical: No
```

## Pattern: Shared File Modification Protocol

**Problem**: Multiple pods modifying the same file cause conflicts
**Solution**: Structured modification approach

**Rules**:
1. Add changes at the TOP of relevant sections
2. Make minimal edits - don't reorganize existing code
3. Add attribution comments: `# Pod-[ID]: Added for issue #[NUM]`
4. Pull coordination branch (if exists) before major changes

**Example**:
```python
# imports section
from new_module import NewClass  # Pod-1: Added for issue #123
from existing_module import ExistingClass
```

## Pattern: Coordination Branch

**Problem**: Pods need shared interfaces/contracts
**Solution**: Dedicated git branch for coordination

**Structure**:
```
.mawep/
├── interfaces.ts       # Shared interfaces
├── breaking-changes.md # Log of breaking changes
├── pod-status.yaml     # Pod progress tracking
└── contracts/          # API contracts between modules
```

**Usage**:
1. Each pod tracks coordination branch
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

**Problem**: Changes affect other pods' work
**Solution**: Immediate notification pattern

**Format**:
```
BREAKING CHANGE ALERT
POD: pod-id
FILE: path/to/file
CHANGE: What changed
AFFECTS: Who needs to know
MIGRATION: How to update
```

**Example**:
```
BREAKING CHANGE ALERT
POD: pod-1
FILE: src/models/user.ts
CHANGE: Added required 'role' field to User interface
AFFECTS: Other pods creating User objects
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

### Pattern: Reality-Based Sprint Planning

**🎭 REALITY CHECK**: Tempted to claim "I'll do this 20x faster than humans"? That's performance mode. Real multiplier is 10-15x, not infinity.

**Context**: Traditional estimates assume human development speed.

**Pattern**: 
1. Estimate as if human developer
2. Divide by 10-15 for AI execution time
3. Add 10% buffer for integration/verification
4. Set invocation intervals at 30-60 seconds

**Example**:
- Human estimate: 6 hours for 4 modules
- AI reality: 6 hours ÷ 12 = 30 minutes
- With buffer: 33 minutes total
- Actual Sprint 3A: 31 minutes ✅

### Pattern: Standalone Module Design

**Context**: Pods cannot import from each other during execution.

**Pattern**:
1. Each pod creates self-contained modules
2. Shared interfaces defined minimally upfront
3. Dependencies resolved during integration only
4. No cross-pod imports during development

**Anti-pattern**: Pod-4 trying to import Pod-1's exceptions
**Solution**: Pod-4 creates own base exceptions, reconciled during integration


## Pattern: Dependency Tracking

**Problem**: Pods blocked by incomplete dependencies
**Solution**: Explicit dependency awareness

**Check Before Starting**:
1. Review mawep-state.yaml for dependencies
2. Check if dependency PRs are merged
3. Pull latest changes if dependencies complete
4. Report blocked status if dependencies pending

## Pattern: Worktree Hygiene

**Problem**: Pods interfering with each other's work
**Solution**: Strict worktree isolation

**Rules**:
- Only work in assigned worktree
- Never modify other pods' worktrees
- Keep worktree's memory-bank updated
- Test in worktree before pushing

## Pattern: Role Self-Discovery

**Problem**: Agent doesn't know which pod it's working in when spawned
**Solution**: Parse invocation context

**Discovery Steps**:
1. Check Task tool invocation message
2. Look for pod assignment: "pod-1", "pod-2", etc.
3. Find worktree path and issue number
4. Load pod-specific context from memory-bank

**Role Mapping**:
- "orchestrat" → Orchestrator role
- "agent" + pod → Development agent in specific pod
- "review" → Reviewer role
- "architect" → Architecture reviewer
- "technical" → Technical reviewer

## Anti-Patterns to Avoid

### 1. Background Work Assumption
**🎭 REALITY CHECK**: Thinking "the agent is still working on it"? No. Agents are frozen between invocations.
**Don't**: Assume agents continue working when not invoked
**Do**: Save all state, assume restart

### 2. Global File Reorganization
**🎭 REALITY CHECK**: Urge to "clean up" the whole file? That's performance mode, not helping.
**Don't**: Reorganize imports/structure when adding code
**Do**: Make minimal, targeted changes

### 3. Silent Failure
**🎭 REALITY CHECK**: Trying to "figure it out" instead of reporting blocked? Stop. Fail fast.
**Don't**: Continue working when blocked
**Do**: Report blocked status immediately

### 4. Dependency Ignorance
**🎭 REALITY CHECK**: Starting work hoping dependencies "will be done soon"? That's wishful thinking.
**Don't**: Start work without checking dependencies
**Do**: Verify all dependencies resolved first

### 5. Context Loss
**🎭 REALITY CHECK**: Keeping decisions "in your head"? You have no head. Document or lose it.
**Don't**: Keep important decisions in memory only
**Do**: Document in memory-bank immediately

### 6. Performance Theater [NEW]
**🎭 REALITY CHECK**: About to claim "completed in record time"? Where are the PR URLs?
**Don't**: Report completion without concrete evidence
**Do**: Show PR URLs, test results, integration proof