# Phase 2 Report: Code-Smell Analysis and Refactoring Target Selection

## 1. Phase 2 Objective

The goal of Phase 2 is to identify maintainability issues in JGraphT using both static-analysis tools and manual inspection. The selected issue must be suitable for a future behavior-preserving refactoring. Since the project is an open-source Java graph library, the selected change should avoid public API changes and should be small enough to validate with focused tests.

This phase uses evidence from:

- PMD output stored in `project_submission/Phase2/pmd_log.txt`.
- SonarQube output stored in `project_submission/Phase2/sonarqube_log.txt` and `project_submission/Phase2/sonarqube_output.txt`.
- IntelliJ PMD screenshots stored as `intellij_pmd_1.png` and `intellij_pmd_2.png`.
- Manual source-code inspection of the highest-value findings.

## 2. Static Analysis Summary

PMD and SonarQube reported several categories of code smells. The most relevant categories for this project were:

- Brain Method / high cognitive complexity.
- Long Method.
- Duplicated conditional logic.
- Deeply nested conditionals.
- Long Parameter List.
- Too Many Methods / God Class.
- Smaller cleanup issues such as useless parentheses, collapsible `if` statements, and debug output warnings.

Not all tool findings are equally useful for the project. Some findings are too cosmetic, while others are too broad or risky for a refactoring-only contribution. Therefore, manual analysis was used to decide which findings represent real maintainability problems and which ones should be avoided.

## 3. Manual Code-Smell Analysis

Manual analysis complements the PMD and SonarQube reports by checking whether each finding is a real maintainability issue, whether a behavior-preserving refactor is possible, and whether the change is suitable for an upstream JGraphT pull request.

| Smell type | File/method location | Manual evidence | Why it is problematic | Selected for refactoring? | Reason |
|---|---|---|---|---|---|
| Brain Method / Long Method | `BoykovKolmogorovMFImpl.adopt()` at `jgrapht-core/src/main/java/org/jgrapht/alg/flow/BoykovKolmogorovMFImpl.java:465` | The method handles orphan selection, parent searching, source-tree adoption, sink-tree adoption, freeing orphan vertices, activating neighbors, and creating child orphans | Several algorithm steps are concentrated in one method, and the source-tree and sink-tree branches repeat nearly symmetrical logic with reversed edge direction | Yes | Strongest combined evidence: SonarQube reports cognitive complexity 112 and Brain Method; PMD also reports collapsible nested `if` statements inside this method |
| Duplicated conditional logic | `BoykovKolmogorovMFImpl.adopt()` source-tree and sink-tree branches | Both branches search for a new parent edge, check residual capacity, verify same-tree membership, test terminal connection, compare distance, then either adopt the vertex or make it free | The duplicated structure makes future fixes risky because source and sink behavior must stay symmetric while using different edge orientation rules | Yes | Refactoring can extract meaningful source-tree and sink-tree adoption helpers while preserving the algorithm and public API |
| Long Method / Conditional Complexity | `BoykovKolmogorovMFImpl.grow()` at `jgrapht-core/src/main/java/org/jgrapht/alg/flow/BoykovKolmogorovMFImpl.java:265` | The method has separate source-tree and sink-tree loops, capacity checks, bounding-edge detection, free-vertex growth, and distance-heuristic parent updates | The method is difficult to scan because the two tree cases are embedded in one deeply branched control-flow structure | Yes, as companion | It belongs to the same Boykov-Kolmogorov grow/adopt phase and SonarQube reports cognitive complexity 68 |
| Deeply nested conditionals | `AhujaOrlinSharmaCyclicExchangeLocalAugmentation.getLocalAugmentationCycle()` at `jgrapht-core/src/main/java/org/jgrapht/alg/cycle/AhujaOrlinSharmaCyclicExchangeLocalAugmentation.java:110` | The method initializes candidate paths, handles self-loops, extends negative paths, checks labels, tests domination, and updates path indexes in nested branches | Readers must track path length, label-set validity, cost negativity, cycle discovery, and domination rules at the same time | Secondary candidate only | Valid smell with SonarQube cognitive complexity 54, but it is less connected to the chosen final target |
| Long Parameter List / Data Clumps | `NetworkGeneratorConfig` constructor at `jgrapht-core/src/main/java/org/jgrapht/generate/netgen/NetworkGeneratorConfig.java:159` | The package-private constructor receives 13 mostly primitive parameters copied from `NetworkGeneratorConfigBuilder` | Many same-type positional arguments can be accidentally reordered and are harder to verify during review | Not selected | Safer and useful, but less impressive for the assignment than the Boykov-Kolmogorov algorithm refactor |
| Loop-counter mutation | `NetworkGenerator.generateChains()` and `NetworkGenerator.connectChainsToSinks()` | PMD and SonarQube report loop-counter modification inside loop bodies | Changing loop counters inside a loop body makes iteration behavior less direct and easier to misread | Not selected | Fixable without adding methods, but too small to be the primary Phase 3 target |
| God Class / Too Many Methods | `BoykovKolmogorovMFImpl`, `NetworkGenerator`, `NetworkGeneratorConfigBuilder`, and other algorithm classes | IntelliJ PMD flags several large classes with many methods | Large method count can indicate broad responsibility, but in graph algorithms some size comes from algorithm-specific state and helper operations | No broad class split | Splitting algorithm classes would be risky, likely too large for a refactoring-only contribution, and may harm readability |
| Debug output concerns | `BoykovKolmogorovMFImpl` debug branches using `System.out` | SonarQube reports console output and platform-specific newline warnings | Direct console output is usually undesirable in library code | No | The debug flag is currently constant `false`; replacing logging is outside the selected behavior-preserving refactor |

