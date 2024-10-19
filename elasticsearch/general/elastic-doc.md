# 48

## Setting explicit mapping for "reviews" index

```json
 PUT /reviews
{
  "mappings": {
    "properties": {
      "field": { "type": "text" }
    }
  }
}
```

# 49

## Retrieving mapping for "reviews" index

```bash
GET /reviews/_mapping
```

## Retrieving field mapping for "reviews" index

```bash
GET /reviews/_mapping/field/<field name>
```

# 50

## Setting explicit mapping using dot notation for "reviews" index

```json
PUT /reviews
{
  "mappings": {
    "properties": {
      "field": { "type": "text" }
      "object-field.prop": { "type": "text" }
    }
  }
}
```

# 51

## Adding new fields to index mapping

```json
PUT /reviews/_mapping
{
  "properties": {
    "new_field_name": { "type": "date" }
  }
}
```

## Get documents in reviews

```json
GET /reviews/_search
{
  "query": {
    "match_all": {}
  }
}
```

# 52

## Date formats:

1. Date without time: "2024-05-13"
2. Date with time and without timezone: "2024-05-13T15:00:00Z"
3. Date with time and with timezone: "2024-05-13T15:00:00+01:00"

**Dates must be formatted according to ISO 5601 specification**

# 54

## field parameters ex

```json
PUT /sales
{
  "mappings": {
    "properties": {
      "date-field": {
        "type": "date",
        "format": "dd/MM/yyyy"
      },
      "second-date-field": {
        "type": "date",
        "format": "epoch_second"
      },
      "nested-field": {
        "type": "nested",
        "properties": {
          "name": { "type": "text" }
        }
      },
      "coerce-field": {
        "type": "text",
        "coerce": false
      },
      "second-date-field": {
        "type": "date",
        "format": "epoch_second"
      },
      "doc_value-field": {
        "type": "text",
        "doc_value": false
      },
      "norms-field": {
        "type": "text",
        "norms": false // rank
      },
      "not-index-field": {
        "type": "text",
        "index": false
      },
      "default-value-field": {
        "type": "text",
        "null_value": "default value"
      },
      "copied-field": {
        "type": "text",
        "copy_to": "full_name"
      },
      "first_name": {
        "type": "text",
        "copy_to": "full_name"
      },
      "last_name": {
        "type": "text",
        "copy_to": "full_name"
      },
      "full_name": {
        "type": "text"
      }
    }
  }
}
```

# 55

**Field types in index mapping cannot be edited once added**

# 56

## Reindexing documents from old_index into new_index

```json
POST /_reindex
{
  "source": {
    "index": "old_index"
  },
  "dest": {
    "index": "new_index"
  }
}
```

## Reindexing documents from old_index into new_index AND manipulating old data

```json
POST /_reindex
{
  "source": {
    "index": "old_index"
  },
  "dest": {
    "index": "new_index"
  },
  "script": """
    if (ctx._source.product_id != null) {
      ctx._source.product_id = ctx._source.product_id.toString();
    }
  """
}
```

## Reindexing documents from old_index into new_index which match a query

```json
POST /_reindex
{
  "source": {
    "index": "old_index",
    "query": {
      "match_all": {}
    }
  },
  "dest": {
    "index": "new_index"
  }
}
```

## Reindexing certain fields documents from old_index into new_index

```json
POST /_reindex
{
  "source": {
    "index": "old_index",
    "_source": ["fields_to_include"]
  },
  "dest": {
    "index": "new_index"
  }
}
```

## Reindexing documents from old_index into new_index while renaming some fields

```json
POST /_reindex
{
  "source": {
    "index": "old_index"
  },
  "dest": {
    "index": "new_index"
  },
  "script": {
    "source": """
      # Rename "old_field_name" to "new_field_name"
      ctx._source.new_field_name = ctx._source.remove("old_field_name");
    """
  }
}
```

## Reindexing documents from old_index into new_index while ignoring some documents

```json
POST /_reindex
{
  "source": {
    "index": "old_index"
  },
  "dest": {
    "index": "new_index"
  },
  "script": {
    "source": """
      if (ctx._source.rating < 4.0) {
        ctx.op = "noop"; # Can also be set to "delete"
      }
    """
  }
}
```

