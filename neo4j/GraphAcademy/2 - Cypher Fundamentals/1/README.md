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

## What is Cypher?

Cypher is a query language designed for graphs.

Just as we would draw circles and arrows on a whiteboard, we write out the pattern in Cypher:

- Nodes are represented by parentheses `()`.
- We use a colon to signify the label(s), for example `(:Person)`.
- Relationships between nodes are written with two dashes, for example `(:Person)--(:Movie)`.
- The direction of a relationship is indicated using a greater than or less than symbol `<` or `>` , for example `(:Person)-→(:Movie)`.
- The type of the relationship is written using the square brackets between the two dashes: `[` and `]`, for example `[:ACTED_IN]`
- Properties drawn in a _speech bubble_ are specified in a JSON like syntax.
  - Properties in Neo4j are key/value pairs, for example `{name: 'Tom Hanks'}`.

For example, a Cypher pattern in the graph could be:

> Partial:
>
> ```
> // example Cypher pattern
> (m:Movie {title: 'Cloud Atlas'})<-[:ACTED_IN]-(p:Person)
> ```

The two node types in this pattern are `Movie` and `Person`. The `Person` nodes have a directed `ACTED_IN` relationship to `Movie` nodes. The specific `Movie` node in this pattern is filtered by the `title` property with a value of `Cloud Atlas`. So this pattern represents all people in the graph who acted in the movie, `Cloud Atlas`.

### How Cypher workss

Cypher works by matching patterns in the data. We retrieve data from the graph using the `MATCH` keyword. You can think of the `MATCH` clause as similar to the FROM clause in an SQL statement.

For example, if we want to find a _Person_ in the graph, we would `MATCH` a pattern of a single node with a label of `:Person` - prefixed with a colon `:`.

> Partial:
>
> ```
> MATCH (:Person)
> // incomplete MATCH clause because we need to return somethin
> ```

Suppose we want to retrieve all _Person_ nodes from the graph. We can assign a variable by placing a value before the colon. Let’s use the variable `p`. Now that `p` represents all Person nodes retrieved from the graph, we can return them using the `RETURN` clause.

Run this Cypher code:

> Cypher:
>
> ```
> MATCH (p:Person)
> RETURN p
> ```

This query returns all nodes in the graph with the _Person_ label.

Now, say we want to find the node which represents the _Person_ who’s name is _Tom Hanks_. Our Person nodes all have a name property. We can use the _braces_ `{..}` to specify the key/value pair of name and _Tom Hanks_ as the filter. As _Tom Hanks_ is a string, we will need to place it inside single or double quotes.

> Cypher:
>
> ```
> MATCH (p:Person {name: 'Tom Hanks'})
> RETURN p
> ```

This query returns a single node that represents _Tom Hanks_.

In our Cypher statement, we can access properties using a _dot notation_. For example, to return the _name_ property value using its property key `p.name`.

> Cypher:
>
> ```
> MATCH (p:Person {name: 'Tom Hanks'})
> RETURN  p.born
> ```

This query returns the value of the _born_ property of the _Tom Hanks_ node.

> In Cypher, labels, property keys, and variables are case-sensitive. Cypher keywords are not case-sensitive.
>
> Neo4j best practices include:
>
> - Name labels using CamelCase.
> - Name property keys and variables using camelCase.
> - User UPPERCASE for Cypher keywords.

Another way that you can filter queries is by using the `WHERE` clause, rather than specifying the property value inline with braces.

This query returns the same data as the previous query.

> Cypher:
>
> ```
> MATCH (p:Person)
> WHERE p.name = 'Tom Hanks'
> RETURN p.born
> ```

As you gain more experience with Cypher, you will find that using `WHERE` to filter your queries is very powerful because you can add more logic to your `WHERE` clause. Here is an example where we filter by two values for _name_.

> Cypher:
>
> ```
> MATCH (p:Person)
> WHERE p.name = 'Tom Hanks' OR p.name = 'Rita Wilson'
> RETURN p.name, p.born
> ```

This query returns two names and their associated birth years.

# Finding Relationships

In the previous lesson, we used the `MATCH` clause to find the node in our database that represented _Tom Hanks_.

> Cypher:
>
> ```
> MATCH (p:Person {name: 'Tom Hanks'})
> RETURN p
> ```

We can extend the pattern in the `MATCH` clause to traverse through all relationships with a type of _ACTED_IN_ to any node. Our domain model shows that the _ACTED_IN_ relationship goes in an outgoing direction from the _Person_ node so we can add the direction in our pattern. We often refer to this as a **traversal**.

> Cypher: Incomplete code
>
> ```
> MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->()
> ```

Our data model dictates that the node at the other end of that relationship will be _Movie_ node, so we don’t necessarily need to specify the _:Movie_ label in the node - instead we will use the variable _m_.

> Cypher: Incomplete code
>
> ```
> MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m)
> RETURN m.title
> ```

This code returns the titles of all movies that _Tom Hanks_ acted in.

If our graph had different labels, for example _Television_ and _Movie_ nodes this query would have returned all _Television_ and _Movie_ nodes that Tom Hanks acted in. That is, if we had multiple types of nodes at the end of the _ACTED_IN_ relationships in our graph, we could make sure that we only return movies.

> Cypher: Incomplete code
>
> ```
> MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m:Movie)
> RETURN m.title
> ```

Because our graph only has _Movie_ nodes that have incoming _ACTED_IN_ relationships, this query returns the exact same results as the previous query.
