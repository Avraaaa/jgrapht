# Manual Code-Smell Analysis

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

## Primary Manual Finding

The best Phase 3 candidate is `BoykovKolmogorovMFImpl.adopt()` together with the companion method `grow()`.

Manual inspection confirms the same concern reported by SonarQube:

- `adopt()` combines several distinct algorithm steps in one method.
- Source-tree and sink-tree handling are nearly symmetrical but duplicated.
- Nested conditional checks make the method hard to audit.
- The logic is internal to the algorithm, so helper extraction can preserve the public API.
- Existing tests for `BoykovKolmogorovMFImpl` provide a validation path after refactoring.

## Manual Versus Tool Findings

The tools identify metric thresholds and suspicious patterns. Manual inspection adds context:

- Some findings, such as `GodClass`, are real but too broad to fix safely in this project.
- Some smaller findings, such as useless parentheses, are easy but not meaningful enough for the assignment.
- The selected issue is supported by both metric evidence and manual readability concerns.
- The proposed change must remain behavior-preserving because the assignment allows refactoring only, not bug fixes or feature changes.

## Risk Assessment

`BoykovKolmogorovMFImpl` implements a maximum-flow algorithm, so the selected refactor has higher behavioral risk than the `NetworkGeneratorConfig` option. The planned refactor should therefore stay narrow:

- Keep all public APIs unchanged.
- Avoid changing algorithm decisions, edge orientation rules, queue ordering, or timestamp behavior.
- Prefer explicit source-tree and sink-tree helper methods over a generic abstraction that hides algorithm direction.
- Run focused tests for `BoykovKolmogorovMFImpl` before and after the change.
