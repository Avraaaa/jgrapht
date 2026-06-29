# Explanation of Changes and Reasoning

## 1. Assignment Context

Phase 3 requires refactoring and improvement based on the issues identified during code-quality
analysis. The required deliverables include:

- Before vs after code comparison.
- Explanation of changes and reasoning.

This document covers the second deliverable. Git commit history is intentionally not included here
because this Phase 3 draft is being prepared before changing production code.

## 2. Selected Code Smell

The selected target is `BoykovKolmogorovMFImpl`, especially:

```text
grow()
adopt()
```

The selected smell is:

```text
Brain Method / Long Method with duplicated source-tree and sink-tree conditional logic
```

This target is supported by both tool output and manual inspection:

- SonarQube reports high cognitive complexity for `grow()`.
- SonarQube reports high cognitive complexity and Brain Method for `adopt()`.
- PMD reports nested conditional issues inside `adopt()`.
- Manual inspection shows duplicated source-tree and sink-tree branches with reversed edge
  direction rules.

## 3. Why This Is a Valid Refactoring Target

The selected methods are not only failing static-analysis thresholds. They also contain real
maintainability problems:

- `grow()` combines source-tree growth, sink-tree growth, bounding-edge detection, free-vertex
  insertion, and distance-heuristic parent updates.
- `adopt()` combines orphan selection, source-tree adoption, sink-tree adoption, parent search,
  free-vertex conversion, neighbor activation, and child-orphan creation.
- Both methods require the reader to compare source-tree and sink-tree branches manually.
- The source and sink branches are similar enough to be duplicate logic, but different enough that
  accidental edge-orientation changes would be easy during future maintenance.

The planned refactor addresses these issues by decomposing the methods into smaller helpers named
after the algorithm steps.

## 4. Why Explicit Source/Sink Helpers Are Preferred

An earlier possible design was to introduce a generic abstraction such as `TreeSide` or
`ParentCandidate`. That was rejected for this Phase 3 refactor.

Explicit source/sink helper methods are clearer here because the Boykov-Kolmogorov implementation
has important directional differences:

- Source-tree parent search uses `edge.getInverse().hasCapacity()`.
- Sink-tree parent search uses `edge.hasCapacity()`.
- Source-tree adoption stores `edge.getInverse()` as the selected parent edge.
- Sink-tree adoption stores `edge` as the selected parent edge.
- Source-tree child detection compares `targetVertex.parentEdge == edge`.
- Sink-tree child detection compares `targetVertex.parentEdge == edge.getInverse()`.

A generic abstraction would reduce lines, but it would also hide these edge-direction rules. For a
refactoring-only contribution to an algorithm implementation, preserving readability of the
algorithmic invariants is more important than minimizing helper count.

## 5. Why `ParentCandidate` Is Not Needed

`ParentCandidate` would store two values:

```text
selected parent edge
selected parent distance
```

However, this abstraction does not improve readability enough to justify itself. The selected
parent distance can be recovered after assigning the parent edge:

```java
currentVertex.parentEdge = newParentEdge;
currentVertex.distance = currentVertex.getParent().distance + 1;
```

This keeps the helper method return type simple:

```java
private AnnotatedFlowEdge findSourceTreeParent(VertexExtension currentVertex)
private AnnotatedFlowEdge findSinkTreeParent(VertexExtension currentVertex)
```

Returning only `AnnotatedFlowEdge` also makes the code easier to audit because `null` naturally
means no valid parent was found.

## 6. Planned Changes

### 6.1 `grow()`

The planned `grow()` refactor will:

- Keep the main `grow()` loop responsible for active-vertex iteration.
- Extract source-tree processing into `growSourceTree()`.
- Extract sink-tree processing into `growSinkTree()`.
- Extract the repeated free-vertex growth assignments into `growTreeVertex()`.
- Extract the distance-heuristic parent update into `updateParentIfCloser()`.

