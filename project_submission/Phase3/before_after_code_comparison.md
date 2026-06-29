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

## 1. `grow()` Before

The current `grow()` method handles both source-tree and sink-tree active vertices inside one
method. The two branches perform the same algorithmic steps with different vertex direction.

```java
private AnnotatedFlowEdge grow()
{
    for (VertexExtension activeVertex = nextActiveVertex(); activeVertex != null;
        activeVertex = nextActiveVertex())
    {

        if (activeVertex.isSourceTreeVertex()) {
            for (AnnotatedFlowEdge edge : activeVertex.getOutgoing()) {

                if (edge.hasCapacity()) {
                    VertexExtension target = edge.getTarget();

                    if (target.isSinkTreeVertex()) {
                        return edge;
                    } else if (target.isFreeVertex()) {
                        target.parentEdge = edge;
                        target.treeStatus = VertexTreeStatus.SOURCE_TREE_VERTEX;
                        target.distance = activeVertex.distance + 1;
                        target.timestamp = activeVertex.timestamp;
                        makeActive(target);
                    } else {
                        assert target.isSourceTreeVertex();
                        if (isCloserToTerminal(activeVertex, target)) {
                            target.parentEdge = edge;
                            target.distance = activeVertex.distance + 1;
                            target.timestamp = activeVertex.timestamp;
                        }
                    }
                }
            }
        } else {
            assert activeVertex.isSinkTreeVertex();

            for (AnnotatedFlowEdge edge : activeVertex.getOutgoing()) {

                if (edge.hasCapacity()) {
                    VertexExtension source = edge.getSource();

                    if (source.isSourceTreeVertex()) {
                        return edge;
                    } else if (source.isFreeVertex()) {
                        source.parentEdge = edge;
                        source.treeStatus = VertexTreeStatus.SINK_TREE_VERTEX;
                        source.distance = activeVertex.distance + 1;
                        source.timestamp = activeVertex.timestamp;
                        makeActive(source);
                    } else {
                        assert source.isSinkTreeVertex();
                        if (isCloserToTerminal(activeVertex, source)) {
                            source.parentEdge = edge;
                            source.distance = activeVertex.distance + 1;
                            source.timestamp = activeVertex.timestamp;
                        }
                    }
                }
            }
        }

        finishVertex(activeVertex);
    }

    return null;
}
```

## 2. `grow()` After

The planned version keeps `grow()` as the high-level algorithm step and moves the two directional
cases into explicit helpers.

```java
private AnnotatedFlowEdge grow()
{
    for (VertexExtension activeVertex = nextActiveVertex(); activeVertex != null;
        activeVertex = nextActiveVertex())
    {
        AnnotatedFlowEdge boundingEdge;
        if (activeVertex.isSourceTreeVertex()) {
            boundingEdge = growSourceTree(activeVertex);
        } else {
            assert activeVertex.isSinkTreeVertex();
            boundingEdge = growSinkTree(activeVertex);
        }

        if (boundingEdge != null) {
            return boundingEdge;
        }

        finishVertex(activeVertex);
    }

    return null;
}

private AnnotatedFlowEdge growSourceTree(VertexExtension activeVertex)
{
    for (AnnotatedFlowEdge edge : activeVertex.getOutgoing()) {
        if (!edge.hasCapacity()) {
            continue;
        }

        VertexExtension target = edge.getTarget();
        if (target.isSinkTreeVertex()) {
            return edge;
        }

        if (target.isFreeVertex()) {
            growTreeVertex(activeVertex, target, edge, VertexTreeStatus.SOURCE_TREE_VERTEX);
        } else {
            assert target.isSourceTreeVertex();
            updateParentIfCloser(activeVertex, target, edge);
        }
    }

    return null;
}

private AnnotatedFlowEdge growSinkTree(VertexExtension activeVertex)
{
    for (AnnotatedFlowEdge edge : activeVertex.getOutgoing()) {
        if (!edge.hasCapacity()) {
            continue;
        }

        VertexExtension source = edge.getSource();
        if (source.isSourceTreeVertex()) {
            return edge;
        }

        if (source.isFreeVertex()) {
            growTreeVertex(activeVertex, source, edge, VertexTreeStatus.SINK_TREE_VERTEX);
        } else {
            assert source.isSinkTreeVertex();
            updateParentIfCloser(activeVertex, source, edge);
        }
    }

    return null;
}

private void growTreeVertex(
    VertexExtension activeVertex, VertexExtension newVertex, AnnotatedFlowEdge parentEdge,
    VertexTreeStatus treeStatus)
{
    newVertex.parentEdge = parentEdge;
    newVertex.treeStatus = treeStatus;
    newVertex.distance = activeVertex.distance + 1;
    newVertex.timestamp = activeVertex.timestamp;
    makeActive(newVertex);
}

private void updateParentIfCloser(
    VertexExtension activeVertex, VertexExtension candidate, AnnotatedFlowEdge parentEdge)
{
    if (isCloserToTerminal(activeVertex, candidate)) {
        candidate.parentEdge = parentEdge;
        candidate.distance = activeVertex.distance + 1;
        candidate.timestamp = activeVertex.timestamp;
    }
}
```

## 3. `adopt()` Before

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

## 4. `adopt()` After

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
| `grow()` control flow | One method contains source and sink processing branches | Main method delegates to `growSourceTree()` and `growSinkTree()` |
| `adopt()` control flow | One method contains orphan selection, parent search, adoption, and free-vertex handling | Main method delegates orphan selection and source/sink adoption cases |
| Duplicate logic | Source and sink branches repeat the same structure inline | Shared simple operations are extracted; directional rules remain explicit |
| Abstraction level | Large methods mix algorithm steps and implementation details | Helper names describe each algorithm step |
| Public API | Unchanged | Unchanged |
