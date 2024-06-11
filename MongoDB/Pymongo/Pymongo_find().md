# Pymongo_find()

## 기본 Pymongo 템플릿 코드

- MongoDB의 대표적인 데이터 셋(sample_mflix) 사용

```sql
from pymongo import MongoClient

# MongoDB에 연결 (인증 미필요시)
client = MongoClient("mongodb://localhost:27017")
# client = MongoClient("mongodb://username:password@localhost:27017")
# 인증이 필요하지 않은 경우 위의 첫 번째 줄 사용, 인증이 필요한 경우 두 번째 줄 사용

db = client.sample_mflix
movies = db.movies
```

### **1. 다양한 find() 문법 적용**

**1. 프로젝션(projection) - 결과 문서에 표시할 필드 지정:**

```sql
for movie in movies.find({"year": 1923}, {"_id": 0, "title": 1, "year": 1}):
    print(movie, movie['year'])
```

**2. 비교 쿼리 연산자 - MongoDB 비교 쿼리 연산자 사용:**

```sql
# 1910년 이전에 출시된 영화 찾기
for movie in movies.find({"year": {"$lt": 1910}}, {"_id": 0, "title": 1, "year": 1}):
    print(movie)
```

**3. 논리 쿼리 연산자 - MongoDB 논리 쿼리 연산자 사용:**

```sql
# 1900년 이전 또는 2015년 이후에 출시된 영화 찾기
for movie in movies.find(
        {"$or": [{"year": {"$lt": 1900}}, {"year": {"$gt": 2015}}]},
        {"_id": 0, "title": 1, "year": 1}
):
    print(movie)
```

**4. 배열 쿼리 연산자 - MongoDB 배열 쿼리 연산자 사용:**

```sql
# 'Action'과 'Sci-Fi' 장르의 영화 찾기
for movie in movies.find(
        {"genres": {"$all": ["Action", "Sci-Fi", "Drama"]}},
        {"_id": 0, "title": 1, "year": 1, "genres": 1}
):
    print(movie)
```

**5. 정렬하기(sort), 앞쪽 일부 건너뛰기(skip), 갯수 제한하기(limit):**

```sql
for movie in movies.find().sort("imdb.rating", -1).skip(3).limit(3):  # -1은 내림차순을 의미합니다.
    print(movie)
```

**6. 정규표현식과 pymongo**

- 파이썬의 정규표현식 라이브러리인 `re` 모듈의 `compile` 함수를 사용하여 정규 표현식 객체를 생성하고,
- 이를 pymongo 에 적용할 수 있습니다.
- 예: re.I (IGNORECASE): 이 옵션은 대소문자를 구분하지 않는다는 것을 나타냅니다. 따라서 'Star', 'STAR', 'star', 'sTaR' 등을 모두 찾을 수 있습니다.

```sql
import re
regex = re.compile('Star', re.I)  # 'Star'를 대소문자를 구분하지 않고 검색합니다.

for movie in movies.find({"title": regex}).limit(1):
    print(movie)
```

- re 모듈 없이, 직접 정규표현식을 pymongo 에 사용할 수도 있음
- `$options`
    - `i`: 대소문자를 구분하지 않습니다. (re 라이브러리에서는 re.I)

```sql
for movie in movies.find({"title": {"$regex": "star", "$options": 'i'}}).limit(1):
    print(movie)
```

**7. distinct: 이 메소드는 특정 필드의 모든 고유한 값을 반환합니다.**

```sql
distinct_genres = movies.distinct('genres')
print(distinct_genres)
```

**8. $in: 이 연산자는 필드 값이 특정 배열 내의 값 중 하나와 일치하는 문서를 선택합니다.**

```sql
for movie in movies.find({'genres': {'$in': ['Action', 'Adventure']}}).limit(3):
    print(movie['genres'])
```

**9. $exists: 이 연산자는 특정 필드가 문서에 존재하는지 여부에 따라 문서를 선택합니다.**

```sql
for movie in movies.find({'writers': {'$exists': False}}).limit(3):
    print(movie)
```

**10. count_documents: 이 메소드는 쿼리에 일치하는 문서의 수를 반환합니다.**

- find() 대신에 count_documents() 메서드로 count 값을 얻을 수 있음

> find().count() 방식도 문서의 수를 세는 방법으로 사용할 수 있지만, 이 방식은 MongoDB 4.0 이후로 공식적으로 deprecated (사용이 권장되지 않는) 되었습니다.
> 

```sql
count = movies.count_documents({'genres': {'$in': ['Action', 'Adventure']}})
print(count)
```