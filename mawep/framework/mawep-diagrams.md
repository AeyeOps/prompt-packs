# MAWEP Framework Diagrams

## System Overview

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    GH[GitHub Issues] --> O[Orchestrator]
    O --> A1[Agent 1]
    O --> A2[Agent 2]
    O --> A3[Agent N]
    
    O -.->|creates once| P1[Pod-1]
    O -.->|creates once| P2[Pod-2]
    O -.->|creates once| P3[Pod-N]
    
    A1 -.->|works in| P1
    A2 -.->|works in| P2
    A3 -.->|works in| P3
    
    A1 & A2 & A3 --> O
```

## Agent Lifecycle

```mermaid
stateDiagram-v2
    [*] --> Idle
    Idle --> Working: Get Issue
    
    state Working {
        [*] --> Coding
        Coding --> Testing: Run tests
        Testing --> Coding: Tests fail
        Testing --> Committing: Tests pass
        Committing --> Pushing: Commit changes
        Pushing --> Coding: More work needed
        Pushing --> [*]: Issue complete
    }
    
    Working --> Idle: Complete
    Working --> Blocked: Dependency
    Blocked --> Working: Resolved
    Working --> [*]: Error
```

## Issue Assignment Flow

```mermaid
sequenceDiagram
    User->>Orchestrator: Start with issues
    Orchestrator->>GitHub: Get issues
    Orchestrator->>Orchestrator: Check if Pod-1 exists
    Note over Orchestrator: Create pod only if needed
    Orchestrator->>Agent1: Work on issue 101 in Pod-1
    
    loop Working on Issue
        Agent1->>Pod-1: Code changes
        Agent1->>Pod-1: Run tests
        Agent1->>Pod-1: Commit & push
        Agent1->>GitHub: Update PR
        Agent1->>Orchestrator: Progress update
    end
    
    Agent1->>Orchestrator: Issue complete
    Orchestrator->>Agent2: Work on issue 102 in Pod-1
```

## Dependency Graph

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    I1[Issue 1] --> I2[Issue 2]
    I1 --> I3[Issue 3]
    I1 --> I4[Issue 4]
    I4 --> I5[Issue 5]
```

## Communication

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    A[Agent] --> O[Orchestrator]
    O --> A
    
    A -.->|status update| O
    A -.->|completion| O
    A -.->|blocked| O
    O -.->|new assignment| A
```

## Architectural Analysis Phase

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    ReadyIssues[Issues Ready for Assignment] --> CountPods{2+ Pods?}
    CountPods -->|No| ProceedNormal[Proceed to Assignment]
    CountPods -->|Yes| DeepAnalysis[Architect Ultrathinking]
    
    DeepAnalysis --> ExamineScope[Examine All Issue Scopes]
    ExamineScope --> CheckInterfaces[Check for Shared Interfaces]
    CheckInterfaces --> CheckPatterns[Check for Common Patterns]
    CheckPatterns --> CheckData[Check for Data Models]
    
    CheckData --> FoundDeps{Hidden Dependencies?}
    FoundDeps -->|No| ProceedParallel[Proceed to Parallel Work]
    FoundDeps -->|Yes| DefineFoundation[Define Foundation Work]
    
    DefineFoundation --> CreateIssue[Create Foundation Issue]
    CreateIssue --> DocDetails[Document Requirements]
    DocDetails --> AssignSingle[Assign to Single Pod]
    
    AssignSingle --> BuildFoundation[Build Foundation]
    BuildFoundation --> TestFoundation[Test Thoroughly]
    TestFoundation --> CreatePR[Create Foundation PR]
    
    CreatePR --> TechReview[Technical Reviewer Persona]
    TechReview --> ReviewDecision{Review Decision}
    ReviewDecision -->|Needs Work| Feedback[Detailed Feedback]
    Feedback --> BuildFoundation
    ReviewDecision -->|Approved| MergeFoundation[Merge to Main]
    
    MergeFoundation --> UpdateDeps[Remove Dependency Block]
    UpdateDeps --> ProceedParallel
```

## Review and Learning Loop

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    AllComplete[All Pods Complete] --> GatherPRs[Gather All PRs]
    GatherPRs --> ArchThink[Architect Ultrathinking]
    ArchThink --> Holistic[Holistic Analysis]
    
    Holistic --> ForEachPR{For Each PR}
    
    ForEachPR --> ReviewPR[Review Individual PR]
    ReviewPR --> Decision{PR Decision}
    
    Decision -->|Approve| ApprovePR[Approve PR]
    Decision -->|Approve with Issue| ApproveDefer[Approve + Create Issue]
    Decision -->|Reject| RejectPR[Cancel PR]
    
    ApprovePR --> NextPR{More PRs?}
    
    ApproveDefer --> CreateBug[Create Bug/Enhancement Issue]
    CreateBug --> NextPR
    
    RejectPR --> UpdateTicket[Update Original Issue]
    UpdateTicket --> BackToBacklog[Return to Backlog]
    BackToBacklog --> NextPR
    
    NextPR -->|Yes| ForEachPR
    NextPR -->|No| MergeApproved[Merge All Approved PRs]
    
    MergeApproved --> PostMortem[Post-Mortem]
    
    PostMortem --> Analyze[Analyze Performance]
    Analyze --> Learn[Extract Learnings]
    
    Learn --> UpdateOrch[Update Orchestrator Rules]
    Learn --> UpdateAgent[Update Agent Patterns]
    Learn --> UpdateReview[Update Review Criteria]
    
    UpdateOrch & UpdateAgent & UpdateReview --> NextRound[Start Next Round]
