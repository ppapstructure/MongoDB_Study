# Pymongo_aggregate()

## **기본 pymongo 템플릿 코드**

```sql
from pymongo import MongoClient

# MongoDB에 연결 (인증 미필요시)
client = MongoClient("mongodb://localhost:27017")
# client = MongoClient("mongodb://username:password@localhost:27017")
# 인증이 필요하지 않은 경우 위의 첫 번째 줄 사용, 인증이 필요한 경우 두 번째 줄 사용

db = client.sample_mflix
movies = db.movies
```

### 1.**다양한 aggregate() 문법 적용**

**1. $match: 이 스테이지는 쿼리와 유사한 방식으로 문서를 필터링한다**

> 결과가 너무 많기 때문에, $limit 문법도 함께 사용
> 

```sql
pipeline = [
    {"$match": {"genres": "Action"}}, {"$limit": 1}
]
# for movie in movies.aggregate(pipeline):
    # print(movie)
documents = list(movies.aggregate(pipeline))[0]
print(documents)
```

**2. $group: 이 스테이지는 특정 필드를 기준으로 문서를 그룹화하고, 각 그룹에 대해 다양한 연산을 수행할 수 있습니다.**

```sql
# 영상 후반부에 왜 다음과 같이 수정하였는지도 설명을 드립니다.
pipeline = [
    {"$group": {"_id": "$directors", "count": {"$sum": 1}}}, { "$limit": 5 }
]
for group in movies.aggregate(pipeline):
    #print(group['count'])
    print(group)
```

**3. $sort: 이 스테이지는 특정 필드를 기준으로 문서를 정렬합니다.**

```sql
pipeline = [
    {"$sort": {"title": 1}}, 
    { "$limit": 3 },
    {"$project": {"_id":0,"title":1}}
]
for movie in movies.aggregate(pipeline):
    print(movie)
```

**4. $limit: 이 스테이지는 출력되는 문서의 수를 제한합니다.**

```sql
pipeline = [
    {"$limit": 1}
]
for movie in movies.aggregate(pipeline):
    print(movie)
```

**5. $project: 이 스테이지는 출력되는 문서의 필드를 추가, 제거, 또는 새로 생성합니다.**

```sql
pipeline = [
    {"$project": {"_id": 0, "title": 1, "genres": 1}}, {"$limit": 1}
]
for movie in movies.aggregate(pipeline):
    print(movie)
```

**6. $unwind: 이 스테이지는 배열 필드를 풀어서 각 원소를 별도의 문서로 만듭니다.**

```sql
pipeline = [
    {"$unwind": "$genres"}, {"$limit": 3}
]
for movie in movies.aggregate(pipeline):
    print(movie)
```

**7. `$group`과 `$sum`: 이 예제에서는 감독별로 영화를 그룹화하고, 각 그룹의 영화 수를 계산합니다.**

```sql
pipeline = [
    {"$group": {"_id": "$directors", "count": {"$sum": 1}}}, { "$limit": 5 }
]

for group in movies.aggregate(pipeline):
    print(group)
```

**8. `$group`과 `$avg`: 이 예제에서는 감독별로 영화를 그룹화하고, 각 그룹의 영화 평점 평균을 계산합니다.**

```sql
pipeline = [
    {"$group": {"_id": "$directors", "average_rating": {"$avg": "$imdb.rating"}}}, { "$limit": 5 }
]
for group in movies.aggregate(pipeline):
    print(group)
```