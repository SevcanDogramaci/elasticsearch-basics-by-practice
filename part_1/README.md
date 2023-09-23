# Elasticsearch Basics Part 1

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

4. See index info.
```
GET products
```

5. See how a text is analyzed.
```json
GET products/_analyze
{
  "text": "Description of a beautiful knit sweater."
}
```

6. Search a product by description.
```json
GET products/_search
{
  "query": {
    "match": {
      "description": "Description of a beautiful knit sweater."
    }
  }
}
```

7. Search a product by description keyword.
```json
GET products/_search
{
  "query": {
    "match": {
      "description.keyword": "Description of a beautiful knit sweater."
    }
  }
}
```