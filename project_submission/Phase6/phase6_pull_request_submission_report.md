# Phase 6 Pull Request Submission Report

## 1. Phase 6 Requirement

The assignment requires:

- A fork of the original repository.
- A feature or refactoring branch.
- The improvement applied and pushed to that branch.
- A pull request submitted to the original repository.
- A clear pull request title.
- A description explaining the addressed issue, why the change improves the system, the
  refactoring approach, and assurance that functionality remains unchanged.

All required submission steps have been completed.

## 2. Repository and Pull Request Information

| Item | Evidence |
|---|---|
| GitHub account | <https://github.com/Avraaaa> |
| Original repository | <https://github.com/jgrapht/jgrapht> |
| Forked repository | <https://github.com/Avraaaa/jgrapht> |
| Fork relationship | GitHub identifies `Avraaaa/jgrapht` as a fork of `jgrapht/jgrapht` |
| Refactoring branch | `refactor-boykov-kolmogorov-adopt` |
| Target branch | `jgrapht:master` |
| Pull request | <https://github.com/jgrapht/jgrapht/pull/1355> |
| Pull request number | `#1355` |
| Pull request title | `Refactor Boykov-Kolmogorov orphan adoption` |
| Commit | `6183e894718be159d82a2182a72ca3bfb08b2a90` |
| Files changed | 1 production file |
| Diff size | 152 additions and 105 deletions |

The local Git configuration also confirms:

```text
origin   https://github.com/Avraaaa/jgrapht.git
upstream https://github.com/jgrapht/jgrapht
```

The local branch tracks the pushed fork branch:

```text
refactor-boykov-kolmogorov-adopt
  -> origin/refactor-boykov-kolmogorov-adopt
```

## 3. Pull Request Status

GitHub was checked on June 25, 2026.

| Status item | Result |
|---|---|
| State | Open |
| Draft | No |
| Merge status | Not merged |
| Mergeability | Clean |
| Commits | 1 |
| Changed files | 1 |
| Reviews | None submitted yet |
| Discussion comments | None after the opening description |

The pull request was created on June 18, 2026 at 19:45:47 UTC. An open or unmerged state does not
mean Phase 6 is incomplete: the assignment requires submission of a pull request, not acceptance by
the upstream maintainers.

## 4. Verification Against the Instructor's PR Requirements

| Instructor requirement | Evidence in PR #1355 | Result |
|---|---|---|
| Clear title describing the improvement | `Refactor Boykov-Kolmogorov orphan adoption` | Satisfied |
| Explain what issue was addressed | Identifies cognitive complexity 112, Brain Method, and repeated source/sink adoption structure | Satisfied |
| Explain why the change improves the system | States that the large adoption logic is split into smaller private steps while retaining explicit directional cases | Satisfied |
| Summarize the refactoring approach | Lists extracted orphan selection, directional orphan handling, and directional parent search | Satisfied |
| Assure unchanged functionality | States that public API and algorithmic behavior are unchanged and identifies preserved invariants | Satisfied |
| Mention validation | Lists focused core tests, complete core tests, and aggregate Javadocs | Satisfied |

The submitted description explicitly records that the refactor preserves:

- Orphan ordering.
- Source-tree and sink-tree edge orientation.
- Timestamp updates.
- Debug output.
- Public API behavior.
- Algorithmic behavior.

## 5. Continuous Integration Evidence

GitHub Actions ran the JGraphT pull request build on all configured operating systems:

| Check | Result |
|---|---|
| Build (Windows) | Success |
| Build (Ubuntu) | Success |
| Build (macOS) | Success |

All three checks completed successfully on June 18, 2026. This upstream CI evidence complements the
local Phase 4 validation, where the original and refactored revisions both passed 31 focused tests
and the refactored revision passed 102 maximum-flow regression tests.

## 6. Commit Evidence

Commit subject:

```text
Refactor Boykov-Kolmogorov orphan adoption
```

Commit body:

```text
Extract orphan selection from adopt().

Extract source-tree and sink-tree orphan handling.

Extract source-tree and sink-tree parent search.

Preserve orphan ordering, edge orientation, timestamp updates, and debug output.
```

The commit is focused on one production file:

```text
jgrapht-core/src/main/java/org/jgrapht/alg/flow/BoykovKolmogorovMFImpl.java
```

## 7. Visual Evidence

### 7.1 Pull Request Submission

The screenshot `pull_request_1355.png` shows:

- PR number `#1355`.
- The clear refactoring title.
- Open status.
- Author `Avraaaa`.
- Source branch `Avraaaa:refactor-boykov-kolmogorov-adopt`.
- Target branch `jgrapht:master`.
- One commit and one changed file.
- The opening explanation of the code smell and refactoring.

### 7.2 CI Checks

The screenshot `pull_request_1355_checks.png` shows successful Windows, macOS, and Ubuntu builds.

## 8. Phase 6 Completion Checklist

- [x] Forked the original repository.
- [x] Created a dedicated refactoring branch.
- [x] Applied and committed the improvement.
- [x] Pushed the branch to the fork.
- [x] Submitted a pull request to the original repository.
- [x] Used a clear title.
- [x] Explained the addressed maintainability issue.
- [x] Explained why the refactor improves maintainability.
- [x] Summarized the refactoring approach.
- [x] Assured that public behavior remains unchanged.
- [x] Included test commands in the PR description.
- [x] Passed all upstream GitHub Actions checks.

## 9. Conclusion

Phase 6 is complete. The change was submitted from the fork and dedicated refactoring branch to the
original JGraphT repository as pull request
[jgrapht/jgrapht#1355](https://github.com/jgrapht/jgrapht/pull/1355).

The live pull request satisfies the instructor's required title and description content. As of June
25, 2026, it is open, non-draft, cleanly mergeable, and has successful CI results on Windows,
Ubuntu, and macOS.
