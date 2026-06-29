# Before vs After Code Comparison

## Refactoring Target

Production file:

```text
jgrapht-core/src/main/java/org/jgrapht/alg/flow/BoykovKolmogorovMFImpl.java
```

Selected methods:

```text
grow()
adopt()
```

This document shows the planned refactoring shape for Phase 3. No production code has been
changed at this stage.



## 1. `adopt()` Before

The current `adopt()` method selects orphan vertices, searches for a new parent, either adopts or
frees the vertex, and processes child orphans. The source-tree and sink-tree branches are similar
but use different residual-edge directions.

```java
private void adopt()
{
    while (!orphans.isEmpty() || !childOrphans.isEmpty()) {
        VertexExtension currentVertex;

        if (childOrphans.isEmpty()) {
            currentVertex = orphans.get(orphans.size() - 1);
            orphans.remove(orphans.size() - 1);
        } else {
            currentVertex = childOrphans.removeLast();
        }

        if (currentVertex.isSourceTreeVertex()) {

            AnnotatedFlowEdge newParentEdge = null;
            int minDistance = Integer.MAX_VALUE;

            for (AnnotatedFlowEdge edge : currentVertex.getOutgoing()) {
                if (edge.getInverse().hasCapacity()) {
                    VertexExtension targetNode = edge.getTarget();

                    if (targetNode.isSourceTreeVertex()
                        && hasConnectionToTerminal(targetNode))
                    {
                        if (targetNode.distance < minDistance) {
                            minDistance = targetNode.distance;
                            newParentEdge = edge.getInverse();
                        }
                    }
                }
            }

            if (newParentEdge == null) {
                currentVertex.timestamp = FREE_NODE_TIMESTAMP;
                currentVertex.treeStatus = VertexTreeStatus.FREE_VERTEX;

                for (AnnotatedFlowEdge edge : currentVertex.getOutgoing()) {
                    VertexExtension targetVertex = edge.getTarget();
                    if (targetVertex.isSourceTreeVertex()) {
                        if (edge.getInverse().hasCapacity()) {
                            makeActive(targetVertex);
                        }
                        if (targetVertex.parentEdge == edge) {
                            targetVertex.makeOrphan();
                            childOrphans.addFirst(targetVertex);
                        }
                    }
                }
            } else {
                makeCheckedInThisIteration(currentVertex);
                currentVertex.parentEdge = newParentEdge;
                currentVertex.distance = minDistance + 1;
            }

        } else {
            assert currentVertex.isSinkTreeVertex();

            AnnotatedFlowEdge newParentEdge = null;
            int minDistance = Integer.MAX_VALUE;
            for (AnnotatedFlowEdge edge : currentVertex.getOutgoing()) {
                if (edge.hasCapacity()) {
                    VertexExtension targetNode = edge.getTarget();

                    if (targetNode.isSinkTreeVertex() && hasConnectionToTerminal(targetNode)) {
                        if (targetNode.distance < minDistance) {
                            minDistance = targetNode.distance;
                            newParentEdge = edge;
                        }
                    }
                }
            }

            if (newParentEdge == null) {
                currentVertex.timestamp = FREE_NODE_TIMESTAMP;
                currentVertex.treeStatus = VertexTreeStatus.FREE_VERTEX;

                for (AnnotatedFlowEdge edge : currentVertex.getOutgoing()) {
                    VertexExtension targetVertex = edge.getTarget();
                    if (targetVertex.isSinkTreeVertex()) {
                        if (edge.hasCapacity()) {
                            makeActive(targetVertex);
                        }
                        if (targetVertex.parentEdge == edge.getInverse()) {
                            targetVertex.makeOrphan();
                            childOrphans.addFirst(targetVertex);
                        }
                    }
                }
            } else {
                makeCheckedInThisIteration(currentVertex);
                currentVertex.parentEdge = newParentEdge;
                currentVertex.distance = minDistance + 1;
            }
        }
    }
}
```

## 2. `adopt()` After

The planned version removes the `ParentCandidate` idea and returns only the selected parent edge.
This is enough because the adopted vertex can compute the new distance from the selected parent
after `parentEdge` is assigned.

