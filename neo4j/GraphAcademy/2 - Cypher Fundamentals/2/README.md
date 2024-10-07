# Writing Data to Neo4j

In this module you will learn how to update the graph using Cypher and the Movies example dataset.

Summary:

- Use `MERGE` to create nodes in the graph.
- Use `MERGE` to create relationships in the graph.
- Create, update and remove properties for nodes and relationships in the graph.
- Perform conditional `MERGE` processing, depending on what is in the graph.
- Delete nodes and relationships from the graph.

## Domain model for this course

Again, here is the domain model and how it is represented in our graph:

![movie schema](./img/movie-schema.svg)

## Creating nodes

We use the `MERGE` keyword to create a _pattern_ in the database.

After the `MERGE` keyword, we specify the pattern that we want to create. Usually this will be a single node or a relationship between two nodes.

Suppose we want to create a node to represent _Michael Caine_. Run this Cypher code to create the node.

> Cypher:
>
> ```cypher
> MERGE (p:Person {name: 'Michael Caine'})
> ```

It creates a single node in the graph. Note that when you use `MERGE` to create a node, you must specify at least one property that will be the unique primary key for the node.

Verify that the node was created.

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Michael Caine'})
> RETURN p
> ```

### Executing multiple Cypher clauses

We can also chain multiple `MERGE` clauses together within a single Cypher code block.

> Cypher:
>
> ```cypher
> MERGE (p:Person {name: 'Katie Holmes'})
> MERGE (m:Movie {title: 'The Dark Knight'})
> RETURN p, m
> ```

This code creates two nodes, each with a primary key property. Because we have specified the variables _p_ and _m_, we can use them in the code to return the created nodes.

### Using CREATE instead of MERGE to create nodes

Cypher has a `CREATE` clause you can use for creating nodes. The benefit of using `CREATE` is that it does not look up the primary key before adding the node. You can use `CREATE` if you are sure your data is clean and you want greater speed during import. We use `MERGE` in this training because it eliminates duplication of nodes.

## Creating a relationship between two nodes

Just like you can use `MERGE` to create nodes in the graph, you use `MERGE` to create relationships between two nodes. First you must have references to the two nodes you will be creating the relationship for. When you create a relationship between two nodes, it must have:

- Type
- Direction

For example, if the Person and Movie nodes both already exist, we can find them using a `MATCH` clause before creating the relationship between them.

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Michael Caine'})
> MATCH (m:Movie {title: 'The Dark Knight'})
> MERGE (p)-[:ACTED_IN]->(m)
> ```

Here we find the two nodes that we want to create the relationship between. Then we use the reference to the found nodes to create the _ACTED_IN_ relationship.

We can confirm that this relationship exists as follows:

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Michael Caine'})-[:ACTED_IN]-(m:Movie {title: 'The Dark Knight'})
> RETURN p, m
> ```

By default, in Neo4j Browser, the visualization connects nodes that have relationships between them.

Notice also that you need not specify direction in the `MATCH` pattern since the query engine will look for all nodes that are connected, regardless of the direction of the relationship.

For example, if we specified this relationship pattern:

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Michael Caine'})<-[:ACTED_IN]-(m:Movie {title: 'The Dark Knight'})
> RETURN p, m
> ```

This query returns no nodes since there are no nodes with the _ACTED_IN_ relationship to _Person_ nodes in the graph.

### Creating nodes and relationships using multiple clauses

We can also chain multiple `MERGE` clauses together within a single Cypher code block.

> Cypher:
>
> ```cypher
> MERGE (p:Person {name: 'Chadwick Boseman'})
> MERGE (m:Movie {title: 'Black Panther'})
> MERGE (p)-[:ACTED_IN]-(m)
> ```

This code creates two nodes and a relationship between them. Because we have specified the variables _p_ and _m_, we can use them in the code to create the relationship between the two nodes.

> [!NOTE]
>
> Note that in this `MERGE` clause where we create the relationships, we did not specify the direction of the relationship. By default, if you do not specify the direction when you create the relationship, it will always be assumed left-to-right.

