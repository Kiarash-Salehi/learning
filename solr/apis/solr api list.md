### user API:

#### 1. Query API

**Endpoint**: `/select`
**HTTP Method**: `GET` (commonly used for querying)

#### Actions and Query Options

- **Standard Search**
  - **Query Option**: `q` - Query string, searching across default search field or fields specified in `df`.
  - **Query Option**: `fq` - Filter query to refine the search results without affecting scoring.
  - **Query Option**: `sort` - Specifies the field(s) and order to sort by.
  - **Query Option**: `rows` - Number of rows to return in the result set.
  - **Query Option**: `start` - Pagination start.
  - **Query Option**: `fl` - Fields to return for each document.
  - **Request Example**: `http://<solr_address>/solr/<core_name>/select?q=*:*&start=0&rows=10&sort=id asc&fl=id,name`
  - **Response JSON Example**:
    ```json
    {
      "responseHeader": {
        "status": 0,
        "QTime": 1
      },
      "response": {
        "numFound": 100,
        "start": 0,
        "docs": [
          { "id": "1", "name": "Document 1" },
          { "id": "2", "name": "Document 2" }
        ]
      }
    }
    ```

#### 2. Update API

**Endpoint**: `/update`
**HTTP Method**: `POST` (used for updates)

#### Actions and Query Options

- **Adding Documents**

  - **Query Option**: `commit` - Whether to commit the update immediately.
  - **Data Format**: JSON for document data.
  - **Request JSON Example**:
    ```bash
    curl http://<solr_address>/solr/<core_name>/update?commit=true -H 'Content-type:application/json' -d '[
        {"id":"1", "title":"A new title"},
        {"id":"2", "title":"Another new title"}
    ]'
    ```
  - **Response JSON Example**:
    ```json
    {
      "responseHeader": {
        "status": 0,
        "QTime": 100
      }
    }
    ```

- **Deleting Documents**
  - **Data Format**: JSON for specifying documents to delete by ID or query.
  - **Request JSON Example With ID**:
    ```bash
    curl http://<solr_address>/solr/<core_name>/update?commit=true -H 'Content-type:application/json' -d '{"delete": {"id": "1"}}'
    ```
  - **Request JSON Example With Query**:
    ```bash
    curl http://<solr_address>/solr/<core_name>/update?commit=true -H 'Content-type:application/json' -d '{"delete": {"query": "name='test'"}}'
    ```
  - **Response JSON Example**:
    ```json
    {
      "responseHeader": {
        "status": 0,
        "QTime": 50
      }
    }
    ```

#### 3. Schema API

**Endpoint**: `/schema`
**HTTP Method**: `GET` and `POST` (for retrieving and modifying schema)

#### Actions and Query Options

- **Getting Schema Info**

  - **Query Option**: `wt` - Output format, typically JSON.
  - **Request Example**: `http://<solr_address>/solr/<core_name>/schema?wt=json`
  - **Response JSON Example**:
    ```json
    {
      "responseHeader": {
        "status": 0,
        "QTime": 1
      },
      "schema": {
        "name": "example",
        "version": 1.6,
        "uniqueKey": "id",
        "fieldTypes": [...],
        "fields": [...],
        "dynamicFields": [...],
        "copyFields": [...]
      }
    }
    ```

- **Getting Fields**

  - **Query Option**: `wt` - Output format, typically JSON.
  - **Request Example**: `http://<solr_address>/solr/<core_name>/schema/fields?wt=json`
  - **Response JSON Example**:
    ```json
    {
      "responseHeader": {
        "status": 0,
        "QTime": 5
      },
      "fields": [
        {
          "name": "_root_",
          "type": "string",
          "docValues": false,
          "indexed": true,
          "stored": false
        }
      ]
    }
    ```

- **Getting Field Types**

  - **Query Option**: `wt` - Output format, typically JSON.
  - **Request Example**: `http://<solr_address>/solr/<core_name>/schema/fieldtypes?wt=json`
  - **Response JSON Example**:
    ```json
    {
      "responseHeader": {
        "status": 0,
        "QTime": 5
      },
      "fieldTypes": [
        {
          "name": "text_general",
          "class": "solr.TextField",
          "sortMissingLast": true
        }
      ]
    }
    ```

- **Adding Fields**

  - **Data Format**: JSON for defining new fields.
  - **Request JSON Example**:
    ```bash
    curl http://<solr_address>/solr/<core_name>/schema -H 'Content-type:application/json' -X POST -d '{
      "add-field":{
        "name":"new_field",
        "type":"text_general",
        "stored":true
      }
    }'
    ```
  - **Response SON Example**:
    ```json
    {
      "responseHeader": {
        "status": 0,
        "QTime": 30
      }
    }
    ```

- **Replacing Fields**

  - **Data Format**: JSON for replacing existing fields.
  - **Request JSON Example**:
    ```bash
    curl http://<solr_address>/solr/<core_name>/schema -H 'Content-type:application/json' -X POST -d '{
      "replace-field":{
        "name":"new_field",
        "type":"text_general",
        "stored":true
      }
    }'
    ```
  - **Response SON Example**:
    ```json
    {
      "responseHeader": {
        "status": 0,
        "QTime": 30
      }
    }
    ```

- **Deleting Fields**
  - **Data Format**: JSON for deleting existing fields.
  - **Request JSON Example**:
    ```bash
    curl http://<solr_address>/solr/<core_name>/schema -H 'Content-type:application/json' -X POST -d '{
      "delete-field":{
        "name":"new_field",
        "type":"text_general",
        "stored":true
      }
    }'
    ```
  - **Response SON Example**:
    ```json
    {
      "responseHeader": {
        "status": 0,
        "QTime": 30
      }
    }
    ```

#### Config API

