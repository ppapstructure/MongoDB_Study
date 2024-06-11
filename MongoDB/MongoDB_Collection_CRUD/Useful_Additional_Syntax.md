# 유용한 추가 문법

## 정규 표현식을 이용한 검색($regex)

- “LEE”라는 문자열을 이름 필드에서 찾는 명령:
    - MongoDB: `db.users.find({ name: /LEE/ })`
    - MognoDB: `db.users.find({ name: { $regex: /LEE/ } })`
    - SQL에서는 `SELECT * FROM users WHERE name like “%Lee%”`와 동일
- 이름 필드가 “Da”로 시작하는 모든 문서를 찾는 명령
    - MongoDB: `db.users.find( { name: /^Da/ } )`
    - MongoDB: `db.users.find( { name: { $regex: /^Da/ } } )`
    - SQL에서는 `SELECT * FROM users WHERE name like “Da%”`와 동일

## 정렬(sort)

- 주소가 “경기도”인 모든 문서를 찾아서, 나이 순으로 오름차순 정렬하는 명령:
    - MongoDB: `db.users.find({ address : “경기도” }).sort({ age:1 })`
    - SQL에서는 `SELECT * FROM users WHERE address = “경기도” ORDER BY age ASC`와 동일
- 주소가 “경기도”인 모든 문서를 찾아서, 나이 순으로 내림차순 정렬하는 명령:
    - MongoDB: `db.users.find({address: “경기도”}).sort( { age: -1 } )`
    - SQL에서는 `SELECT * FROM users WHERE address = “경기도” ORDER BY age DESC`와 동일

## 문서 개수 세기(count)

- 사용자 컬렉션의 문서 수를 세는 명령:
    - MongoDB: `db.users.count(), db.users.find().count()`
    - SQL에서는 `SELECT COUNT(*) FROM users`와 동일

## 필드 존재 여부로 개수 세기($exists)

- 모든 사용자의 주소를 중복 없이 가져오는 명령:
    - MongoDB: `db.users.count( { address: { $exists: true } } ), db.users.find({ address: { $exists: true }}).count()`
    - `SELECT COUNT(address) FROM users`

## 중복제거(distinct)

- 모든 사용자의 주소를 중복 없이 가져오는 명령:
    - MongoDB: `db.users.distinct(”address”)`
    - SQL에서는 `SELECT DISTINCT(address) FROM people`과 동일

## 한 개만 가져오기(findOne, limit)

- 사용자 컬렉션에서 한 개의 문서만 가져오는 명령:
    - MongoDB: `db.users.findOne(), db.users.find().limit(1)`
    - SQL에서는 `SELECT * FROM susers LIMIT 1`

## 배열과 $all

- 배열(array)를 사용하여 여러 값을 하나의 필드에 저장 가능
- 배열은 대괄호([])로 묶인 값들의 리스트로 표현
- ex)

```sql
db.users.insertMany([
	{ name: "유진", age: 25, hobbies: ["독서", "영화" ,"요리"]},
	{ name: "동현", age: 30, hobbies: ["축구", "음악" ,"영화"]},
	{ name: "혜진", age: 35, hobbies: ["요리", "여행", "독서"]}
])
```

### 배열 필드가 주어진 모든 값을 포함하는 문서 찾기($all)

- 취미 필드가 “축구”와 “요리”를 모두 포함하는 모든 문서를 찾는 명령:
    - MongoDB: `db.users.find( { hobbies: { $all: [”축구”,”음악”] } } )`
    - SQL에서는 동일한 기능을 지원하지 않음 but
    - `SELECT * FROM users WHERE hobbies LIKE “%축구%”  AND hobbies LIKE “%음악%”`
    - 와 유사하나, SQL의 경우 “hobbies” 필드가 문자열 타입이어야 함

## 여러 값 중 하나와 일치하는 문서 찾기($in)

- 나이가 20세, 30세, 40세 중 어떤 것도 아닌 모든 문서를 찾는 명령:
    - MongoDB: `db.users.find( { hobbies: { $nin: [”축구”, “요리”] } } )`
    - SQL에서는 `SELECT * FROM users WHERE hobbies NOT LIKE “%축구%” AND hobbies NOT LIKE ‘%요리%’`와 동일