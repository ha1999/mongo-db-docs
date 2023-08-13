# Query Documents
`pymongo`

## 1. Insert documents to query
```python
db.inventory.insert_many(
    [
        {
            "item": "journal",
            "qty": 25,
            "size": {"h": 14, "w": 21, "uom": "cm"},
            "status": "A",
        },
        {
            "item": "notebook",
            "qty": 50,
            "size": {"h": 8.5, "w": 11, "uom": "in"},
            "status": "A",
        },
        {
            "item": "paper",
            "qty": 100,
            "size": {"h": 8.5, "w": 11, "uom": "in"},
            "status": "D",
        },
        {
            "item": "planner",
            "qty": 75,
            "size": {"h": 22.85, "w": 30, "uom": "cm"},
            "status": "D",
        },
        {
            "item": "postcard",
            "qty": 45,
            "size": {"h": 10, "w": 15.25, "uom": "cm"},
            "status": "A",
        },
    ]
)
```
## 2. Select all documents in a collection
```python
cursor = db.inventory.find({})
```
```sql
SELECT * FROM inventory
```

## 3. Specify equality condition
```python
cursor = db.inventory.find({"status": "D"})
```

```sql
SELECT * FROM inventory WHERE status = "D"
```

## 4. Specify condition using query operator
- The following example retrieves all documents from the inventory collection where status equals either "A" or "D":
    ```python
    cursor = db.inventory.find({"status": {"$in": ["A", "D"]}})
    ```
    ```sql
    SELECT * FROM inventory WHERE status in ("A", "D")
    ```
- Specify and conditions
    ```python
    cursor = db.inventory.find({"status": "A", "qty": {"$lt": 30}})
    ```
    ```sql
    SELECT * FROM inventory WHERE status = "A" AND qty < 30
    ```
- Specify or conditions

    ```python
    cursor = db.inventory.find({"$or": [{"status": "A"}, {"qty": {"$lt": 30}}]})
    ```
    ```sql
    SELECT * FROM inventory WHERE status = "A" OR qty < 30
    ```
- Specify `AND` as well as `OR` conditions
    ```python
    cursor = db.inventory.find(
        {
            "status": "A", 
            "$or": [
                {"qty": {"$lt": 30}}, 
                {"item": {"$regex": "^p"}}
            ]}
    )
    ```
    ```sql
    SELECT * FROM inventory WHERE status = "A" AND ( qty < 30 OR item LIKE "p%")
    ```