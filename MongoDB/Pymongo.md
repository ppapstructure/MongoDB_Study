# Pymongo

[Pymongo_find()](Pymongo%205f20539e770a488f8c7802a149956d26/Pymongo_find()%201fbb8e2cb41c45f2a29cbc5331dea128.md)

[Pymongo_aggregate()](Pymongo%205f20539e770a488f8c7802a149956d26/Pymongo_aggregate()%205893f45e1d774191b4ae04a893d25cb4.md)

### **1. pymongo 설치**

- pymongo 설치하는 코드

```bash
!pip install pymongo
```

### **2. MongoDB에 연결하기**

- MongoDB에 연결하려면 MongoClient 클래스를 사용해야 합니다.
- MongoClient 객체를 생성하고 host 매개변수에 MongoDB 서버의 주소와 포트를 지정합니다.
- 다음 코드에서 username 및 password는 MongoDB 인스턴스에 대한 실제 사용자 이름과 비밀번호로 대체되어야 합니다.
- 또한, localhost:27017 부분은 MongoDB 서버의 주소와 포트로 실제 값으로 대체되어야 합니다.

```python
from pymongo import MongoClient

# MongoDB에 연결 (인증 필요시)
# client = MongoClient("mongodb://username:password@localhost:27017")

# MongoDB에 연결 (인증 미필요시)
client = MongoClient("mongodb://localhost:27017")
```

### **3. 데이터베이스 생성 및 선택**

- 연결된 MongoDB 클라이언트에서 데이터베이스를 생성하고 선택할 수 있습니다.
- client 객체의 database_name 속성을 사용하여 데이터베이스를 생성하고 선택합니다.

```python
# 데이터베이스 선택# 해당 데이터베이스가 없으면 해당 데이터베이스에 새로운 컬렉션에 데이터 처리시, 해당 데이터베이스와 컬렉션이 자동 생성
db= client["mydatabase"]
# 또는db= client.mydatabase
# 이때 mydatabase는 임의의 db 이름을 의미
```

**데이터베이스의 컬렉션 리스트 확인**

```python
# list_collection_names() 를 통해 컬렉션 리스트를 가져올 수 있음
# db = client['test']
db = client.test
collections = db.list_collection_names()
for collection in collections:
     print(collection)

# users 컬렉션 생성 후, 데이터를 1개이상 삽입해야 위의 코드를 통해 조회 가능
```

### **4.컬렉션 생성 및 선택**

```python
# 컬렉션 선택 (해당 컬렉션이 없으면 해당 컬렉션에 데이터 처리시, 해당 컬렉션이 자동 생성)
users= db["users"]
# 또는
users= db.users
# users는 임의의 collection 이름
```

# Pymongo CRUD

### **1. 데이터 삽입(CREATE)**

- 데이터를 MongoDB에 삽입하려면 insert_one() 또는 insert_many() 메서드를 사용합니다.

```python
# 단일 문서 삽입
db = client['test'] 
collection = db.users 
# or
# collection = db["users"]
data = {"name": "John", "age": 30}
result = collection.insert_one(data)
print("Inserted ID:", result.inserted_id) # _id 

# 여러 문서 삽입
data = [
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 35},
    {"name": "Charlie", "age": 40}
]
result = collection.insert_many(data)
print("Inserted IDs:", result.inserted_ids) # _id 리스트
```

### **2. 데이터 조회(READ)**

- 데이터를 조회하려면 find_one() 또는 find() 메서드를 사용합니다.

> MongoDB 에서는 findOne() 또는 insertMany 과 같이 naming 이 되어 있지만, pymongo 에서는 find_one() 또는 insert_many() 와 같은 naming 으로 되어 있음
> 

```python
db = client['데이터베이스_이름']
collection = db.컬렉션_이름
# 단일 문서 조회
db = client['test']
collection = db.users
document = collection.find_one({"name": "John"})
print('find_one')
print(document)

# 모든 문서 조회
print('find')
documents = collection.find()
for document in documents:
    print(document)
```

### **3. 데이터 수정(UPDATE)**

- 데이터를 수정하려면 update_one() 또는 update_many() 메서드를 사용합니다.

```python
# 단일 문서 수정
collection = db.users
filter = {"name": "John"}
update = {"$set": {"age": 31}}
result = collection.update_one(filter, update)
print("Modified Count:", result.modified_count) # 수정된 document count

# 여러 문서 수정
filter = {"age": {"$gt": 30}}
update = {"$set": {"is_available": True}}
result = collection.update_many(filter, update)
print("Modified Count:", result.modified_count) # 수정된 document count
```

### **4. 데이터 삭제(DELTE)**

- 데이터를 수정하려면 delete_one() 또는 delete_many() 메서드를 사용합니다

```python
# 단일 문서 삭제
collection = db.users
filter = {"name": "John"}
result = collection.delete_one(filter)
print("Deleted Count:", result.deleted_count) # 삭제된 document count

# 여러 문서 삭제
filter = {"age": {"$lt": 30}}
result = collection.delete_many(filter)
print("Deleted Count:", result.deleted_count) # 삭제된 document count
```

### **프로그램 종료**

- 프로그램 종료시에는 MongoClient() 객체에 close() 를 명시적으로 호출해주는 것이 좋다.

```python
# 연결 종료
client.close()
```

## **전체 pymongo 템플릿 코드**

```python
from pymongo import MongoClient

# MongoDB에 연결 (인증 미필요시)
client = MongoClient("mongodb://localhost:27017")
# client = MongoClient("mongodb://username:password@localhost:27017")
# 인증이 필요하지 않은 경우 위의 첫 번째 줄 사용, 인증이 필요한 경우 두 번째 줄 사용

db = client['test']  # 'test' 데이터베이스 선택

collection = db.users  # 'users' 컬렉션 선택

documents = collection.find()  # 'users' 컬렉션의 모든 문서 조회
for document in documents: # find() 의 결과는 iterable 객체이므로 반복문을 통해 각 데이터를 가져와야 함
    print(document)

# 연결 종료
client.close()
```