# 57

## Specifying alias for an already existing field

```json
PUT /index-name/_mapping
{
  "properties": {
    "comment": {
      "type": "alias",
      "path": "old_field_name"
    }
  }
}
```

# 58

## Creating a multi-field index

```json
PUT /index-name
{
  "mappings": {
    "properties": {
      "description": { "type": "text" },
      "ingredients": {
        "type": "text",
        "fileds": {
          "keyword": {
            "type": "keyword"
          }
        }
      }
    }
  }
}
```

## Retrieving a multi-field index

```json
PUT /index-name/_search
{
  "query": {
    "match": {
      "ingredients": "Spaghetti"
    }
  }
}
```

```json
PUT /index-name/_search
{
  "query": {
    "term": {
      "ingredients.keyword": "Spaghetti"
    }
  }
}
```

# 59

## Creating a template mapping

```json
PUT /_template/access-logs
{
  "index_patterns": ["access-logs-*"],
  "settings": {
    "number_of_shards": 2,
    "index.mapping.coerce": false,
  },
  "mappings": {
    "properties": {
      "@timestamp": {
        "type": "date"
      },
      "url.original": {
        "type": "keyword"
      },
      "http.request.referrer": {
        "type": "keyword"
      },
      "http.response.status_code": {
        "type": "long"
      }
    }
  }
}
```

# 62

## Turning dynamic mapping to false | "strict"

```json
PUT /index-name
{
  "mappings": {
    "dynamic": false,
    "properties": {
      "object-field-with-dynamic-mapping": {
        "dynamic": true,
        "properties": {}
      }
    }
  }
}
```

## Checking numeric types in dynamic mapping when a string is passed

```json
PUT /index-name
{
  "mappings": {
    "numeric_detection": true
  }
}
```

## Disabling date detection

```json
PUT /index-name
{
  "mappings": {
    "date_detection": false
  }
}
```

## Recognizing certain formats as date type only

```json
PUT /index-name
{
  "mappings": {
    "dynamic_date_format": ["dd-MM-yyyy"]
  }
}
```

# 63

## Dynamic template mapping by type

```json
PUT /index-name
{
  "mappings": {
    "dynamic_templates": [
      {
        "integers": {
          "match_mapping_type": "long",
          "mapping": {
            "type": "integer"
          }
        }
      }
    ]
  }
}
```

## Dynamic template mapping by name

```json
PUT /index-name
{
  "mappings": {
    "dynamic_templates": [
      {
        "names": {
          "match_mapping_type": "string",
          "match": "^[a-zA-z]+_name$",
          "unmatch": "*_keyword",
          "match_pattern": "regex",
          "mapping": {
            "type": "text"
          }
        }
      }
    ]
  }
}
```

## Dynamic template mapping by full field path

```json
PUT /index-name
{
  "mappings": {
    "dynamic_templates": [
      {
        "copy_to_full_name": {
          "match_mapping_type": "string",
          "path_match": "employer.name.*",
          "mapping": {
            "type": "text",
            "copy_to": "full_name"
          }
        }
      }
    ]
  }
}
```

## Disabling index by dynamic template mapping

```json
PUT /index-name
{
  "mappings": {
    "dynamic_templates": [
      {
        "no_doc_value": {
          "match_mapping_type": "*",
          "mapping": {
            "type": "{dynamic_type}",
            "index": false
          }
        }
      }
    ]
  }
}
```

# 64

## mapping recommendations

- use _explicit mapping_ and set `dynamic` to `false` if possible
- use keyword type only if you need to use the field in aggregations, sorting or filtering on exact values
- disable coercion
- use appropriate numeric data types
- recommended parameters:
  - set `doc_values` to `false` if you don't need sorting, aggregations and scripting
  - set `norms` to `false` if you don't need relevance scoring
  - set `index` to `false` if you don't need to filter on values
  - these recommendations are for million of documents otherwise these are most likely just overcomplication

# 65

## Stemming

The process of transferring words into their root form

## Stop Words

Words which are removed from text fields during the text analysis, like: "a", "the', "at", etc

# 69

