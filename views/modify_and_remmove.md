# Modify and remove a exist view

## 1. Remove a view
```mongodb
db.productView01.drop()
```

## 2. Modify a view
* To modify a view using two following options:
  * Drop and recreate the view
  * Use the `collMod` command

##### 2.1. Drop and recreate view
```mongodb
db.lowStock.drop()
db.createView(
   "lowStock",
   "products",
   [ { $match: { quantity: { $lte: 10 } } } ]
)
```
##### 2.2. Use `collMod` command to modify a view
```mongodb
db.runCommand( {
   collMod: "lowStock",
   viewOn: "products",
   "pipeline": [ { $match: { quantity: { $lte: 10 } } } ]
} )
```