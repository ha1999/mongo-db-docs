# Query on Embedded/Nested Documents
`pymongo`

## 1. Create collections
```python
from bson.son import SON

db.inventory.insert_many(
    [
        {
            "item": "journal",
            "qty": 25,
            "size": SON([("h", 14), ("w", 21), ("uom", "cm")]),
            "status": "A",
        },
        {
            "item": "notebook",
            "qty": 50,
            "size": SON([("h", 8.5), ("w", 11), ("uom", "in")]),
            "status": "A",
        },
        {
            "item": "paper",
            "qty": 100,
            "size": SON([("h", 8.5), ("w", 11), ("uom", "in")]),
            "status": "D",
        },
        {
            "item": "planner",
            "qty": 75,
            "size": SON([("h", 22.85), ("w", 30), ("uom", "cm")]),
            "status": "D",
        },
        {
            "item": "postcard",
            "qty": 45,
            "size": SON([("h", 10), ("w", 15.25), ("uom", "cm")]),
            "status": "A",
        },
    ]
)
```

## 2. Match an Embedded/Nested Document
- For example, the following query selects all documents where the field size equals the document `{ h: 14, w: 21, uom: "cm" }`:
    ```python
    cursor = db.inventory.find({"size": SON([("h", 14), ("w", 21), ("uom", "cm")])})
    ```
- Equality matches on the whole embedded document require an exact match of the specified <value> document, including the field order. For example, the following query does not match any documents in the inventory collection:
    ```python
    cursor = db.inventory.find({"size": SON([("w", 21), ("h", 14), ("uom", "cm")])})
    ```
## 3. Query on Nested Field
> To specify a query condition on fields in an embedded/nested document, use dot notation ("field.nestedField").
- Specify Equality Match on a Nested Field
    ```python
    cursor = db.inventory.find({"size.uom": "in"})
    ```
- Specify Match using Query Operator
  ```python
  cursor = db.inventory.find({"size.h": {"$lt": 15}})
  ```
- Specify AND Condition
    ```python
    cursor = db.inventory.find({"size.h": {"$lt": 15}, "size.uom": "in", "status": "D"})
    ```