## Changing analyzer settings

```json
PUT /index-name/_settings
{
  "analysis": {
    "analyzer": {
      "my_second_analyzer": {
        "type": "custom",
        "tokenizer": "standard",
        "char_filter": ["html_strip"],
        "filter: [
          "lowercase",
          "stop",
          "asciifolding"
        ]
      }
    }
  }
}
```

## Open vs Closed index

### Open Index

Open index is reffered to an index which accepts requests.

### Closed Index

Closed index is reffered to an index which dosen't accepts requests.

## Static vs Dynamic settings

### Static Settings

Static settings are settings which require the index to be closed

### Dynamic Settings

Dynamic settings are settings which do not require the index to be closed

## Closing the index

```bash
POST /index-name/_close
```

## Opening the index

```bash
POST /index-name/_open
```

# 70

## Reindexing documents in the same index

```bash
POST /index-name/_update_by_query?conflicts=proceed
```

# 72

## Searching using query parameters

**Example**

```bash
GET /index-name/_search?q=field:value AND field2: value2
```

## Searching using

**Example**

```json
GET /products/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "kiarash"
          }
        },
        {
          "match": {
            "tags": "wine"
          }
        }
      ]
    }
  }
}
```

# 73

## The term query for exact text search

**This should never be used with the text data type**

```json
GET /index-name/_search
{
  "query": {
    "term": {
      "brand.keyword": "Nike"
    }
  }
}
```

# 74

## The term query with some params

```json
GET /index-name/_search
{
  "query": {
    "term": {
      "tags.keyword": {
        "value": "vegetable",
        "case_insensetive": true
      }
    }
  }
}
```

## The terms query comparing egainst two texts

```json
GET /index-name/_search
{
  "query": {
    "terms": {
      "tags.keyword": ["cat", "dog"]
    }
  }
}
```

# 75

## Retrieving multiple documents by their \_id field

```json
GET /index-name/_search
{
  "query": {
    "ids": {
      "values": ["id1", "id2", "id3"]
    }
  }
}
```

# 76

## Range query

used with **numeric** and **date** types

```json
GET /index-name/_search
{
  "query": {
    "range": {
      "in_stock": {
        "gte": 1,
        "lte": 5
      }
    }
  }
}
```

## Range query with a different date format

```json
GET /index-name/_search
{
  "query": {
    "range": {
      "created_at": {
        "format": "dd/MM/",
        "gte": "2024/01/01 01:00:00",
        "gte": "2024/02/01 01:59:59"
      }
    }
  }
}
```

## Range query with a timezone

```json
GET /index-name/_search
{
  "query": {
    "range": {
      "created_at": {
        "time_zone": "+01:00",
        "gte": "2024/01/01 01:00:00",
        "gte": "2024/02/01 01:59:59"
      }
    }
  }
}
```

# 77

## Searching by the beginning of a text field

```json
GET /index-name/_search
{
  "query": {
    "prefix": {
      "name.keyword": {
        "value": "Past"
      }
    }
  }
}
```

**Example matchs of the above query**

1. "Pasta - linguini Dry"
2. "Paste - Black Olive"
3. "Pastry - Baked Scones - Mini"
4. "linguini Pasta"

## Searching using Wildcards

### Wildcard #1 `?`

The `?` wildcard matchs with any one character where it is placed. There must be one character where the `?` is placed to match this wildcard.

```json
GET /index-name/_search
{
  "query": {
    "wildcard": {
      "tags.keyword": {
        "value": "Past?"
      }
    }
  }
}
```

**Example match case:** Paste, Pasta

### Wildcard #2 `*`

The `*` wildcard matchs with any character Before/After where it is placed.

```json
GET /index-name/_search
{
  "query": {
    "wildcard": {
      "tags.keyword": {
        "value": "Bee*"
      }
    }
  }
}
```

**Example match case:** Bee, Beer, Bee in the hive

### Wildcard #3 `regexp`

The `*` wildcard matchs with any character Before/After where it is placed.

```json
GET /index-name/_search
{
  "query": {
    "regexp": {
      "tags.keyword": {
        "value": "Bee(f|t)+"
      }
    }
  }
}
```

