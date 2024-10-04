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

## Finding Relationships

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

> Cypher:
>
> ```
> MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m)
> RETURN m.title
> ```

This code returns the titles of all movies that _Tom Hanks_ acted in.

If our graph had different labels, for example _Television_ and _Movie_ nodes this query would have returned all _Television_ and _Movie_ nodes that Tom Hanks acted in. That is, if we had multiple types of nodes at the end of the _ACTED_IN_ relationships in our graph, we could make sure that we only return movies.

> Cypher:
>
> ```
> MATCH (p:Person {name: 'Tom Hanks'})-[:ACTED_IN]->(m:Movie)
> RETURN m.title
> ```

Because our graph only has _Movie_ nodes that have incoming _ACTED_IN_ relationships, this query returns the exact same results as the previous query.

## Filtering queries

this query retrieves the _Person_ nodes and _Movie_ nodes where the person acted in a movie that was released in _2008_ or _2009_:

> Cypher:
>
> ```
> MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
> WHERE m.released = 2008 OR m.released = 2009
> RETURN p, m
> ```

### Filtering by node labels

Returning the names of all people who acted in the movie, _The Matrix_.

> Cypher:
>
> ```
> MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
> WHERE m.title='The Matrix'
> RETURN p.name
> ```

An alternative to this query is the following where we test the node labels in the `WHERE` clause:

> Cypher:
>
> ```
> MATCH (p)-[:ACTED_IN]->(m)
> WHERE p:Person AND m:Movie AND m.title='The Matrix'
> RETURN p.name
> ```

Both queries execute the same way, but you may want to use one style of filtering over another in your code.

### Filtering using ranges

You can specify a range for filtering a query. Here we want to retrieve Person nodes of people who acted in movies released between _2000_ and _2003_:

> Cypher:
>
> ```
> MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
> WHERE 2000 <= m.released <= 2003
> RETURN p.name, m.title, m.released
> ```

### Filtering by existence of a property

Recall that by default, there is no requirement that a node or relationship has a given property. Here is an example of a query where we only want to return _Movie_ nodes where Jack _Nicholson_ acted in the movie, and the movie has the _tagline_ property.

> Cypher:
>
> ```
> MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
> WHERE p.name='Jack Nicholson' AND m.tagline IS NOT NULL
> RETURN m.title, m.tagline
> ```

### Filtering by partial strings

Cypher has a set of string-related keywords that you can use in your `WHERE` clauses to test string property values. You can specify `STARTS WITH`, `ENDS WITH`, and `CONTAINS`.

For example, to find all actors in the graph whose first name is _Michael_, you would write:

> Cypher:
>
> ```
> MATCH (p:Person)-[:ACTED_IN]->()
> WHERE p.name STARTS WITH 'Michael'
> RETURN p.name
> ```

String tests are case-sensitive so you may need to use the `toLower()` or `toUpper()` functions to ensure the test yields the correct results. For example:

> Cypher:
>
> ```
> MATCH (p:Person)-[:ACTED_IN]->()
> WHERE toLower(p.name) STARTS WITH 'michael'
> RETURN p.name
> ```

### Filtering by patterns in the graph

Suppose you wanted to find all people who wrote a movie but did not direct that same movie. Here is how you would perform the query:

> Cypher:
>
> ```
> MATCH (p:Person)-[:WROTE]->(m:Movie)
> WHERE NOT exists( (p)-[:DIRECTED]->(m) )
> RETURN p.name, m.title
> ```

### Filtering using lists

If you have a set of values you want to test with, you can place them in a list or you can test with an existing list in the graph. A Cypher list is a comma-separated set of values within square brackets.

You can define the list in the `WHERE` clause. During the query, the graph engine will compare each property with the values `IN` the list. You can place either numeric or string values in the list, but typically, elements of the list are of the same type of data. If you are testing with a property of a string type, then all the elements of the list will be strings.

In this example, we only want to retrieve _Person_ nodes of people born in _1965_, _1970_, or _1975_:

> Cypher:
>
> ```
> MATCH (p:Person)
> WHERE p.born IN [1965, 1970, 1975]
> RETURN p.name, p.born
> ```

You can also compare a value to an existing list in the graph.

We know that the _:ACTED_IN_ relationship has a property, _roles_ that contains the list of roles an actor had in a particular movie they acted in. Here is the query we write to return the name of the actor who played _Neo_ in the movie _The Matrix_:

> Cypher:
>
> ```
> MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
> WHERE  'Neo' IN r.roles AND m.title='The Matrix'
> RETURN p.name, r.roles
> ```

### What properties does a node or relationship have?

The properties for a node with a given label need not be the same. One way you can discover the properties for a node is to use the `keys()` function. This function returns a list of all property keys for a node.

Discover the keys for the Person nodes in the graph by running this code:

> Cypher:
>
> ```
> MATCH (p:Person)
> RETURN p.name, keys(p)
> ```

The results returned for each row include the name of the person, followed by the list of property keys for that node. If you scroll down in the result pane, you will notice that some Person nodes do not have a born property.

### What properties exist in the graph?

More generally, you can run this code to return all the property keys defined in the graph.

> Cypher:
>
> ```
> CALL db.propertyKeys()
> ```

> [!NOTE]
>
> that a property key remains in the graph, once it has been defined, even if there are currently no nodes or relationships that use that property key.
