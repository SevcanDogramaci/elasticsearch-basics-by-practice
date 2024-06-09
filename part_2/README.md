# Elasticsearch Basics Part 2

1. Run `docker-compose up`.

2. Go to http://localhost:5601/app/dev_tools#/console.

3. Create products index by inserting documents.
```json
POST products/_doc
{
   "id": 1, 
   "title": "Knit Sweater",
   "description": "Description of a beautiful knit sweater."
}


POST products/_doc
{
   "id": 2, 
   "title": "Cotton Sweater",
   "description": "Description of a beautiful cotton sweater."
}
```

4. See how a text is analyzed.
```json
GET products/_analyze
{
  "text": "Description of a beautiful knit sweater."
}
```

5. Create a index with the custom analyzer.
```json
PUT products-with-custom-analyzer
{
  "settings": {
    "analysis": {
      "analyzer": {
        "custom_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": ["lowercase", "english_stopwords"]
        }
      },
      "filter": {
        "english_stopwords": {
          "type": "stop",
          "stopwords": "_english_" 
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "id": {
          "type": "long"
      },
      "description": {
        "type": "text",
        "analyzer": "custom_analyzer"
      },
      "title": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      },
      "sellerPhoneNumber": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      }
    }
  }
}
```

6. See how a text is analyzed in the new index with the new analyzer.
```json
GET products-with-custom-analyzer/_analyze
{
  "analyzer": "custom_analyzer",
  "text": "Description of a beautiful knit sweater."
}
```

7. Let's insert a new product to the new index.
```json
POST products-with-custom-analyzer/_doc
{
   "id": 1, 
   "title": "Knit Sweater",
   "description": "Description of a beautiful knit sweater.",
   "sellerPhoneNumber": "123123"
}
```

8. Let's ingest a pipeline to remove the sellerPhoneNumber fields.
```json
PUT /_ingest/pipeline/sensitive-data-remover
{
  "processors": [
    {
      "remove": {
        "field": "sellerPhoneNumber",
        "ignore_failure": true
      }
    }
  ]
}
```

9. Search a product by sellerPhoneNumber field.
```json
GET products-with-custom-analyzer/_search
{
  "query": {
    "match": {
      "sellerPhoneNumber.keyword": "123123"
    }
  }
}
```

10. Reindex data to a new index by ingesting the pipeline.
```json
POST _reindex
{
  "source": {
    "index": "products-with-custom-analyzer"
  },
  "dest": {
    "index": "products-with-custom-analyzer-reindexed",
    "pipeline": "sensitive-data-remover"
  }
}
```

11. Search a product by sellerPhoneNumber field again.
```json
GET products-with-custom-analyzer-reindexed/_search
{
  "query": {
    "match": {
      "sellerPhoneNumber.keyword": "123123"
    }
  }
}
```