**Example match case:** Beet, Beef

# 78

## Get all documents where a certain field exists (is not null)

```json
GET /index-name/_search
{
  "query": {
    "exists": {
      "field": "tags.keyword"
    }
  }
}
```

## Empty values in elastic

- NULL
- empty array []

## Searching for documents which do not have a field

```json
GET /index-name/_search
{
  "query": {
    "bool": {
      "must_not": [
        {
          "exists": {
            "field": "tags.keyword"
          }
        }
      ]
    }
  }
}
```

# 80

## Full text search

Should not be used for _keyword_ types

```json
GET /index-name/_search
{
  "query": {
    "match": {
      "field": "value"
    }
  }
}
```

# 81

## Requiring all tokens to appear in the text found

Matching **pasta** OR **chicken**

```json
GET /index-name/_search
{
  "query": {
    "match": {
      "name": "PASTA CHICKEN"
    }
  }
}
```

Matching **pasta** AND **chicken**

```json
GET /index-name/_search
{
  "query": {
    "match": {
      "name": "PASTA CHICKEN",
      "operator": "AND"
    }
  }
}
```

# 83

## Multi matching

```json
GET /index-name/_search
{
  "query": {
    "multi_match": {
      "query": "vegetable",
      "fields": ["name", "tags"]
    }
  }
}
```

## Multi matching while specifying relevance

Searching with name being 2 times as relevant as the tags

```json
GET /index-name/_search
{
  "query": {
    "multi_match": {
      "query": "vegetable",
      "fields": ["name^2", "tags"]
    }
  }
}
```

## Multi matching while multiplying all fields relevance

```json
GET /index-name/_search
{
  "query": {
    "multi_match": {
      "query": "vegetable",
      "fields": ["name", "tags"],
      "tie_breaker": 0.3
    }
  }
}
```

# 84

## Searching for phrase

When using match phrase the text field must contain all of the specified search tokens and in the same order as search is happening

```json
GET /index-name/_search
{
  "query": {
    "match_phrase": {
      "description": "Complete Guide to Elasticsearch"
    }
  }
}
```

**Analyzed version**: ["complete", "guide", "to", "elasticsearch"]

# 87

## Bool compound query

- Any text matched in `should` section is not required to be matched with but it increases the relevance score
- `filter` pipe acts exactly like `must` pipe except it ignores relevance scoring

```json
{
  "query": {
    "bool": {
      "must": [
        {
          "term": {
            "tags.keyword": "Alcohol"
          }
        }
      ],
      "must_not": [
        {
          "term": {
            "tags.keyword": "Wine"
          }
        }
      ],
      "should": [
        {
          "term": {
            "tags.keyword": "Beer"
          }
        },
        {
          "match": {
            "name": "beer"
          }
        },
        {
          "match": {
            "description": "beer"
          }
        }
      ],
      "minimum_should_match": 1,
      "filter": [
        {
          "term": {
            "tags.keyword": "Alcohol"
          }
        }
      ]
    }
  }
}
```

# 88

## Query vs Filter context

### Query Context

Has relevance scoring

### Filter Context

Does not have relevance scoring

# 89

## Chnaging the number of documents returned in one search

```json
GET /index-name/_search
{
  "size": 20,
  "query": {
    "match_all": {}
  }
}
```

## Boosting query

```json
GET /index-name/_search
{
  "query": {
    "boosting": {
      "positive": {
        {
          "name": "juice"
        }
      },
      "negative": {
        {
          "name": "apple"
        }
      },
      "negative_boost": 0.5
    }
  }
}
```

# 90

## `dis_max` Query

```json
GET /index-name/_search
{
  "query": {
    "dis_max": {
      "queries": [
        { "match": { "name": "vegetable" } },
        { "match": { "tags": "vegetable" } }
      ],
      "tie_breaker": 0.3 // 0.0 is the defalut
    }
  }
}
```

# 91

## Querying nested data types

```json
GET /index-name/_search
{
  "qeury": {
    "nested": {
      "path": "path.to.nested.field",
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "path.to.nested.field.search_field_name": "value"
              }
            }
          ]
        }
      }
    }
  }
}
```

# 92

