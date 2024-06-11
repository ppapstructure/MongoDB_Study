# MongoDB_Collection_CRUD

[Useful_Additional_Syntax](MongoDB_Collection_CRUD/Useful_Additional_Syntax.md)

## 1. Document 입력(CREATE) - insertOne, insertMany

- `insertOne` : 한 개의 document 생성
- `insertMany` : 여러개의 document 생성

```sql
# insertOne EX)
db.people.insertOne(
	{ user_id: "bcd001", age:45, status:"A" }
)

# SQL
INSERT INTO people(user_id, age, status)
VALUES ("bcd001",45,"A")
```

```sql
# insertMany() EX)
db.users.insertMany([
    { name: "Alice", age: 25 },
    { name: "Bob", age: 30 },
    { name: "Charlie", age: 35 }
])

# SQL
INSERT INTO your_table_name (name, age)
VALUES ('Alice', 25),
       ('Bob', 30),
       ('Charlie', 35);
```

## 2. Document 읽기(READ) - findOne, find

- `findOne` : 매칭되는 한 개의 document 검색
- `find` : 매칭되는 여러개의 document 검색

```json
# query cirteria:조건
# projection : 결과값에서 보여질 field

db.users.find(                # collection
		{ age: { $gt: 18} },      # query criteria
		{ name: 1, address: 1}    # projection
).limit(5)                    # cursor modifier
```

```sql
# find() vs SQL
db.users.find()
SELECT * FROM users

db.users.find({}, { name:1, address: 1 })
SELECT _id, name, address FROM users

db.users.find({}, { _id:0, name:1, address:1})
SELECT name, address FROM users

db.users.find({ address: "서울" })
SELECT * FROM users WHERE address = "서울"
```

### 2.1 비교 문법

```json
$eq     =
$gt     >
$gte    >=
$in          Matches any of the values specified in an array.
$lt     <
$lte    <=
$ne     !=
$nin         Matches none of the values specified in an array.
```

```sql
db.users.find({ age: { $gt: 25 } })
SELECT * FROM users WHERE age > 25

db.users.find({ age: { $lt: 25 } })
SELECT * FROM users WHERE age < 25

db.users.find({ age: { $gt: 25, $lte: 50 } })
SELECT * FROM users WHERE age > 25 and age <= 50
```

### 2.2 논리 연산 문법

```json
		$or : or 조건
		$and : and  조건
		$not : not 조건
```

```sql
db.users.find({ address: "서울", age: 45 })
db.users.find({ $and : [{address: "서울"}, {age:45}]})
SELECT * FROM users WHERE address = "서울" AND age = 45

db.users.find( $or : [{address: "경기도" }, { age:45 }])
SELECT * FROM users WHERE address = "경기도" OR age = 45

db.users.find({ age: { $not: { $eq: 45 } } })
SELECT * FROM users WHERE age != 45

```

## 3. Document 수정(Update) 문법 - updateOne, updateMany

- `updateOne` - 매칭되는 한개의 document 업데이트
- `updateMany` - 매칭되는 여러개의 document 업데이트

[Document_Update_Syntax](MongoDB_Collection_CRUD/Document_Update_Syntax.md)

> 업데이트해야 하는 데이터(Key:Value)가 없으면, 해당 데이터가 해당 Document에 추가됨

```sql
db.users.updateMany(                   # collection
{ age: { $lt: 18 } },                  # update filter
{ $set: { status: “reject” }}          # update action
)
```

```sql
db.users.updateMany( { age: {$gt:25} }, { $set: { address: "서울" } } )
UPDATE users SET address = "서울" WHERE age > 25

db.users.updateMany({ address: "서울" }, { $inc: {age: 3} })
UPDATE users SET age = age + 3 WHERE status = "서울"
```

## 4. Document 삭제(Delete) 문법 - deleteOne, deleteMany

- `deleteOne` - 매칭되는 한개의 document 삭제
- `deleteMany` - 매칭되는 여러개의 document 삭제

```sql
db.users.deleteMany(        # collection
	{ status : "reject" },    # delete filter
)
```

```sql
db.users.deleteMany({ address: "서울" })
DELETE FROM users WHERE status = "서울"

db.people.deleteMany({})
DELETE FROM people
```
