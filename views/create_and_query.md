# Create and query a view

## 1. Create a view

We are using two following syntax to create a view in MongoDB

- [db.createCollection()](https://www.mongodb.com/docs/manual/reference/method/db.createCollection/#mongodb-method-db.createCollection)
  ```mongodb
  db.createCollection(
  "<viewName>",
      {
          "viewOn" : "<source>",
          "pipeline" : [<pipeline>],
          "collation" : { <collation> }
      }
  )
  ```
- [db.createView()](https://www.mongodb.com/docs/manual/reference/method/db.createView/#mongodb-method-db.createView)

  ```mongodb
  db.createView(
      "<viewName>",
      "<source>",
      [<pipeline>],
      {
          "collation" : { <collation> }
      }
  )
  ```

## 2. Restrictions

- You must `create views in the same database` as the source collection.
- A view definition pipeline `cannot include the $out or the $merge stage`. This restriction also applies to `embedded pipelines, such as pipelines used in $lookup or $facet stages`.
- You `cannot rename a view` once it is created.

## 3. Unsupported operations

Some operations are not available with views:

1. db.collection.mapReduce().
2. $text operator, since $text operation in aggregation is valid only for the first stage. $geoNear pipeline stage.
3. Renaming a view.

## 4. Examples

- Create a students collection to use for this example:

  ```mongodb
  db.students.insertMany( [
      { sID: 22001, name: "Alex", year: 1, score: 4.0 },
      { sID: 21001, name: "bernie", year: 2, score: 3.7 },
      { sID: 20010, name: "Chris", year: 3, score: 2.5 },
      { sID: 22021, name: "Drew", year: 1, score: 3.2 },
      { sID: 17301, name: "harley", year: 6, score: 3.1 },
      { sID: 21022, name: "Farmer", year: 1, score: 2.2 },
      { sID: 20020, name: "george", year: 3, score: 2.8 },
      { sID: 18020, name: "Harley", year: 5, score: 2.8 },
  ] )
  ```

- Use db.createView() to create a view

  ```mongodb
  db.createView(
      "firstYears",
      "students",
      [ { $match: { year: 1 } } ]
  )
  ```

- Query the View

  ```mongodb
  db.graduateStudents.aggregate(
  [
      { $sort: { name: 1 } },
      { $unset: [ "_id" ] }
  ]
  )
  ```

  ```mongodb
  db.firstYears.find({}, { _id: 0 } )
  ```

## 5. Resource Locking
* db.createView() obtains an exclusive lock on the specified collection or view for the duration of the operation. `All subsequent operations on the collection must wait until db.createView() releases the lock. db.createView() typically holds this lock for a short time`.
