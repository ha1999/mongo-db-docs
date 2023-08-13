# Documents in MongoDB
> MongoDB stores data records as BSON documents.

## 1. Documents structure
 - MongoDB documents are composed of field-and-value pairs and have the following structure:
    ```mongodb
    {
        field1: value1,
        field2: value2,
        field3: value3,
        ...
        fieldN: valueN
    }
    ```
## 2. Field names
> Documents have the following restrictions on field names:
- The field name `_id` is reserved for use as a primary key; its value must be unique in the collection, is immutable, and may be of any type other than an array. If the `_id` contains subfields, the subfield names cannot begin with a `($)` symbol.
- Field names cannot contain the `null` character.
- The server permits storage of field names that contain `dots (.)` and `dollar signs ($)`.

## 3. Dot notation
> MongoDB uses the dot notation to access the elements of an array and to access the fields of an embedded document.
- Arrays
    ```mongodb
    "<array>.<index>"
    ```
    ```mongodb
    {
        ...
        contribs: [ "Turing machine", "Turing test", "Turingery" ],
        ...
    }
    ```
    To specify the third element in the contribs array, use the dot notation `"contribs.2"`.
- Embedded documents
  ```mongodb
  "<embedded document>.<field>"
  ```
  ```mongodb
  {
    ...
    name: { first: "Alan", last: "Turing" },
    contact: { phone: { type: "cell", number: "111-222-3333" } },
    ...
  }
  ```
    - To specify the field named last in the name field, use the dot notation `"name.last"`.
    - To specify the number in the phone document in the contact field, use the dot notation `"contact.phone.number"`.

## 4. Documents limitation
- Documents size limit
  > The maximum BSON document size is 16 megabytes.
- Documents field ordered
  > Unlike JavaScript objects, the fields in a BSON document are ordered.