**Endpoint**: `/config`
**HTTP Method**: `POST` for modifications; `GET` can also be used for retrieving configuration.

###### Actions and Query Options

1. **Get Config**

   - **Action**: Retrieve configuration details for a Solr core.
   - **Request Example**:
     ```bash
     curl http://<solr_address>/solr/<core_name>/config
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 5
       },
       "config": {
         "solrConfig": {
           "luceneMatchVersion": "8.0.0",
           "lib": [],
           "indexConfig": {...},
           "requestHandler": {...},
           "updateHandler": {...},
           "query": {...}
         }
       }
     }
     ```

2. **Update Config**
   - **Action**: Update or set properties in the Solr configuration without restarting the server.
   - **Query Options**: JSON payload describing the changes.
   - **Request JSON Example**:
     ```bash
     curl -X POST http://<solr_address>/solr/<core_name>/config -H 'Content-type:application/json' -d '{
       "set-property": {"query.filterCache.size": "512"}
     }'
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 10
       }
     }
     ```

#### Collections API (SolrCloud)

**Endpoint**: `/admin/collections`
**HTTP Method**: `GET` for most actions; `POST` can be used as well depending on the setup and security configurations.

###### Actions and Query Options

1. **Create Collection**

   - **Action**: Creates a new collection.
   - **Query Options**:
     - `name`: The name of the collection.
     - `numShards`: The number of shards for the collection.
     - `replicationFactor`: The number of replicas per shard.
     - `collection.configName`: The name of the configuration to use.
   - **Request Example**:
     ```bash
     curl "http://<solr_address>/solr/admin/collections?action=CREATE&name=new_collection&numShards=2&replicationFactor=2&collection.configName=_default"
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 150
       }
     }
     ```

2. **Delete Collection**

   - **Action**: Deletes an existing collection.
   - **Query Options**:
     - `name`: The name of the collection to delete.
   - **Request Example**:
     ```bash
     curl "http://<solr_address>/solr/admin/collections?action=DELETE&name=old_collection"
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 75
       }
     }
     ```

3. **List Collections**
   - **Action**: Lists all collections.
   - **Request Example**:
     ```bash
     curl "http://<solr_address>/solr/admin/collections?action=LIST"
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 5
       },
       "collections": ["collection1", "collection2", "new_collection"]
     }
     ```

### Core Admin API

**Endpoint**: `/admin/cores`
**HTTP Method**: `GET` for most actions; some might use `POST` depending on how the server is configured.

###### Actions and Query Options

1. **CREATE**

   - **Action**: Creates a new core.
   - **Query Options**:
     - `name`: The name of the core to create.
     - `instanceDir`: The directory for the core instance.
     - `config`: The name of the config file for the core.
     - `schema`: The name of the schema file for the core.
     - `dataDir`: Optional. Specify a custom directory to store the index data.
   - **Request Example**:
     ```bash
     curl "http://<solr_address>/solr/admin/cores?action=CREATE&name=new_core&instanceDir=path/to/dir&config=solrconfig.xml&schema=schema.xml"
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 123
       }
     }
     ```

2. **UNLOAD**

   - **Action**: Unloads a core, optionally removing data.
   - **Query Options**:
     - `core`: The name of the core to unload.
     - `deleteIndex`: Whether to delete the index data (`true` or `false`).
     - `deleteDataDir`: Whether to delete the data directory (`true` or `false`).
     - `deleteInstanceDir`: Whether to delete the instance directory (`true` or `false`).
   - **Request Example**:
     ```bash
     curl "http://<solr_address>/solr/admin/cores?action=UNLOAD&core=old_core&deleteIndex=true"
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 50
       }
     }
     ```

3. **RELOAD**

   - **Action**: Reloads a core to apply new configurations.
   - **Query Options**:
     - `core`: The name of the core to reload.
   - **Request Example**:
     ```bash
     curl "http://<solr_address>/solr/admin/cores?action=RELOAD&core=existing_core"
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 75
       }
     }
     ```

4. **RENAME**

   - **Action**: Renames a core.
   - **Query Options**:
     - `core`: The current name of the core.
     - `other`: The new name for the core.
   - **Request Example**:
     ```bash
     curl "http://<solr_address>/solr/admin/cores?action=RENAME&core=old_name&other=new_name"
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 30
       }
     }
     ```

5. **SWAP**

   - **Action**: Swaps the names of two cores.
   - **Query Options**:
     - `core`: One of the cores to swap.
     - `other`: The other core to swap with.
   - **Request Example**:
     ```bash
     curl "http://<solr_address>/solr/admin/cores?action=SWAP&core=core1&other=core2"
     ```
   - **Response JSON Example**:
     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 10
       }
     }
     ```

6. **STATUS**

   - **Action**: Checks the status of one or more cores.
   - **Query Options**:
     - `core`: (Optional) The name of the core to check status. If omitted, status for all cores is returned.
   - **Request Example**:
     ```bash
     curl "http://<solr_address>/solr/admin/cores?action=STATUS&core=core1"
     ```
   - **Response JSON Example**:

     ```json
     {
       "responseHeader": {
         "status": 0,
         "QTime": 5
       },
       "status": {
         "core1": {
           "name": "core1",
           "instanceDir": "/path/to/core1",
           "dataDir": "/path/to/data/core1",
           "startTime": "2021-05-01T12:00:00Z",
           "uptime": 3600
         }
       }
     }
     ```

Each of these APIs is designed to be interacted with using HTTP requests, where the options vary based on what you need to accomplish. For JSON interaction, you can generally specify wt=json to get responses in JSON format, making it easier to integrate with modern applications, including those built with NestJS. This detailed list gives a fuller picture of the capabilities and administrative controls available within Solr, each with specific endpoints and options tailored to different aspects of managing and querying your index.