This reduces the amount of branching inside `grow()` while preserving the active-vertex order and
the condition for returning a bounding edge.

### 6.2 `adopt()`

The planned `adopt()` refactor will:

- Extract orphan selection into `nextOrphan()`.
- Extract source-tree orphan handling into `adoptSourceOrphan()`.
- Extract sink-tree orphan handling into `adoptSinkOrphan()`.
- Extract parent search into `findSourceTreeParent()` and `findSinkTreeParent()`.
- Extract free-vertex processing into `makeSourceTreeVertexFree()` and
  `makeSinkTreeVertexFree()`.
- Extract common adoption assignment into `adoptWithParent()`.
- Extract common free-vertex status assignment into `makeFree()`.

This keeps the source and sink behavior explicit while separating parent search from adoption and
free-vertex processing.

## 7. Commenting Approach

The refactored code should avoid comments that simply restate method names or assignments. Comments
should be added only for non-obvious algorithmic invariants or edge cases.

Useful comments:

```java
// Source-tree children store the forward edge from this vertex to the child.
if (targetVertex.parentEdge == edge) {
    ...
}

// Sink-tree children store the inverse edge because the tree is rooted at the sink.
if (targetVertex.parentEdge == edge.getInverse()) {
    ...
}
```

Unnecessary comments:

```java
// Set the parent edge.
currentVertex.parentEdge = newParentEdge;

// Return null.
return null;
```

The final refactor should keep comments focused on edge orientation and tree invariants, because
those are the parts most likely to cause behavior changes if misunderstood.

## 8. Behavior Preservation

This refactor must preserve all observable behavior. The following must not change:

- Public class and method signatures.
- Maximum-flow results.
- Active-vertex processing order.
- Orphan and child-orphan processing order.
- Source-tree and sink-tree edge orientation.
- Residual-capacity checks.
- Timestamp updates.
- Distance-heuristic behavior.
- Bounding-edge detection.

The refactor should only move existing logic into helper methods and simplify nested conditionals.

## 9. Validation Plan

Before changing production code, the existing baseline has already been documented in Phase 3. After
the refactor, the same tests should be run again.

Focused validation:

```powershell
mvn -pl jgrapht-core "-Dtest=org.jgrapht.alg.flow.BoykovKolmogorovMFImplTest" test
```

Broader validation if time permits:

```powershell
mvn -pl jgrapht-core "-Dtest=*MFImplTest,MaximumFlowAlgorithmTest" test
```

Project-level validation if feasible:

```powershell
mvn -pl jgrapht-core -am test
```

Expected result:

```text
All maximum-flow tests should pass with the same expected flow values as before the refactor.
```

## 10. Pull Request Risk Assessment

This is appropriate for a refactoring-only contribution if the final diff remains narrow.

Low-risk aspects:

- The target methods are private.
- No public API changes are required.
- The existing test suite already exercises `BoykovKolmogorovMFImpl` through public maximum-flow
  behavior.
- The planned helpers mostly move existing code without changing decisions.

Higher-risk aspects:

- The code implements a graph algorithm, so small edge-direction mistakes can change results.
- Source-tree and sink-tree logic is similar but not identical.
- Orphan handling depends on queue ordering and parent-edge identity comparisons.

Risk mitigation:

- Prefer explicit source/sink helper methods instead of a generic abstraction.
- Keep comments only where edge orientation is non-obvious.
- Run focused and related max-flow tests before and after the change.
- Avoid combining this refactor with logging cleanup, formatting-only churn, or unrelated code
  smells.

## 11. Summary

The proposed Phase 3 refactor is a valid maintainability improvement because it addresses long
methods, duplicated conditional logic, and high cognitive complexity in a focused way. The design
uses explicit source-tree and sink-tree helpers to improve readability without hiding the
algorithm's directional invariants. Unnecessary abstractions such as `ParentCandidate` are avoided
because they do not add enough clarity for this code.
