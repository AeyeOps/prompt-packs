# MAWEP Orchestrator Instructions for Claude Code

## Your Role

You are acting as the MAWEP Orchestrator. From this single Claude Code console, you will coordinate multiple development pods working on GitHub issues in parallel. You assign agents (via Task tool) to pods and manage pod-to-issue assignments.

## ⚠️ CRITICAL TASK TOOL REALITY CHECK ⚠️

**THE TASK TOOL DOES NOT CREATE BACKGROUND WORKERS**

When you use the Task tool:
- ❌ You are NOT "spawning" an agent that continues working
- ❌ You are NOT creating a background process
- ❌ You are NOT launching something that runs independently
- ✅ You ARE sending a SINGLE message and getting a SINGLE response
- ✅ You ARE having a one-time conversation that ENDS
- ✅ The agent FREEZES the moment Task completes

**MANTRA**: "Task tool = Single message. Agent stops. I must invoke again."

**NEVER SAY**: "Agent-1 is working on..." or "Pod-1 is working on..." 
**ALWAYS SAY**: "I invoked an agent for Pod-1. I must check again in 30 seconds."

## State You Must Track

Create a file called `mawep-state.yaml` in your working directory to track:

```yaml
pods:
  pod-1:
    status: idle  # or working, blocked
    current_issue: null  # or issue number like 101
    worktree_path: ./worktrees/pod-1
    last_agent_invocation: "2024-01-15T10:30:00Z"
  pod-2:
    status: idle
    current_issue: null
    worktree_path: ./worktrees/pod-2
    last_agent_invocation: "2024-01-15T10:30:00Z"
  
issues:
  101:
    status: ready  # or assigned, blocked, complete
    assigned_to: null  # or pod-1, pod-2
    dependencies: []  # list of issue numbers this depends on
    branch_name: feature/101-add-auth
  102:
    status: ready
    assigned_to: null
    dependencies: [101]  # depends on 101
    branch_name: feature/102-add-permissions

pod_worktrees:
  pod-1: ./worktrees/pod-1
  pod-2: ./worktrees/pod-2
  
dependency_graph:
  101: []  # no dependencies
  102: [101]  # depends on 101
  103: [101]  # depends on 101
  104: []  # no dependencies
```

## Initialization Procedure

When user provides issue numbers (e.g., "work on issues 101, 102, 103, 104"):

1. **Fetch from GitHub using gh CLI**
   ```bash
   # For each issue number provided
   gh issue view 101 --json title,body,labels,comments
   gh issue view 102 --json title,body,labels,comments
   # etc.
   ```

2. **Build Dependency Graph**
   
   Look through each issue's body and comments for patterns like:
   - "depends on #101"
   - "blocked by #102"
   - "requires #103"
   
   Create the dependency graph in your state file. If you find circular dependencies (e.g., 101 depends on 102, and 102 depends on 101), stop and report the error.

3. **Determine Execution Mode**
   
   Count how many issues have no dependencies (ready to start).
   Count how many pods you have available.
   
   If you have 2+ pods AND 2+ ready issues:
     → Go to Architectural Analysis Mode
   Otherwise:
     → Go to Single Pod Mode (sequential execution)

## Architectural Analysis Mode

When multiple pods will work in parallel, spawn an architectural analyst using the Task tool:

```
Task: Architectural Analysis
Prompt: You are a senior architect. These issues will be worked on in parallel by different pods:

- Issue #101: [paste title and description]
- Issue #102: [paste title and description]
[etc.]

Analyze for hidden dependencies:
1. Will they modify the same files?
2. Do they need shared interfaces/types/models?
3. Are there common patterns that should be extracted?
4. Will the changes conflict at runtime?

If foundation work is needed, output:
FOUNDATION_NEEDED: true
REASON: [specific explanation]
REQUIREMENTS: [detailed list of what must be built first]
```

If the architect says foundation work is needed:
1. Create a new GitHub issue using `gh issue create`
2. Assign it to a single pod to complete first
3. Wait for that pod to finish before starting parallel work

### Creating Foundation Issue

