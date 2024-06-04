# Document 수정 관련 문법

## 특정 필드 업데이트 하기

- 이름이 ‘유진’인 문서에서 ‘age’필드를 26으로 업데이트하는 명령:
    - MongoDB: `db.usres.updateOne( { name: “유진” }, { $set: { age: 26 } })`
    - SQL에서는 UPDATE users SET age = 26 WHERE name = ‘유진’과 동일

## 문서를 replace 하기

- 이름이 ‘동현’인 문서를 새로운 문서로 대체하는 명령:
    - MongoDB: `db.users.updateOne( { name: “동현” }, { $set: { “name”:”동현2세”, age:31, hobbies: [”축구”,”음악”,”영화”] } } )`
    - SQL에서는 동등한 구문이 없음

## 특정 필드를 제거하기

- 이름인 “유진”인 문서에서 “age”필드를 제거하는 명령:
    - MongoDB: `db.users.updateOne( { name: “유진” }, { $unset: { age: 1} } )`
    - SQL에서는 `UPDATE usres SET age = NULL WHERE name = ‘유진’`과 유사

## 특정 필드를 제거하기

- 이름이 ‘유진’인 문서에서 ‘age’ 필드를 제거하는 명령:
    - MongoDB: `db.users.updateOne( { name: “유진” }, { $unset: { age: 1 } } )`
    - SQL에서는 UPDATE users SET age = NULL WHERE name = ‘유진’과 유사

## 특정 조건을 만족하는 문서가 없을 경우 새로 추가하기

- 이름이 ‘민준’인 문서가 없을 경우 새로운 문서를 추가하는 명령:
    - MongoDB: `db.users.updateOne( { name: “민준” }, { $set: { name:”민준”, age:22, hobbies: [ “음악”, “여행” ] } }, { upsert: true } )`
    - SQL에서는 이런 직접적인 기능은 지원하지 않음

## 여러 문서의 특정 필드를 수정하기

- 나이가 20 이하인 모든 문서에서 ‘hobbies’ 필드를 ‘독서’로 업데이트 하는 명령:
    - MongoDB : `db.users.updateMany( { age : { $lte: 20 } }, {$set: { hobbies: [”독서”] } } )`
    - SQL : `UPDATE users SET hobbies = ‘독서’ WHERE age ≤ 20`과 동일

## **배열에 값 추가하기**

- 이름이 '유진'인 문서의 'hobbies' 배열에 '운동'을 추가하는 명령:
    - MongoDB: `db.users.updateOne( { name: "유진" }, { $push: { hobbies: "운동" } } )`
    - SQL에서는 이런 배열 추가 기능을 지원하지 않음

## **배열에서 값 제거하기**

- 이름이 '유진'인 문서의 'hobbies' 배열에서 '운동'을 제거하는 명령:
    - MongoDB: `db.users.updateOne( { name: "유진" }, { $pull: { hobbies: "운동" } } )`
    - SQL에서는 이런 배열 제거 기능을 지원하지 않음