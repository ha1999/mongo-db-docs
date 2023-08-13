# On-Demand Materialized Views

> An on-demand materialized view is a pre-computed aggregation pipeline result that is stored on and read from disk. On-demand materialized views are typically the results of a `$merge` or `$out` stage.

## 1. Comparison with Standard Views

- Standard views are computed when you read the view, and are not stored to disk.
- On-demand materialized views are stored on and read from disk. They use a `$merge` or `$out` stage to update the saved data.

> You can create indexes directly on on-demand materialized views because they are stored on disk.

## 2. Performance

> On-demand materialized views provide better read performance than standard views because they are read from disk instead of computed as part of the query. This performance benefit increases based on the complexity of the pipeline and size of the data being aggregated.

## 3. Example

- Create collection ${\color{green}bakeSales}$

    ```python
    db.bakesales.insertMany( [
    { date: new ISODate("2018-12-01"), item: "Cake - Chocolate", quantity: 2, amount: new NumberDecimal("60") },
    { date: new ISODate("2018-12-02"), item: "Cake - Peanut Butter", quantity: 5, amount: new NumberDecimal("90") },
    { date: new ISODate("2018-12-02"), item: "Cake - Red Velvet", quantity: 10, amount: new NumberDecimal("200") },
    { date: new ISODate("2018-12-04"), item: "Cookies - Chocolate Chip", quantity: 20, amount: new NumberDecimal("80") },
    { date: new ISODate("2018-12-04"), item: "Cake - Peanut Butter", quantity: 1, amount: new NumberDecimal("16") },
    { date: new ISODate("2018-12-05"), item: "Pie - Key Lime", quantity: 3, amount: new NumberDecimal("60") },
    { date: new ISODate("2019-01-25"), item: "Cake - Chocolate", quantity: 2, amount: new NumberDecimal("60") },
    { date: new ISODate("2019-01-25"), item: "Cake - Peanut Butter", quantity: 1, amount: new NumberDecimal("16") },
    { date: new ISODate("2019-01-26"), item: "Cake - Red Velvet", quantity: 5, amount: new NumberDecimal("100") },
    { date: new ISODate("2019-01-26"), item: "Cookies - Chocolate Chip", quantity: 12, amount: new NumberDecimal("48") },
    { date: new ISODate("2019-01-26"), item: "Cake - Carrot", quantity: 2, amount: new NumberDecimal("36") },
    { date: new ISODate("2019-01-26"), item: "Cake - Red Velvet", quantity: 5, amount: new NumberDecimal("100") },
    { date: new ISODate("2019-01-27"), item: "Pie - Chocolate Cream", quantity: 1, amount: new NumberDecimal("20") },
    { date: new ISODate("2019-01-27"), item: "Cake - Peanut Butter", quantity: 5, amount: new NumberDecimal("80") },
    { date: new ISODate("2019-01-27"), item: "Tarts - Apple", quantity: 3, amount: new NumberDecimal("12") },
    { date: new ISODate("2019-01-27"), item: "Cookies - Chocolate Chip", quantity: 12, amount: new NumberDecimal("48") },
    { date: new ISODate("2019-01-27"), item: "Cake - Carrot", quantity: 5, amount: new NumberDecimal("36") },
    { date: new ISODate("2019-01-27"), item: "Cake - Red Velvet", quantity: 5, amount: new NumberDecimal("100") },
    { date: new ISODate("2019-01-28"), item: "Cookies - Chocolate Chip", quantity: 20, amount: new NumberDecimal("80") },
    { date: new ISODate("2019-01-28"), item: "Pie - Key Lime", quantity: 3, amount: new NumberDecimal("60") },
    { date: new ISODate("2019-01-28"), item: "Cake - Red Velvet", quantity: 5, amount: new NumberDecimal("100") },
    ] );
    ```

- Define the On-demand materialized view
  > The following updateMonthlySales function defines a monthlybakesales materialized view that contains the cumulative monthly sales information.

    ```python
    updateMonthlySales = function(startDate) {
    db.bakesales.aggregate( [
        { $match: { date: { $gte: startDate } } },
        { $group: { _id: { $dateToString: { format: "%Y-%m", date: "$date" } }, sales_quantity: { $sum: "$quantity"}, sales_amount: { $sum: "$amount" } } },
        { $merge: { into: "monthlybakesales", whenMatched: "replace" } }
    ] );
    };
    ```

- Perform Initial Run
  ```python
  updateMonthlySales(new ISODate("1970-01-01"));
  ```
  ```python
  { "_id" : "2018-12", "sales_quantity" : 41, "sales_amount" : NumberDecimal("506") }
  { "_id" : "2019-01", "sales_quantity" : 86, "sales_amount" : NumberDecimal("896") }
  ```
- Refresh materialize view
  ```python
  updateMonthlySales(new ISODate("2019-01-01"));
  ```