## Querying nested data types and highlighting the nested document(s) which matched our query

```json
GET /index-name/_search
{
  "qeury": {
    "nested": {
      "path": "path.to.nested.field",
      "inner_hits": {},
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "path.to.nested.field.search_field_name": "value"
              }
            }
          ]
        }
      }
    }
  }
}
```

## `Inner_hits` Additional parameters

```json
GET /index-name/_search
{
  "qeury": {
    "nested": {
      "path": "path.to.nested.field",
      "inner_hits": {
        "name": "custom_name",
        "size": 20
      },
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "path.to.nested.field.search_field_name": "value"
              }
            }
          ]
        }
      }
    }
  }
}
```

# 96

## Adding mapping for a join field

```json
PUT /index-name
{
  "mappings": {
    "_doc": {
      "properties": {
        "join_field_name": {
          "type": "join",
          "relations": {
            "parent_name": "child_name"
          }
        }
      }
    }
  }
}
```

# 98

## Adding to join index

Adding a department

```json
PUT /department/_doc/1
{
  "name": "Development",
  "join_field_name": "department"
}
```

Adding an employee

```json
PUT /department/_doc/1?routing=1
{
  "name": "Kiarash",
  "age": 21,
  "gender": "M",
  "join_field_name": {
    "name": "employee",
    "parent": 1
  }
}
```

The routing above is the parent id

# 99

## Query by parent id

```json
GET /department/_search
{
  "query": {
    "parent_id": {
      "type": "employee",
      "id": 1
    }
  }
}
```

# 100

## Query child documents by parent

```json
GET /index-name/_search
{
  "query": {
    "has_parent": {
      "parent_type": "parent_name",
      "score": true, // set for better relevance scoring
      "query": {
        "name.keyword": "value"
      }
    }
  }
}
```

# 101

## Query parent by child document

```json
GET /index-name/_search
{
  "query": {
    "has_child": {
      "type": "child_name",
      "score_mode": "sum",
      "query": {
        "bool": {
          "must": [
            {
              "range": {
                "age": {
                  "lte": 30
                }
              }
            }
          ]
        }
      }
    }
  }
}
```

### Score modes for this query

- min: the lowest score of the matching documents is mapped into the parent
- min: the highest score of the matching documents is mapped into the parent
- sum: the matching children's scores are summed up and mapped into the parent
- avg: the average score based on maching child documents is mapped into the parent
- none: score of child documents are ignored (default value)

# 102

## Creating multi-level relations

```json
PUT /company
{
  "mappings": {
    "_doc": {
      "properties": {
        "join_field": {
          "type": "join",
          "relations": {
            "company": ["department", "supplier"],
            "department": "employee"
          }
        }
      }
    }
  }
}
```

## Adding documents to the above index

Adding a **company**

```json
PUT /company/_doc/1
{
  "name": "My Company Inc",
  "join_field": "company"
}
```

Adding a **department**

```json
PUT /company/_doc/2?routing=1
{
  "name": "Development",
  "join_field": {
    "name": "department",
    "parent": 1
  }
}
```

Adding an **employee**

The parent of the employee must be the company it belongs to so that the employee is saved on the same shard as the first parent

```json
PUT /company/_doc/3?routing=1
{
  "name": "John Doe",
  "join_field": {
    "name": "employee",
    "parent": 2
  }
}
```

## Querying for departments

```json
GET /company/_search
{
  "query": {
    "has_child": {
      "type": "department",
      "query": {
        "has_child": {
          "type": "employee",
          "query": {
            "term": {
              "name.keyword": "John Doe"
            }
          }
        }
      }
    }
  }
}
```

# 104

## Terms lookup

```json
GET /stories/_search
{
  "query": {
    "terms": {
      "user": {
        "index": "users",
        "type": "_doc",
        "id": 1,
        "path": "following"
      }
    }
  }
}
```

# 109

## controlling the output fields

```json
GET /index-name/_search
{
  "_source": false,
  "query": {
    ...
  }
}
```

```json
GET /index-name/_search
{
  "_source": "field_to_include",
  "query": {
    ...
  }
}
```

```json
GET /index-name/_search
{
  "_source": ["fields", "to", "include"],
  "query": {
    ...
  }
}
```

