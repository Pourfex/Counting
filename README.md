# Counting
Project to explain git workflows to students

Slides : https://docs.google.com/presentation/d/16eEDMDin6eRqLmcMi7e1N0vbOjkd0vI-u4j8MSAmP2g/edit?usp=sharing


# Git Operations

## add / commit / push

The basic workflow for adding changes to a repository.

```mermaid
sequenceDiagram
    participant WD as Working Directory
    participant SA as Staging Area
    participant LR as Local Repository
    participant RR as Remote Repository

    WD->>SA: git add <file>
    Note over SA: Files staged for commit
    SA->>LR: git commit -m "message"
    Note over LR: Commit created locally
    LR->>RR: git push origin <branch>
    Note over RR: Changes pushed to remote
```

## pull

Fetching and merging changes from remote repository.

```mermaid
sequenceDiagram
    participant RR as Remote Repository
    participant LR as Local Repository
    participant WD as Working Directory

    RR->>LR: git fetch
    Note over LR: Remote changes downloaded
    LR->>WD: git merge origin/<branch>
    Note over WD: Changes merged into working directory
    
    Note over RR,WD: git pull = git fetch + git merge
```

## rebase

Reapplying commits on top of another base tip.

```mermaid
gitGraph
    commit id: "A"
    commit id: "B"
    branch feature
    commit id: "C"
    commit id: "D"
    checkout main
    commit id: "E"
    commit id: "F"
    checkout feature
    commit id: "C'"
    commit id: "D'"
    
    Note: "After rebase: feature commits replayed on top of main"
```

Alternative view:
```mermaid
sequenceDiagram
    participant Main as Main Branch
    participant Feature as Feature Branch
    participant Temp as Temporary Storage

    Feature->>Temp: Store commits C, D
    Note over Feature: Reset to common ancestor B
    Main->>Feature: Fast-forward to latest (F)
    Temp->>Feature: Replay commits C', D'
    Note over Feature: Commits rewritten with new base
```

## cherry pick

Applying specific commits from one branch to another.

```mermaid
gitGraph
    commit id: "A"
    commit id: "B"
    branch feature
    commit id: "C"
    commit id: "D"
    commit id: "E"
    checkout main
    commit id: "F"
    cherry-pick id: "D"
    commit id: "D'"
    
    Note: "Commit D copied as D' to main branch"
```

Process flow:
```mermaid
flowchart TD
    A[Identify commit to cherry-pick] --> B[Switch to target branch]
    B --> C[git cherry-pick commit-hash]
    C --> D{Conflicts?}
    D -->|Yes| E[Resolve conflicts]
    D -->|No| F[Commit applied successfully]
    E --> G[git add resolved files]
    G --> H[git cherry-pick --continue]
    H --> F
```

## pull / merge request

Collaborative workflow for code review and integration.

```mermaid
sequenceDiagram
    participant Dev as Developer
    participant FB as Feature Branch
    participant PR as Pull Request
    participant Rev as Reviewer
    participant Main as Main Branch

    Dev->>FB: Create feature branch
    Dev->>FB: Make commits
    Dev->>PR: Create pull request
    PR->>Rev: Request review
    Rev->>PR: Review & approve/request changes
    
    alt Changes requested
        Rev->>Dev: Request modifications
        Dev->>FB: Make additional commits
        FB->>PR: Update pull request
        PR->>Rev: Re-request review
    end
    
    Rev->>PR: Approve
    PR->>Main: Merge to main branch
    Main->>FB: Delete feature branch (optional)
```

Merge strategies:
```mermaid
flowchart TD
    A[Pull Request Approved] --> B{Merge Strategy}
    B --> C[Merge Commit]
    B --> D[Squash and Merge]
    B --> E[Rebase and Merge]
    
    C --> F[Creates merge commit<br/>Preserves branch history]
    D --> G[Combines all commits into one<br/>Clean linear history]
    E --> H[Replays commits without merge commit<br/>Linear history with individual commits]
```

