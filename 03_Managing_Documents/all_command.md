# 02_Getting_Started
GET /_cluster/health

GET /_cat/nodes?v

GET /_cat/indices/?v&expand_wildcards=all

GET /_cat/shards?v

GET /products/_doc/100

DELETE /products

PUT /pages


# 03_Managing_Documents
## 03_01 Creating and delete index
### Deleting an index
DELETE /pages

### Create an index (with settings)

PUT /products
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 2
  }
}

#### --> Lúc này khi tạo 1 document, document đó sẽ có 2 shard, mỗi shard 3 replicas (1 primary + 2 replica)


## 03_02 Indexing documents
### Indexing document with auto generated ID
POST /products/_doc
{
  "name": "Coffee Maker",
  "price": 64,
  "in_stock": 10
}

### Indexing document with custom ID:
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 49,
  "in_stock": 4
}

## 03_03 Retiving documents by id
GET /products/_doc/100

## 03_04 Updaing documents
### Updaing an existing field
POST /products/_update/100
{
  "doc": {
    "in_stock":3
  }
}

### Adding a new field
Syntax same as above
POST /products/_update/100
{
  "doc": {
    "tags": ["electronics"]
  }
}

## 03_05 Scripted update
### Reduce current value by one
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock--"
  }
}

### Assigning an arbitrary value
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock = 10"
  }
}

### Using parameter in script
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock -= params.quatity",
    "params": {
      "quatity": 4
    }
  }
}

## 03_06 Upsert
### Có một cách khác để update documents, đó là upsert (update or insert follow a condition )
POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock++"
  },
  "upsert": {
    "name": "Blender",
    "price": 399,
    "in_stock": 5
  }
}

GET /products/_doc/101

## 03_07 Replacing document
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 79,
  "in_stock": 4
}

GET /products/_doc/100

## 03_08 Deleting documents
DELETE /products/_doc/101

## 03_09 Optimistic concurrency control
POST /products/_update/100?if_primary_term=1&if_seq_no=3
{
  "doc": {
    "in_stock": 123
  }
}

## 03_10 Update by query
POST /products/_update_by_query
{
  "script": {
    "source": "ctx._source.in_stock--"
  },
  "query": {
    "match_all": {}
  }
}

## 03_11 Delete by query
POST /products/_delete_by_query
{
  "query": {
    "match_all": { }
  }
}

## 03_12 Batch processing
### Indexing documents
POST /_bulk
{"index": {"_index": "products", "_id": 200}}
{"name": "Espresso Machine", "price": 199, "in_stock": 5}
{"create": {"_index": "products","_id":201}}
{"name": "Milk Frother", "price": 149, "in_stock": 14}

### Updating and deleting documents
POST /_bulk
{"update": {"_index": "products", "_id":201}}
{"doc":{"price":129}}
{"delete": {"_index": "products","_id": 200}}

### Specifying the index name in the request path
POST /products/_bulk
{"update": {"_id":201}}
{"doc":{"price": 129}}
{"delete": {"_id":200}}


GET /products/_search
{
  "query": {
    "match_all": {}
  }
}

GET /_cat/indices

GET /_cat/shards?v