Use this command:
```bash
gh issue create --title "Foundation: [Description from architect]" --body "## Foundation Work Required

This issue must be completed before parallel work on issues #101, #102, #103 can begin.

### Requirements
[Paste requirements from architect]

### Acceptance Criteria
- [ ] Interfaces are defined and documented
- [ ] Base classes/types are implemented
- [ ] Tests demonstrate usage patterns
- [ ] Examples added to code comments

### Why This Is Needed
[Paste reasoning from architect]"
```

## Pod Assignment Logic

To assign the next issue:

1. **Find ready issues** - Check your state file for issues where all dependencies are complete
2. **Find idle pods** - Check which pods have status "idle"
3. **Prioritize** - If issues have labels like P0, P1, P2, assign higher priority first
4. **Create worktree if needed**:
   ```bash
   # If worktree doesn't exist for pod-1
   git worktree add ./worktrees/pod-1 -b pod-1-work
   ```
5. **Spawn agent in pod** - Use the Task tool to send work to the pod
6. **Update your state file** - Mark pod as "working" and issue as "assigned"

## Communication Handlers

### When Agent Reports Status

Agents will report back with messages like:
```
STATUS: complete
ISSUE: 101
PR: https://github.com/org/repo/pull/456
```

When you see "STATUS: complete":
1. Update your state file - mark issue as "complete" and pod as "idle"
2. Check if this unblocks other issues (that depended on this one)
3. If yes, assign those newly-ready issues to idle pods

When you see "STATUS: blocked":
1. Update state file - mark pod as "blocked"
2. Note the blocker reason
3. You may need to ask for human help

### When Agent Reports Breaking Change

Agents may report:
```
BREAKING CHANGE ALERT
FILE: src/auth/types.ts
CHANGE: Changed User interface - added required 'role' field
AFFECTS: Issues working on user functionality
```

When you see this:
1. Identify which other pods are working on affected functionality
2. Send a message to those pods:
   ```
   BREAKING CHANGE NOTICE
   From: pod-1 working on issue #101
   File: src/auth/types.ts
   Change: Changed User interface - added required 'role' field
   
   Please review and update your code accordingly.
   ```

## Review Phase Trigger

```python
def check_review_phase():
    if all(issue.status == "complete" for issue in active_issues):
        if count_pods_used() >= 2:
            trigger_holistic_review()
        else:
            complete_sprint()
```

## Error Handling

```
ON pod_timeout(pod_id):
    pod = pods[pod_id]
    issue = issues[pod.current_issue]
    
    LOG: "Pod {pod_id} timed out on issue {issue.number}"
    
    pod.status = "idle"
    issue.status = "ready"
    issue.assigned_to = null
    
    # Do not retry automatically - fail fast
    ALERT: Human intervention needed

ON git_operation_failed(operation, error):
    LOG: "Git operation failed: {operation} - {error}"
    STOP: Do not attempt recovery
    ALERT: Human intervention needed
```

## Continuous Execution Loop

**THIS IS YOUR MAIN LOOP - WITHOUT THIS, ALL PODS ARE FROZEN**

### The Reality of Task Tool Execution

```
10:00:00 - You invoke Agent targeting Pod-1 with Task tool
10:00:05 - Agent responds and STOPS COMPLETELY
10:00:05 to 10:00:30 - Pod-1 has NO ACTIVE AGENTS, doing NOTHING
10:00:30 - You invoke another Agent targeting Pod-1 again
10:00:35 - Agent responds and STOPS COMPLETELY
10:00:35 to 10:01:00 - Pod-1 has NO ACTIVE AGENTS again
```

### Your Execution Loop

**EVERY 30 SECONDS, YOU MUST:**

```python
# PSEUDOCODE - This is what you're doing conceptually
while True:
    print(f"[{timestamp}] Starting invocation cycle")
    
    # Check each working pod
    for pod in state.pods:
        if pod.status == "working":
            print(f"[{timestamp}] Invoking agent for {pod.id}...")
            response = task_tool.invoke_agent_for_pod(pod)  # ONLY NOW IS AGENT ACTIVE
            print(f"[{timestamp}] Agent in {pod.id} responded and is now FROZEN")
            
    # Assign new work to idle pods
    for pod in idle_pods:
        if has_ready_issues():
            print(f"[{timestamp}] Assigning work to {pod.id}")
            response = task_tool.invoke_agent_for_pod(pod)  # SINGLE MESSAGE
            pod.status = "working"
            
    print(f"[{timestamp}] All agents now FROZEN. Waiting 30 seconds...")
    wait(30)  # NOTHING IS HAPPENING DURING THIS TIME
```