```

## Data Flow Diagram (DFD)

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    subgraph External
        GH[(GitHub)]
        User[User]
        Config[Configuration]
    end
    
    subgraph Orchestrator
        IssueQueue[Issue Queue]
        DepGraph[Dependency Graph]
        AgentRegistry[Agent Registry]
        WorktreeMap[Worktree Mapping]
    end
    
    subgraph Agent
        IssueData[Issue Data]
        WorkDir[Working Directory]
        BranchState[Branch State]
        TestResults[Test Results]
    end
    
    subgraph Reviewer
        PRList[PR List]
        ReviewNotes[Review Notes]
        Decisions[Decisions]
    end
    
    %% User inputs
    User -->|Issue Numbers| IssueQueue
    Config -->|Settings| Orchestrator
    
    %% GitHub data flows
    GH -->|Issue Details| IssueQueue
    GH -->|Dependencies| DepGraph
    IssueQueue -->|Create Issues| GH
    Agent -->|Push Code| GH
    Agent -->|Create PR| GH
    Agent -->|Update Issue| GH
    GH -->|PR Data| PRList
    Reviewer -->|PR Actions| GH
    
    %% Orchestrator flows
    IssueQueue -->|Ready Issues| DepGraph
    DepGraph -->|Next Issue| AgentRegistry
    AgentRegistry -->|Assignment| Agent
    WorktreeMap -->|Path| Agent
    Agent -->|Status| AgentRegistry
    Agent -->|Complete| IssueQueue
    
    %% Agent internal flows
    IssueData -->|Requirements| WorkDir
    WorkDir -->|Code Changes| BranchState
    BranchState -->|Run Tests| TestResults
    TestResults -->|Pass/Fail| WorkDir
    
    %% Review flows
    PRList -->|All PRs| ReviewNotes
    ReviewNotes -->|Analysis| Decisions
    Decisions -->|Actions| GH
    Decisions -->|Learnings| Config
```

## MAWEP Workflow

```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    Start([User runs MAWEP]) --> Input[Input: Issue Numbers]
    Input --> Fetch[Fetch Issues from GitHub]
    Fetch --> Build[Build Dependency Graph]
    Build --> Check{Any Issues Ready?}
    
    Check -->|Yes| MultiPod{Multiple Pods?}
    Check -->|No| Wait[Wait for Completion]
    
    MultiPod -->|No| HasPod{Pod exists?}
    MultiPod -->|Yes| ArchAnalysis[Architect Ultrathinking]
    
    ArchAnalysis --> FoundDeps{Found Hidden Dependencies?}
    FoundDeps -->|No| HasPod
    FoundDeps -->|Yes| CreateDepIssue[Create Dependency Issue]
    
    CreateDepIssue --> SinglePod[Assign to Single Pod]
    SinglePod --> DepWork[Implement Foundation]
    DepWork --> DepTest{Tests Pass?}
    DepTest -->|No| DepWork
    DepTest -->|Yes| DepPR[Create PR]
    
    DepPR --> DepReview[Technical Review]
    DepReview --> DepApprove{Approved?}
    DepApprove -->|No| DepFeedback[Provide Feedback]
    DepFeedback --> DepWork
    DepApprove -->|Yes| DepMerge[Merge Foundation]
    
    DepMerge --> UpdateGraph[Update Dependency Graph]
    UpdateGraph --> Check
    
    HasPod -->|No| CreatePod[Create Pod]
    HasPod -->|Yes| Assign[Assign Issue to Pod]
    CreatePod --> Assign
    
    Assign --> Work[Agent Works in Pod]
    
    Work --> Code[Write/Update Code]
    Code --> Test{Tests Pass?}
    Test -->|No| Code
    Test -->|Yes| Commit[Commit Changes]
    Commit --> Push[Push to Branch]
    Push --> PR{PR Ready?}
    PR -->|No| Code
    PR -->|Yes| Update[Update GitHub Issue]
    Update --> Done{Issue Complete?}
    Done -->|No| Code
    Done -->|Yes| Complete[Mark Complete]
    
    Complete --> Check
    Wait --> Check
    
    Check -->|All Done| Review{Multiple Pods?}
    Review -->|No| End([Sprint Complete])
    Review -->|Yes| ArchReview[Architect Reviews All PRs]
    
    ArchReview --> Holistic[Holistic Analysis]
    Holistic --> Approve{Approve All?}
    
    Approve -->|Yes| Merge[Merge PRs]
    Approve -->|No| Reject[Reject/Request Changes]
    
    Reject --> CreateIssues[Create Follow-up Issues]
    CreateIssues --> End
    
    Merge --> Conflicts{Merge Conflicts?}
    Conflicts -->|Yes| Resolve[Resolve Conflicts]
    Conflicts -->|No| PostMortem[Post-Mortem Analysis]
    Resolve --> PostMortem
    
    PostMortem --> Improve[Update Guidance]
    Improve --> Next([Next Round])
    Next --> Fetch
```