```json
GET /index-name/_search
{
  "_source": {
    "includes": "",
    "excludes": ""
  },
  "query": {
    ...
  }
}
```

# 110

## Specifying size for returned documents

```json
GET /index-name/_search?size=20
{
  ...
}
```

```json
GET /index-name/_search
{
  "size": 20,
  ...
}
```

# 111

## Fetching after an offset

```json
GET /index-name/_search?size=20
{
  "from": 20,
  ...
}
```

# 112

## Implementing pagination

- total_pages = ceil(total_hits / page_size)
- from = (page_size \* (page_number - 1))

# 113

## Sorting documents

```json
GET /index-name/_search
{
  "query": {
    "match_all": {}
  },
  "sorting": [
    "field_name"
  ]
}
```

```json
GET /index-name/_search
{
  "query": {
    "match_all": {}
  },
  "sorting": [
    { "some_other_field": "desc" },
    { "createdAt": "desc" }
  ]
}
```

# 114

## Sorting array documents

```json
GET /index-name/_search
{
  "query": {
    "match_all": {}
  },
  "sorting": [
    {
      "ratings": {
        "order": "desc",
        "mode": "avg"
      }
    }
  ]
}
```

### Sorting modes

- sum
- avg
- min
- max

# 116

## Metric aggregations

### Sum aggregation

The `stats` aggregation returns the result of the following aggregations:

- count
- min
- max
- avg
- sum

```json
GET /index-name/_search
{
  "size": 0,
  "aggs": {
    "total_sales": {
      "sum": {
        "field": "total_amount"
      }
    },
    "average_sales": {
      "avg": {
        "field": "total_amount"
      }
    },
    "min_sale": {
      "min": {
        "field": "total_amount"
      }
    },
    "max_sale": {
      "max": {
        "field": "total_amount"
      }
    },
    "total_salesmen": {
      "cardinality": {
        "field": "salesman.id"
      }
    },
    "values_count": {
      "value_count": {
        "field": "total_amount"
      }
    },
    "amount_stats": {
      "stats": {
        "field": "total_amount"
      }
    }
  }
}
```

# 117

## Bucket aggregations

```json
GET /index-name/_search
{
  "aggs": {
    "bucket_field_name": {
      "terms": {
        "field": "field_name",
        "missing": "N/A",
        "min_doc_count": 0,
        "order": {
          "_key": "desc"
        }
      }
    }
  }
}
```

# 119

## Nested aggregations

```json
GET /index-name/_search
{
  "aggs": {
    "bucket_field_name": {
      "terms": {
        "field": "field_name",
        "missing": "N/A",
        "min_doc_count": 0,
        "order": {
          "_key": "desc"
        },
        "aggs": {
          "metrics": {
            "stats": "total_amount"
          }
        }
      }
    }
  }
}
```

# 120

## Filtering documents in an aggregation

```json
GET /index-name/_search
{
  "aggs": {
    "low_value": {
      "filter": {
        "range": {
          "total_amount": {
            "lte": 50
          }
        }
      },
      "aggs": {
        "avg_amount": {
          "avg": "total_amount"
        }
      }
    }
  }
}
```

# 121

## Defining bucket rules with filters

```json
GET /index-name/_search
{
  "aggs": {
    "my_filter": {
      "filters": {
        "filters": {
          "pasta": {
            "match": {
              "title": "pasta"
            }
          },
          "spaghetti": {
            "match": {
              "title": "spaghetti"
            }
          }
        }
      },
      "aggs": {
        "avg_amount": {
          "avg": "total_amount"
        }
      }
    }
  }
}
```

# 122

## Defining bucket rules with filters

```json
GET /index-name/_search
{
  "aggs": {
    "amount_distribution": {
      "range": {
        "field": "total_amount",
        "range": [
          {
            "to": "50"
          },
          {
            "from": "50",
            "to": "100"
          },
          {
            "from": "100"
          }
        ]
      }
    }
  }
}
```

## Defining bucket rules with filters (date)