We can confirm that this relationship exists as follows:

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Chadwick Boseman'})-[:ACTED_IN]-(m:Movie {title: 'Black Panther'})
> RETURN p, m
> ```

### Using MERGE to create nodes and a relationship in single clause

What `MERGE` does is create the node or relationship if it does not exist in the graph.

This code successfully creates the nodes and relationship:

> Cypher:
>
> ```cypher
> MERGE (p:Person {name: 'Emily Blunt'})-[:ACTED_IN]->(m:Movie {title: 'A Quiet Place'})
> RETURN p, m
> ```

You can execute this Cypher code multiple times and it will not create any new nodes or relationships.

## Updating Properties Section

### Adding properties for a node or relationship

There are two ways that you can set a property for a node or relationship.

#### 1. Inline as part of the MERGE clause

You have already seen how to create the primary key property for a node. You can also set a property for a relationship inline as follows:

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Michael Caine'})
> MERGE (m:Movie {title: 'Batman Begins'})
> MERGE (p)-[:ACTED_IN {roles: ['Alfred Penny']}]->(m)
> RETURN p,m
> ```

In this code, the actor, _Michael Caine_ exists but the movie, _Batman Begins_ does not. We find the _Person_ node and we create the _Movie_ node. Then, we create the _ACTED_IN_ relationship between the _Michael Caine_ node and the newly-created _Batman Begins_ node. And we set the roles property for this relationship to an array of values - containing one value, _Alfred Penny_. Notice that for inline property setting, we use the JSON-style of adding the property key/value pairs in braces `{ .. }`, just like we did when we specified the property for the node.

#### 2. Using the SET keyword for a reference to a node or relationship

We also have the option to use the `SET` keyword for setting a property value. In the context of particular `MERGE` or `MATCH` clause where you have defined a variable to reference the node or relationship, you can set property values.

> Cypher:
>
> ```cypher
> MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
> WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
> SET r.roles = ['Alfred Penny']
> RETURN p, r, m
> ```

##### Setting multiple properties

If you need to set multiple properties, you separate them with a comma (,). For example:

> Cypher:
>
> ```cypher
> MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
> WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
> SET r.roles = ['Alfred Penny'], m.released = 2008
> RETURN p, r, m
> ```

### Updating properties

If you have a reference to a node or relationship, you can also use SET to modify the property. For example, if we wanted to modify Michael Caine’s role to be something different, we could do the following:

> Cypher:
>
> ```cypher
> MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
> WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
> SET r.roles = ['Mr. Alfred Penny']
> RETURN p, r, m
> ```

### Removing properties

You can remove or delete a property from a node or relationship by using the `REMOVE` keyword, or setting the property to `null`.

Here we remove the roles property of this relationship:

> Cypher:
>
> ```cypher
> MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
> WHERE p.name = 'Michael Caine' AND m.title = 'The Dark Knight'
> REMOVE r.roles
> RETURN p, r, m
> ```

Here we remove the born property from an actor:

> Cypher:
>
> ```cypher
> MATCH (p:Person)
> WHERE p.name = 'Gene Hackman'
> SET p.born = null
> RETURN p
> ```

> [!CAUTION]
>
> You should never remove the property that is used as the primary key for a node.

## Merge Processing

You can use `MERGE` to create nodes and relationships in the graph. `MERGE` operations work by first trying to find a pattern in the graph. If the pattern is found then the data already exists and is not created. If the pattern is not found, then the data can be created.

### Customizing MERGE behavior

You can also specify behavior at runtime that enables you to set properties when the node is created or when the node is found. We can use the `ON CREATE SET` or `ON MATCH SET` conditions, or the `SET` keywords to set any additional properties.