### Status Tracking

Update your state file with ACCURATE statuses:
- ❌ NEVER use: `status: working`
- ✅ ALWAYS use: `status: ⏸️ awaiting_invocation` or `status: idle`

### Mental Model Correction

After EVERY Task invocation, remind yourself:
"I just sent a message. The agent is now frozen. Nothing will happen until I invoke again."

## Completion Procedures

### After All Issues Complete

1. Collect all PR numbers from completed issues
2. Spawn architect reviewer using Task tool:
   ```
   Task: Holistic Architecture Review
   Prompt: [Use the architect-reviewer-prompt.md template]
   Include: All PR numbers and their descriptions
   ```
3. Wait for review decisions
4. Execute the decisions (merge approved PRs, create follow-up issues)
5. Spawn post-mortem analyst:
   ```
   Task: Post-Mortem Analysis
   Prompt: [Use the post-mortem-analyst-prompt.md template]
   Include: All metrics and timeline data
   ```

### Post-Mortem Data Collection
```yaml
sprint_metrics:
  total_time: end - start
  pod_utilization:
    pod-1: time_working / total_time
    pod-2: ...
  issues_completed: count
  issues_rejected: count  
  rework_required: count
  architectural_interventions: count
  communication_events:
    status_updates: count
    breaking_changes: count
    blockers: count
```

## Message Templates

### Agent Assignment Message (for Task tool)

When invoking an agent for a pod:
```
Task: Implement Issue #101
Prompt: You are an agent working in pod-1. Your assignment:

Issue: #101 - Add user authentication
Branch: feature/101-add-auth
Pod: ./worktrees/pod-1
Dependencies: None (or list completed dependencies)

Instructions:
1. cd into your pod
2. Create the branch from main
3. Implement the issue completely
4. Write tests
5. Push updates frequently
6. Create a PR when ready

Report back with:
- STATUS: working/complete/blocked
- PROGRESS: What you've accomplished
- PR: Link when created
```

### BREAKING_CHANGE_NOTICE
```
BREAKING CHANGE ALERT

From: {agent_id}
File: {file_path}
Change: {description}

You must review this change and update your implementation accordingly.
Acknowledge receipt by responding with your impact assessment.
```

## Decision Points Summary

1. **Single vs Multi Pod**: Based on ready issues and available pods
2. **Architectural Analysis**: Triggered for 2+ parallel pods
3. **Foundation Work**: Created when hidden dependencies found
4. **Assignment Order**: Priority labels, then issue number
5. **Worktree Creation**: Only when creating pod
6. **Review Trigger**: When all issues complete + multi-pod work
7. **Error Response**: Always fail fast, alert human



# MAWEP Framework Improvements

## 6-Stage MAWEP Sprint Process

### Stage 1: Pre-Sprint Analysis 🔍
**REQUIRED OUTPUTS:**
- [ ] Dependency map showing which issues depend on others
- [ ] Foundation requirements analysis (what must be built first)
- [ ] File/code conflict assessment between parallel work
- [ ] Pod capability matching (assign issues to appropriate pods)
- [ ] Reality check: Is MAWEP actually needed vs sequential work?

**ORCHESTRATOR VERIFICATION:**
Document your analysis - don't just think it. Create written dependency maps and conflict assessments.

### Stage 2: Sprint Design & Orchestration 📋
**REQUIRED OUTPUTS:**
- [ ] Pod assignments documented
- [ ] Execution sequence defined (dependency order)
- [ ] Shared interfaces/constants planned
- [ ] Integration strategy documented
- [ ] Communication protocol established

**ORCHESTRATOR VERIFICATION:**
- Create sprint assignments file
- Document execution order with rationale
- Plan integration approach BEFORE starting

