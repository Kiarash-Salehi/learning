# Reading Data from Neo4j

In this module you will learn how to write Cypher code to retrieve data from the graph.

You will learn how to:

- Retrieve nodes from the graph.
  - Retrieve nodes with a particular label.
  - Filter the retrieval by a property value.
  - Return property values.
- Retrieve nodes and relationships from the graph using patterns in the graph.
- Filter queries
- Using the Movies example dataset, you will create and execute Cypher code to find actors and movies in our graph.

## Domain model for this course

Here is the data model used in this course. The graph contains nodes with the labels `Person` and `Movie`. `Person` nodes have several types of relationships to `Movie` nodes. A `Person` node can have a `FOLLOWS` relationship to another `Person` node.

![movie schema](./img/movie-schema.svg)
