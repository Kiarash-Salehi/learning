# Graph Data Modeling Fundamentals

## Module Overview

In this module, you will learn about:

- What graph data modeling is.
- How domain knowledge is important for modeling.
- The difference between a graph data model and an instance model.

## What is Graph Data Modeling?

### Why model?

If you use a Neo4j graph to support part or all of your application, you must collaboratively work with your stakeholders to design a graph that will:

- Answer the key use cases for the application.
- Provide the best Cypher statement performance for the key use cases.

### Components of a Neo4j graph

The Neo4j components that are used to define the graph data model are:

- Nodes
- Labels
- Relationships
- Properties

### Data modeling process

Here are the steps to create a graph data model:

1. Understand the domain and define specific use cases (questions) for the application.
2. Develop the initial graph data model:
   - Model the nodes (entities).
   - Model the relationships between nodes.
3. Test the use cases against the initial data model.
4. Create the graph (instance model) with test data using Cypher.
5. Test the use cases, including performance against the graph.
6. Refactor (improve) the graph data model due to a change in the key use cases or for performance reasons.
7. Implement the refactoring on the graph and retest using Cypher.

Graph data modeling is an iterative process. Your initial graph data model is a starting point, but as you learn more about the use cases or if the use cases change, the initial graph data model will need to change. In addition, you may find that especially when the graph scales, you will need to modify the graph **(refactor)** to achieve the best performance for your key use cases.

Refactoring is **very common** in the development process. A Neo4j graph has an optional schema which is quite flexible, unlike the schema in an RDBMS. A Cypher developer can easily modify the graph to represent an improved data model.