> Cypher:
>
> ```cypher
> // Find or create a person with this name
> MERGE (p:Person {name: 'McKenna Grace'})
>
> // Only set the `createdAt` property if the node is created during this query
> ON CREATE SET p.createdAt = datetime()
>
> // Only set the `updatedAt` property if the node was created previously
> ON MATCH SET p.updatedAt = datetime()
>
> // Set the `born` property regardless
> SET p.born = 2006
>
> RETURN p
> ```

If you want to set multiple properties for an ON CREATE SET or ON MATCH SET clause, you separate them by commas. For example:

```cypher
ON CREATE SET m.released = 2020, m.tagline = `A great ride!'
```

### Merging with relationships

You can use `MERGE` to create nodes or relationships:

> Cypher:
>
> ```cypher
> // Find or create a person with this name
> MERGE (p:Person {name: 'Michael Caine'})
>
> // Find or create a movie with this title
> MERGE (m:Movie {title: 'The Cider House Rules'})
>
> // Find or create a relationship between the two nodes
> MERGE (p)-[:ACTED_IN]->(m)
> ```

Another way your can create these nodes and relationship is as follows:

> Cypher:
>
> ```cypher
> MERGE (p:Person {name: 'Michael Caine'})-[:ACTED_IN]->(m:Movie {title: 'The Cider House Rules'})
> RETURN p, m
> ```

Here is what happens in the query processor:

1. Neo4j will attempt to find a Person node with the name Michael Caine.

2. If it does not exist, it creates the node.

3. Then, it will attempt to expand the ACTED_IN relationships in the graph for this node.

4. If there are any ACTED_IN relationships from this node, it looks for a Movie with the title 'The Cider House Rules'.

5. If there is no node for the Movie, it creates the node.

6. If there is no relationship between the two nodes, it then creates the ACTED_IN relationship between them.

## Deleting Data

In a Neo4j database you can delete:

- nodes
- relationships
- properties
- labels

To delete any data in the database, you must first retrieve it, then you can delete it. You have already learned how to remove or delete properties from nodes or relationships.

### Deleting a node

You delete a node as follows where you first retrieve the node. Then with a reference to the node you can delete it.

> Cypher:
>
> ```cypher
> MATCH (p:Person)
> WHERE p.name = 'Jane Doe'
> DELETE p
> ```

### Deleting a relationship

To remove the relationship we retrieve the relationship and delete it.

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Jane Doe'})-[r:ACTED_IN]->(m:Movie {title: 'The Matrix'})
> DELETE r
> RETURN p, m
> ```

> [!WARNING]
> If we attempt to delete a node with a relationship, we will receive an error!
>
> Neo4j prevents orphaned relationships in the graph.

### Deleting a node and its relationships

Neo4j provides a feature where you cannot delete a node if it has incoming or outgoing relationships. This prevents the graph from having orphaned relationships.

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Jane Doe'})
> DETACH DELETE p
> ```

This code deletes the relationship and the _Person_ node.

> [!WARNING]
> You should only do this on relatively small databases as trying to do this on a large database will exhaust memory.

### Deleting labels

A best practice is to have at least one label for a node.

run this code to add a new label to this node:

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Jane Doe'})
> SET p:Developer
> RETURN p
> ```

To remove the newly-added label, Developer, you use the `REMOVE` clause. Run this code:

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Jane Doe'})
> REMOVE p:Developer
> RETURN p
> ```

> [!NOTE]
> You could have specified `MATCH (p:Developer {name: 'Jane Doe'})` or `MATCH (p:Person:Developer {name: 'Jane Doe'})` to find the same node

Delete the Jane Doe node by running this code:

> Cypher:
>
> ```cypher
> MATCH (p:Person {name: 'Jane Doe'})
> DETACH DELETE p
> ```

> [!NOTE]
> Note that `DELETE p` would also work in this case since we have not created any relationships.

### What labels exist in the graph?

This code returns all node labels defined in the graph.

> Cypher:
>
> ```cypher
> CALL db.labels()
> ```

## Course Summary

In this course, you have learned how to:

- Write Cypher code to query the database.

- Write Cypher code to modify the database.

- Use some Cypher statement best practices.
