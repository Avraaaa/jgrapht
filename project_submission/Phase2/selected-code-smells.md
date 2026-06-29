# Selected Code Smells

## Selection Strategy

The refactoring target should:

- Be supported by tool output and manual inspection.
- Be meaningful enough for the assignment, not only cosmetic cleanup.
- Preserve public API and observable behavior.
- Avoid broad architectural changes to a mature graph library.
- Produce a clear before/after comparison with focused validation.

## Primary Refactoring Target

### `BoykovKolmogorovMFImpl.adopt()`

Location: `jgrapht-core/src/main/java/org/jgrapht/alg/flow/BoykovKolmogorovMFImpl.java:465`

Recorded tool findings:

- SonarQube `java:S3776`: cognitive complexity 112, above the allowed 15.
- SonarQube `java:S6541`: Brain Method detected.
- SonarQube `java:S1066`: nested `if` statements at lines 492 and 548.
- PMD `CollapsibleIfStatements`: matching findings at lines 492 and 548.

Manual finding:

The method performs several responsibilities:

- Selects the next orphan vertex.
- Searches for a new parent in the source tree.
- Searches for a new parent in the sink tree.
- Makes an orphan vertex free when no parent exists.
- Activates neighboring vertices.
- Creates child orphans.
- Applies the distance and timestamp heuristics.

Why selected:

- The smell is supported by both SonarQube and PMD.
- The duplicated source-tree and sink-tree branches are visible in the code.
- The method is private, so refactoring can avoid public API changes.
- The refactor can be explained as decomposition of a Brain Method rather than style-only cleanup.
- The class already has tests under `BoykovKolmogorovMFImplTest`, giving a validation path.

Planned refactoring direction:

```java
private void adopt()
{
    while (!orphans.isEmpty() || !childOrphans.isEmpty()) {
        VertexExtension currentVertex = /* existing orphan-selection logic */;

        if (currentVertex.isSourceTreeVertex()) {
            adoptSourceTreeVertex(currentVertex); // extracted source-tree case
        } else {
            assert currentVertex.isSinkTreeVertex();
            adoptSinkTreeVertex(currentVertex); // extracted sink-tree case
        }
    }
}
```

The implementation should avoid a broad `TreeSide` abstraction unless review shows it improves readability. Explicit source and sink helpers are safer because they keep the algorithm's edge-direction rules visible.

## Companion Refactoring Target

### `BoykovKolmogorovMFImpl.grow()`

Location: `jgrapht-core/src/main/java/org/jgrapht/alg/flow/BoykovKolmogorovMFImpl.java:265`

Recorded tool finding:

- SonarQube `java:S3776`: cognitive complexity 68, above the allowed 15.

Manual finding:

The method handles two symmetrical cases:

- Growing from a source-tree active vertex.
- Growing from a sink-tree active vertex.

Each case performs capacity checks, detects a bounding edge, grows the tree through a free vertex, and updates the parent edge when the distance heuristic finds a closer path.

Why included as companion:

- It belongs to the same grow/adopt phase of the Boykov-Kolmogorov algorithm.
- It has the same source-tree versus sink-tree duplication pattern as `adopt()`.
- Refactoring it with the same style makes the selected change more coherent.

Planned refactoring direction:

```java
private AnnotatedFlowEdge grow()
{
    for (VertexExtension activeVertex = nextActiveVertex(); activeVertex != null;
        activeVertex = nextActiveVertex())
    {
        AnnotatedFlowEdge boundingEdge = activeVertex.isSourceTreeVertex()
            ? growSourceTree(activeVertex)
            : growSinkTree(activeVertex);

        if (boundingEdge != null) {
            return boundingEdge;
        }

        finishVertex(activeVertex);
    }

    return null;
}
```

This companion should be kept smaller than the `adopt()` change. The goal is to reduce duplicated conditional structure, not to redesign the algorithm.

## Secondary Candidate

### `AhujaOrlinSharmaCyclicExchangeLocalAugmentation.getLocalAugmentationCycle()`

Location: `jgrapht-core/src/main/java/org/jgrapht/alg/cycle/AhujaOrlinSharmaCyclicExchangeLocalAugmentation.java:110`

Recorded tool findings:

- SonarQube `java:S3776`: cognitive complexity 54, above the allowed 15.
- SonarQube `java:S1066`: nested `if` at line 212.
- PMD `CollapsibleIfStatements`: matching finding near line 212.

Manual finding:

The method combines path initialization, self-loop handling, cycle detection, path extension, label-set checking, domination checking, and path-index updates.

Why not selected:

- It is a valid smell, but the final selected target is stronger because `adopt()` has both cognitive-complexity and Brain Method evidence.
- The Boykov-Kolmogorov pair gives a clearer theme: duplicated source-tree/sink-tree algorithm logic.
- Refactoring only one Ahuja method would be useful, but slightly less impressive than addressing `adopt()` and `grow()` together.

## Alternative Safe Candidate

### `NetworkGeneratorConfig` constructor

Location: `jgrapht-core/src/main/java/org/jgrapht/generate/netgen/NetworkGeneratorConfig.java:159`

Recorded tool finding:

- SonarQube `java:S107`: constructor has 13 parameters, above the authorized 7.

Manual finding:

The package-private constructor receives many primitive configuration values from `NetworkGeneratorConfigBuilder`. This is a Long Parameter List and Data Clumps smell because the values belong together as one network-generation configuration.

Why not selected:

- It is the safest upstream PR candidate.
- However, it is less complex and less impressive for the assignment than the selected algorithm refactor.
- IntelliJ PMD also flags related classes for Too Many Methods, so adding helper methods in the netgen package would need care.

## Documented but Not Selected

| Smell | Evidence | Why not selected |
|---|---|---|
| God Class in algorithm classes | IntelliJ PMD reports several large classes, including `BoykovKolmogorovMFImpl` | Splitting algorithm classes is too broad and risky for a refactoring-only assignment |
| Too Many Methods | IntelliJ PMD flags `BoykovKolmogorovMFImpl`, `NetworkGenerator`, and `NetworkGeneratorConfigBuilder` | Reducing method count would require larger restructuring; selected refactor may add a few helpers but improves a stronger Brain Method smell |
| Debug `System.out` usage | SonarQube flags debug print statements in `BoykovKolmogorovMFImpl` | Logging changes are not central to the chosen smell and may distract from behavior-preserving algorithm refactoring |
| Useless parentheses and imports | PMD reports many simple cleanup issues | Too cosmetic for the main assignment target |
| Broad netgen loop cleanup | PMD and SonarQube flag loop-counter mutation in `NetworkGenerator` | Valid but smaller than the selected Brain Method refactor |

## Decision

Proceed to Phase 3 with `BoykovKolmogorovMFImpl.adopt()` as the primary refactoring target and `BoykovKolmogorovMFImpl.grow()` as the companion target.

The selected smell is:

> Brain Method / Long Method with duplicated source-tree and sink-tree conditional logic.

Required validation before and after refactoring:

- Run the existing `BoykovKolmogorovMFImplTest` tests.
- Run related max-flow tests if time permits.
- Confirm no public API signatures change.
- Confirm the refactor only moves and clarifies logic, without changing edge orientation, active-vertex order, orphan ordering, timestamp updates, or distance calculations.