```java
private void adopt()
{
    while (!orphans.isEmpty() || !childOrphans.isEmpty()) {
        VertexExtension currentVertex = nextOrphan();

        if (currentVertex.isSourceTreeVertex()) {
            adoptSourceOrphan(currentVertex);
        } else {
            assert currentVertex.isSinkTreeVertex();
            adoptSinkOrphan(currentVertex);
        }
    }
}

private VertexExtension nextOrphan()
{
    if (childOrphans.isEmpty()) {
        VertexExtension orphan = orphans.get(orphans.size() - 1);
        orphans.remove(orphans.size() - 1);
        return orphan;
    }

    return childOrphans.removeLast();
}

private void adoptSourceOrphan(VertexExtension currentVertex)
{
    AnnotatedFlowEdge newParentEdge = findSourceTreeParent(currentVertex);

    if (newParentEdge == null) {
        makeSourceTreeVertexFree(currentVertex);
    } else {
        adoptWithParent(currentVertex, newParentEdge);
    }
}

private AnnotatedFlowEdge findSourceTreeParent(VertexExtension currentVertex)
{
    AnnotatedFlowEdge newParentEdge = null;
    int minDistance = Integer.MAX_VALUE;

    for (AnnotatedFlowEdge edge : currentVertex.getOutgoing()) {
        if (!edge.getInverse().hasCapacity()) {
            continue;
        }

        VertexExtension targetNode = edge.getTarget();
        if (targetNode.isSourceTreeVertex()
            && hasConnectionToTerminal(targetNode)
            && targetNode.distance < minDistance)
        {
            minDistance = targetNode.distance;
            newParentEdge = edge.getInverse();
        }
    }

    return newParentEdge;
}

private void makeSourceTreeVertexFree(VertexExtension currentVertex)
{
    makeFree(currentVertex);

    for (AnnotatedFlowEdge edge : currentVertex.getOutgoing()) {
        VertexExtension targetVertex = edge.getTarget();
        if (!targetVertex.isSourceTreeVertex()) {
            continue;
        }

        if (edge.getInverse().hasCapacity()) {
            makeActive(targetVertex);
        }

        // Source-tree children store the forward edge from this vertex to the child.
        if (targetVertex.parentEdge == edge) {
            targetVertex.makeOrphan();
            childOrphans.addFirst(targetVertex);
        }
    }
}

private void adoptSinkOrphan(VertexExtension currentVertex)
{
    AnnotatedFlowEdge newParentEdge = findSinkTreeParent(currentVertex);

    if (newParentEdge == null) {
        makeSinkTreeVertexFree(currentVertex);
    } else {
        adoptWithParent(currentVertex, newParentEdge);
    }
}

private AnnotatedFlowEdge findSinkTreeParent(VertexExtension currentVertex)
{
    AnnotatedFlowEdge newParentEdge = null;
    int minDistance = Integer.MAX_VALUE;

    for (AnnotatedFlowEdge edge : currentVertex.getOutgoing()) {
        if (!edge.hasCapacity()) {
            continue;
        }

        VertexExtension targetNode = edge.getTarget();
        if (targetNode.isSinkTreeVertex()
            && hasConnectionToTerminal(targetNode)
            && targetNode.distance < minDistance)
        {
            minDistance = targetNode.distance;
            newParentEdge = edge;
        }
    }

    return newParentEdge;
}

private void makeSinkTreeVertexFree(VertexExtension currentVertex)
{
    makeFree(currentVertex);

    for (AnnotatedFlowEdge edge : currentVertex.getOutgoing()) {
        VertexExtension targetVertex = edge.getTarget();
        if (!targetVertex.isSinkTreeVertex()) {
            continue;
        }

        if (edge.hasCapacity()) {
            makeActive(targetVertex);
        }

        // Sink-tree children store the inverse edge because the tree is rooted at the sink.
        if (targetVertex.parentEdge == edge.getInverse()) {
            targetVertex.makeOrphan();
            childOrphans.addFirst(targetVertex);
        }
    }
}

private void makeFree(VertexExtension currentVertex)
{
    currentVertex.timestamp = FREE_NODE_TIMESTAMP;
    currentVertex.treeStatus = VertexTreeStatus.FREE_VERTEX;
}

private void adoptWithParent(VertexExtension currentVertex, AnnotatedFlowEdge newParentEdge)
{
    makeCheckedInThisIteration(currentVertex);
    currentVertex.parentEdge = newParentEdge;
    currentVertex.distance = currentVertex.getParent().distance + 1;
}
```

## 5. Comparison Summary

| Area | Before | After |
|---|---|---|
| `adopt()` control flow | One method contains orphan selection, parent search, adoption, and free-vertex handling | Main method delegates orphan selection and source/sink adoption cases |
| Duplicate logic | Source and sink branches repeat the same structure inline | Shared simple operations are extracted; directional rules remain explicit |
| Abstraction level | Large methods mix algorithm steps and implementation details | Helper names describe each algorithm step |
| Public API | Unchanged | Unchanged |