```json
GET /index-name/_search
{
  "aggs": {
    "amount_distribution": {
      "date_range": {
        "field": "purchased_at",
        "format": "yyyy-MM-dd", // optional
        "keyed": true, // optional
        "range": [
          {
            "from": "2016-01-01",
            "to": "2016-01-011||+6M",
            "key": "first_half"  // optional
          },
          {
            "from": "2016-01-011||+6M",
            "to": "2016-01-011||+1y",
            "key": "second_half"  // optional
          }
        ]
      },
      "aggs": {
        "bucket_stats": {
          "stats": {
            "field": "total_amount"
          }
        }
      }
    }
  }
}
```

# 123

## Histogram for number values

```json
GET /index-name/_search
{
  "aggs": {
    "amount_distribution": {
      "histogram": {
        "field": "total_amount",
        "interval": 25,
        "min_doc_count": 0,
        "extended_bounds": {
          "min": 0,
          "max": 500
        }
      }
    }
  }
}
```

## Histogram for date values

```json
GET /index-name/_search
{
  "aggs": {
    "orders_over_time": {
      "date_histogram": {
        "field": "peu",
        "interval": 25,
        "min_doc_count": 0,
        "extended_bounds": {
          "min": 0,
          "max": 500
        }
      }
    }
  }
}
```

# 124

## Global aggregation

```json
GET /index-name/default/_search
{
  "query": {
    "range": {
      "total_amount": {
        "gte": 100
      }
    }
  },
  "size": 0,
  "aggs": {
    "stats_expensive": {
      "stats": {
        "field": "total_amount"
      }
    },
    "all_orders": {
      "global": {},
      "aggs": {
        "stats_amount": {
          "stats": {
            "field": "total_amount"
          }
        }
      }
    }
  }
}
```

# 125

## Aggregating documents which do not have a cerain field

```json
GET /index-name/default/_search
{
  "size": 0,
  "aggs": {
    "orders_without_status": {
      "missing": {
        "field": "status"
      }
    }
  }
}
```

# 126

## Nested aggregation

```json
GET /index-name/default/_search
{
  "size": 0,
  "aggs": {
    "employees": {
      "nested": {
        "path": "employees"
      },
      "aggs": {
        "minimum_age": {
          "min": {
            "field": "employees.age"
          }
        }
      }
    }
  }
}
```

# 128

## Proximity search

```json
GET /index-name/_search
{
  "query": {
    "match_phrase": {
      "title": {
        "query": "spicy sauce",
        "slop": 1
      }
    }
  }
}
```

# 129

## Effecting scores using proximity

```json
GET /index-name/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "spicy sauce"
          }
        }
      ],
      "should": [
        {
          "match_phrase": {
            "title": {
              "query": "spicy sauce",
              "slop": 5
            }
          }
        }
      ]
    }
  }
}
```

# 130

## Ignoring user bad input by fuzzy search

- fuzziness `auto` mode:
  - 1-2: 0 key distance
  - 3-5: 1 key distance
  - \>5: 2 key distance

```json
GET /index-name/_search
{
  "query": {
    "match": {
      "name": {
        "query": "lobster",
        "fuzziness": "auto"
      }
    }
  }
}
```

## Ignoring user bad input by fuzzy search

transposition => lvie == live

```json
GET /index-name/_search
{
  "query": {
    "match": {
      "name": {
        "query": "lobster",
        "fuzziness": "auto",
        "fuzzy_transposition": false
      }
    }
  }
}
```

# 131

## Fuzzy query

```json
GET /index-name/_search
{
  "query": {
    "fuzzy": {
      "name": {
        "value": "LOBSTER",
        "fuzziness": "auto"
      }
    }
  }
}
```

# 134

## Highlighting matchs

```json
GET /index-name/default/_search
{
  "query": {
    "match": {
      "description": "Elasticsearch story"
    }
  },
  "highlight": {
    "fields": {
      "description": {}
    }
  }
}
```

## Highlighting matchs with custom pre/post fix

```json
GET /index-name/default/_search
{
  "query": {
    "match": {
      "description": "Elasticsearch story"
    }
  },
  "highlight": {
    "fields": {
      "pre_tags": ["<strong>"],
      "post_tags": ["<strong>"],
      "description": {}
    }
  }
}
```
