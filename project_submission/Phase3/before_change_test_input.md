# Before-Change Test Input

## Target

The selected refactoring target is:

- `BoykovKolmogorovMFImpl.adopt()`

Both methods are private methods inside:

```text
jgrapht-core/src/main/java/org/jgrapht/alg/flow/BoykovKolmogorovMFImpl.java
```

They are exercised through the public maximum-flow API and the existing test class:

```text
jgrapht-core/src/test/java/org/jgrapht/alg/flow/BoykovKolmogorovMFImplTest.java
```

## Test Command

```powershell
mvn -pl jgrapht-core "-Dtest=org.jgrapht.alg.flow.BoykovKolmogorovMFImplTest" test
```

## Representative Test Input

The existing test suite contains 31 tests for `BoykovKolmogorovMFImpl`. A representative input from `test1()` is shown below.

```java
int[][] edges = {
    { 1, 5, 10 }, { 5, 9, 20 }, { 9, 8, 35 }, { 8, 3, 27 }, { 3, 2, 49 },
    { 2, 4, 10 }, { 4, 6, 26 }, { 6, 7, 28 }, { 2, 10, 34 }, { 1, 7, 15 },
    { 1, 10, 9 }, { 3, 7, 26 }, { 3, 4, 25 }, { 3, 9, 25 }, { 3, 8, 18 },
    { 4, 9, 28 }, { 4, 3, 4 }, { 4, 7, 22 }, { 4, 8, 1 }, { 6, 2, 42 },
    { 6, 4, 13 }, { 6, 8, 43 }, { 6, 5, 48 }, { 8, 9, 8 }, { 8, 5, 34 },
    { 9, 4, 36 }, { 9, 3, 47 }, { 3, 10, 10 }, { 4, 10, 39 }, { 5, 10, 16 },
};
testDirectedUndirected(edges, 1, 10, 19, 34);
```

## Expected Output

For the representative input:

- Directed graph maximum flow from source `1` to sink `10`: `19`
- Undirected graph maximum flow from source `1` to sink `10`: `34`

For the full selected test class:

- Tests run: `31`
- Failures: `0`
- Errors: `0`
- Skipped: `0`

