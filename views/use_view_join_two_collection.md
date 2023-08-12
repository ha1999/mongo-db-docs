# Use a View to Join Two Collections
```
You can use $lookup to create a view over two collections and then run queries against the view.
Applications can query the view without having to construct or maintain complex pipelines.
```
## 1. Create two collections for joinning

```mongodb
db.inventory.insertMany( [
   { prodId: 100, price: 20, quantity: 125 },
   { prodId: 101, price: 10, quantity: 234 },
   { prodId: 102, price: 15, quantity: 432 },
   { prodId: 103, price: 17, quantity: 320 }
] )

db.orders.insertMany( [
   { orderId: 201, custid: 301, prodId: 100, numPurchased: 20 },
   { orderId: 202, custid: 302, prodId: 101, numPurchased: 10 },
   { orderId: 203, custid: 303, prodId: 102, numPurchased: 5 },
   { orderId: 204, custid: 303, prodId: 103, numPurchased: 15 },
   { orderId: 205, custid: 303, prodId: 103, numPurchased: 20 },
   { orderId: 206, custid: 302, prodId: 102, numPurchased: 1 },
   { orderId: 207, custid: 302, prodId: 101, numPurchased: 5 },
   { orderId: 208, custid: 301, prodId: 100, numPurchased: 10 },
   { orderId: 209, custid: 303, prodId: 103, numPurchased: 30 }
] )
```

## 2. Create a joined view
```mongodb
db.createView( "sales", "orders", [
   {
      $lookup:
         {
            from: "inventory",
            localField: "prodId",
            foreignField: "prodId",
            as: "inventoryDocs"
         }
   },
   {
      $project:
         {
           _id: 0,
           prodId: 1,
           orderId: 1,
           numPurchased: 1,
           price: "$inventoryDocs.price"
         }
   },
      { $unwind: "$price" }
] )
```

## 3. Query the joined view
```mongodb
db.sales.aggregate( [
   {
      $group:
         {
            _id: "$prodId",
            amountSold: { $sum: { $multiply: [ "$price", "$numPurchased" ] } }
         }
   }
] )
```

* The result is:
  ```mongodb
  [
    { _id: 102, amountSold: 90 },
    { _id: 101, amountSold: 150 },
    { _id: 103, amountSold: 1105 },
    { _id: 100, amountSold: 600 }
  ]
  ```