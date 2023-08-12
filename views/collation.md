# Create a View with Default Collation
```
Collation allows you specify language rules for string comparion, such as rules letter-case, accent marks
```

## 1. Create a collection

``` mongodb
db.places.insertMany([
   { _id: 1, category: "café" },
   { _id: 2, category: "cafe" },
   { _id: 3, category: "cafE" }
])
```

## 2. Create a standard view with specifying collation
```mongodb
db.createView(
   "placesView",
   "places",
   [ { $project: { category: 1 } } ],
   { collation: { locale: "fr", strength: 1 } }
)
```

## 3. Query on a view with specific collation
```mongodb
db.placesView.countDocuments( { category: "cafe" } )
```