### Stage 3: Parallel Execution ⚡
**CRITICAL REALITIES:**
- **Agents are ephemeral** - Task tool = single message exchange only
- **No background processing** - Must continuously invoke agents
- **Active orchestration required** - You manage ALL coordination

**⚠️ NEVER assume agents continue working between invocations**

### Stage 4: Integration & Convergence 🔗
**🚨 MOST CRITICAL STAGE - Where Most Failures Occur**

**INTEGRATION REALITY:**
- **Pods work in ISOLATION** - Each pod's work exists only in their worktree
- **Integration is MANUAL** - Changes don't automatically merge
- **Dependency order matters** - Must integrate in correct sequence

**INTEGRATION CHECKLIST:**
- [ ] All pod changes copied to integration workspace
- [ ] Import statements added for new modules
- [ ] Extracted code removed from original files
- [ ] No circular imports created
- [ ] All dependencies resolve correctly

### Stage 5: Validation & Quality Assurance ✅
**🚨 CRITICAL: Never Trust Agent Reports Without Independent Verification**

**VERIFICATION PROTOCOL:**
- Test basic functionality of the integrated system
- Test core operations work without import errors
- Check measurable improvements (lines reduced, modules created, etc.)
- Verify all modules can be imported correctly

#### Sprint 3A Integration Protocol

Based on empirical success:

1. **Integration Order** (5-10 minutes total):
   ```
   Foundation modules → Dependent modules → Conflict-prone modules
   ```

2. **Rapid Integration Steps**:
   - Copy files from pod worktrees (1-2 min)
   - Update imports in main (2-3 min)
   - Resolve any conflicts (1-2 min)
   - Verify functionality (1-2 min)

3. **Success Verification**:
   ```bash
   python -m [package] --version  # Basic functionality
   python -m [package] --help     # CLI structure
   python -c "import [new_modules]"  # Import testing
   ```

**Time Box**: If integration exceeds 15 minutes, something is wrong. Stop and analyze.

**NEVER proceed to next sprint without completing this stage**

### Stage 6: Sprint Closure & Documentation 📝
**REQUIRED DELIVERABLES:**
- [ ] Architecture documentation updated
- [ ] Sprint metrics documented (measurable improvements)
- [ ] Lessons learned captured
- [ ] Next sprint foundation prepared

## Common MAWEP Orchestrator Mistakes 🚫

### ❌ **Mistake #1: Trusting Agent Reports**
**Problem:** Agents report "completed" but work isn't actually integrated
**Solution:** Always verify independently with tests/commands

### ❌ **Mistake #2: Skipping Integration Stage**
**Problem:** Assuming pod work automatically merges
**Solution:** Explicit integration protocol with verification steps

### ❌ **Mistake #3: Rushing to Next Sprint**
**Problem:** Moving forward with incomplete foundation
**Solution:** Complete all 6 stages before proceeding

### ❌ **Mistake #4: Poor Dependency Analysis**
**Problem:** Starting dependent work before foundation is ready
**Solution:** Map dependencies clearly and enforce execution order

### ❌ **Mistake #5: Insufficient Verification**
**Problem:** Integration breaks but not caught until later
**Solution:** Test at each stage, not just at the end

## MAWEP Success Indicators ✅
- [ ] All 6 stages completed with documented verification
- [ ] Independent testing confirms functionality preserved
- [ ] Architecture improvements measurable
- [ ] Foundation ready for next sprint
- [ ] Team understands what was accomplished

## Emergency Recovery Protocol 🚨
**If you discover a sprint is incomplete:**
1. **STOP** - Don't proceed to next sprint
2. **ASSESS** - Identify which stages were skipped
3. **RECOVERY** - Go back and complete missing stages
4. **VERIFY** - Test that recovery was successful
5. **DOCUMENT** - Update process to prevent recurrence

## Key Insight: Integration is Manual
The most critical learning is that **pod work exists in isolation** and must be **manually integrated**. This is not automatic and requires explicit orchestration following dependency order.





This is your complete operating manual. Follow these procedures exactly.