## 4. Selection Strategy

The refactoring target should:

- Be supported by tool output and manual inspection.
- Be meaningful enough for the assignment, not only cosmetic cleanup.
- Preserve public API and observable behavior.
- Avoid broad architectural changes to a mature graph library.
- Produce a clear before/after comparison with focused validation.

The selected target should not be a broad God Class split. Although IntelliJ PMD reports Too Many Methods and God Class-style findings, those changes would require large architectural restructuring and would be risky for a refactoring-only assignment.

## 5. Primary Refactoring Target

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

## 6. Companion Refactoring Target

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

## 7. Secondary Candidate

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

## 8. Alternative Safe Candidate

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

## 9. Documented but Not Selected

| Smell | Evidence | Why not selected |
|---|---|---|
| God Class in algorithm classes | IntelliJ PMD reports several large classes, including `BoykovKolmogorovMFImpl` | Splitting algorithm classes is too broad and risky for a refactoring-only assignment |
| Too Many Methods | IntelliJ PMD flags `BoykovKolmogorovMFImpl`, `NetworkGenerator`, and `NetworkGeneratorConfigBuilder` | Reducing method count would require larger restructuring; selected refactor may add a few helpers but improves a stronger Brain Method smell |
| Debug `System.out` usage | SonarQube flags debug print statements in `BoykovKolmogorovMFImpl` | Logging changes are not central to the chosen smell and may distract from behavior-preserving algorithm refactoring |
| Useless parentheses and imports | PMD reports many simple cleanup issues | Too cosmetic for the main assignment target |
| Broad netgen loop cleanup | PMD and SonarQube flag loop-counter mutation in `NetworkGenerator` | Valid but smaller than the selected Brain Method refactor |

## 10. Risk Assessment

`BoykovKolmogorovMFImpl` implements a maximum-flow algorithm, so the selected refactor has higher behavioral risk than the `NetworkGeneratorConfig` option. The planned refactor should therefore stay narrow:

- Keep all public APIs unchanged.
- Avoid changing algorithm decisions, edge orientation rules, queue ordering, or timestamp behavior.
- Prefer explicit source-tree and sink-tree helper methods over a generic abstraction that hides algorithm direction.
- Avoid changing debug output or logging as part of the same refactor.
- Run focused tests for `BoykovKolmogorovMFImpl` before and after the change.

## 11. Phase 2 Decision

Proceed to Phase 3 with `BoykovKolmogorovMFImpl.adopt()` as the primary refactoring target and `BoykovKolmogorovMFImpl.grow()` as the companion target.

The selected smell is:

> Brain Method / Long Method with duplicated source-tree and sink-tree conditional logic.

Required validation before and after refactoring:

- Run the existing `BoykovKolmogorovMFImplTest` tests.
- Run related max-flow tests if time permits.
- Confirm no public API signatures change.
- Confirm the refactor only moves and clarifies logic, without changing edge orientation, active-vertex order, orphan ordering, timestamp updates, or distance calculations.
