---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, docs read 2026-11-07
difficulty: beginner
reading-time: 8 min
---

# Domain 3: CRUD Operations

Weighted **3%**, the smallest domain. One objective: create, read, update and delete documents with the right methods and operators, and query with equality, comparison, logical and array constraints. Commands were run on MongoDB 8.3.13 on 2026-11-07.

## Table of Contents

1. [The four verbs](#the-four-verbs)
2. [Updating with operators](#updating-with-operators)
3. [Querying arrays](#querying-arrays)
4. [Bulk inserts and errors](#bulk-inserts-and-errors)
5. [How the exam tests this domain](#how-the-exam-tests-this-domain)
6. [Key terms](#key-terms)

---

## The four verbs

| Verb | Methods |
|---|---|
| Create | `insertOne()`, `insertMany()` |
| Read | `find()` (plus `findOne()`) |
| Update | `updateOne()`, `updateMany()`, `replaceOne()` |
| Delete | `deleteOne()`, `deleteMany()` |

Insert creates the collection if it does not exist. Every write targets a single collection, and "all write operations in MongoDB are atomic on the level of a single document". That sentence is the reason embedding gives you atomic updates to related data, a point that returns in domain 4.

**[📖 MongoDB CRUD Operations](https://www.mongodb.com/docs/manual/crud/)** - the methods per verb and the single-document atomicity rule

## Updating with operators

An update document uses operators such as `$set` and `$push` to change fields without replacing the whole document. `replaceOne()` swaps the entire document, so fields you leave out vanish. Use `updateOne()` for one match and `updateMany()` for all matches.

```javascript
db.orders.updateOne({_id: 3}, {$set: {status: "paid"}, $push: {items: {sku: "b", qty: 3, price: 20}}}).modifiedCount
```

Output:

```text
1
```

One call changed a scalar and appended to an array in a single atomic document write.

**[📖 Update Operators](https://www.mongodb.com/docs/manual/reference/operator/update/)** - `$set`, `$push` and the rest of the update operator families

## Querying arrays

Filters use comparison operators (`$gt`, `$gte`, `$in`), logical operators (`$and`, `$or`, `$not`) and equality. Array fields add one subtlety. A plain condition on an array's fields can be satisfied by different elements. `$elemMatch` requires one element to satisfy all its conditions.

```javascript
db.orders.find({"items.sku": "b", status: {$in: ["paid", "new"]}}, {_id: 1})
db.orders.find({items: {$elemMatch: {sku: "a", qty: {$gte: 2}}}}, {_id: 1})
```

Output:

```text
[ { _id: 1 }, { _id: 3 } ]
[ { _id: 1 } ]
```

The first query matches orders that contain sku `b` and have status paid or new (orders 1 and 3). The second needs a single line with sku `a` and quantity of at least 2 (order 1 only).

**[📖 Query an Array](https://www.mongodb.com/docs/manual/tutorial/query-arrays/)** - exact match, element conditions and `$elemMatch`

## Bulk inserts and errors

`insertMany()` is ordered by default: it stops at the first error. With `{ordered: false}` it keeps going and reports the failures at the end. Inserting `_id: 1` (a duplicate) and `_id: 9` unordered gave:

```text
MongoBulkWriteError: E11000 duplicate key error collection: shop.orders index: _id_ dup key: { _id: 1 }
```

and a later read showed `[ 1, 3, 9 ]`, so document 9 was still inserted. This ordered/unordered behavior is general MongoDB knowledge; the CRUD overview page above lists bulk writes but this detail was shown by the run, not quoted from a page.

## How the exam tests this domain

- **Pick the method:** one match or many, insert or replace.
- **Pick the operator:** `$set` changes a field, `$push` appends to an array.
- **Array filters:** know that `$elemMatch` ties multiple conditions to one element.

## Key terms

- **CRUD** - create, read, update and delete, the four basic data operations.
- **Update operator** - a `$`-prefixed instruction in an update document, such as `$set` or `$push`.
- **`replaceOne()`** - a method that replaces an entire matching document.
- **`$elemMatch`** - a query operator requiring at least one array element to satisfy all the listed conditions.
- **Atomic** - all-or-nothing; MongoDB writes are atomic at the level of a single document.
