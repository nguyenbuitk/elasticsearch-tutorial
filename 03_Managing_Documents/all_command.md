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

