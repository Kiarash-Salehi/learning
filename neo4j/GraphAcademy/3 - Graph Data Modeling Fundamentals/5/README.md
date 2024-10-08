# Refactoring the Graph

## Module Overview

In this module, you will learn about:

- Why we refactor a graph data model and graph.
- Adding labels to the data model.

## Refactoring

### Why Refactor?

Refactoring is the process of changing the data model **and** the graph.

There are three reasons why you would refactor:

- The graph as modeled does not answer all of the use cases.
- A new use case has come up that you must account for in your data model.
- The Cypher for the use cases does not perform optimally, especially when the graph scales

### Steps for refactoring

To refactor a graph data model and a graph, you must:

1. Design the new data model.

2. Write Cypher code to transform the existing graph to implement the new data model.

3. Retest all use cases, possibly with updated Cypher code.
