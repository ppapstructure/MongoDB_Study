# Mongodb_aggregation

## MongoDB Aggregation Framework 문법과 pipeline 동작

- Aggregation Pipeline: Aggregation은 주로 Aggregation Pipeline을 사용하여 데이터를 처리
    - Pipeline은 여러 단계로 구성되며, 각 단계는 데이터를 변형하거나 필터링하는데 사용
    - 각 단계는 이전 단계의 결과를 입력으로 받아 처리하고, 최종 결과를 반환
- 매칭, 그룹화, 정렬, 프로젝션: Aggregation Pipeline은 다양한 단계로 구성될 수 있음
    - 주요 단계에는 `$match`, `$group`, `$sort`, `$project` 등이 있음
        - `$match`는 조건에 맞는 문서를 필터링
        - `$group`은 문서를 그룹화하고 집계 작업을 수행
        - `$sort`는 결과를 정렬
        - `$project`는 필요한 필드를 선택하거나 계산하여 결과에 포함시킴
    - 이외에 다양한 집계 작업을 위한 기능 제공
        - `$sum, $avg, $min, $max, $count` 등으로 필드를 합산하거나 평균을 계산하고, 최소/최대 값을 찾을 수 있음

## **주요 Mongodb Aggregation 문법**

- mongo db의 대표적 예시인 smaple_mflix 데이터베이스 사용.

### 1. aggregate()

- `aggregate()` 메서드는 Mongodb Aggregation 작업을 수행할 수 있게 해주는 메서드
- 이 메서드는 배열 형태로 표현하며, 맨 처음 연산부터, 순차적으로 파이프라인 연산을 수행함

```sql
db.collection.aggregate([ { <stage1> }, { <stage2> }, ... ])
```

### 2. $match

- `$match` 단계는 지정된 조건을 만족하는 문서만을 필터링하여 다음 파이프라인 단계로 전달하는 데 사용합니다.

```sql
{ $match: { <query> } }
```

- ex) 1995년에 개봉한 모든 영화를 선택하는 경우.

```sql
db.movies.aggregate([
	{ $match: { year: 1995 } }
]);
```

### 3. $group

- `$group` 단계는 Document에 대한 그룹화를 수행하여 복잡한 집계 연산을 가능하게 합니다.
- `$group` 연산자는 그룹의 키를 정의하는 `_id` 필드를 필요로 합니다.
    - `_id`는 표현식이 될 수 있으며, 이는 그룹화의 기준이 됩니다.
- 각 그룹에 대해 집계를 수행할 수 있습니다.
    - 이는 필드와 해당 필드에 적용할 누산식(accumulator)의 쌍으로 제공됩니다.
    - 이 누산식은 `$sum`, `$avg`, `$min`, `$max` 등과 같은 여러 가지 연산자를 포함할 수 있습니다.

```sql
db.collection.aggregate([
  { 
    $group: {
      _id: <expression>, // 그룹화할 필드를 지정
      <field1>: { <accumulator1> : <expression1> }, // 그룹에 적용할 누산식
      ...
    }
  }
]);
```

- ex) 모든 영화에 대한 댓글 수를 집계하는 경우

```sql
# 여기서는 각 영화를 그룹화(_id: "$movie_id")하고, 
# 각 그룹에 대해 댓글 수를 누적($sum: 1)하여 commentCount 필드에 저장
db.comments.aggregate([
	{
		$group: {
			_id: "$movie_id",
			commentCount: { $sum: 1 }
		}
	}
]);
```

### 4. 주요 accumulator

4-1. $sum : **숫자 값을 누적하여 합계를 계산**

- ex) 각 연도별로 출시된 영화의 총 수(`totalMovies`)를 계산합니다.

```sql
db.movies.aggregate([
  {
    $group: {
      _id: "$year", 
      totalMovies: { $sum: 1 }
    }
  }
]);
```

4-2. $avg : **숫자 값의 평균을 계산**

- ex)  각 연도별 영화의 IMDB 평점의 평균(`averageRating`)

```sql
db.movies.aggregate([
	{
		$group: {
			_id: "$year",
			averageRating: { $avg: "$imdb.rating" }
		}
	}
]);
```

4-3. $min & $max : 숫자 값의 평균을 계산

- ex) 각 연도별 영화의 IMDB 평점 중 최소(`minRating`)와 최대(`maxRating`) 평점

```sql
db.movies.aggregate([
	{
		$group: { 
			_id: "$year",
			minRating : { $min : "$imdb.rating" },
			maxRating : { $max : "$imdb.rating" }
		}
	}
]);
```

4-4. $push: 그룹에 있는 모든 값들을 배열로 반환

- ex) 각 연도별로 출시된 영화의 제목들(`titles`)을 배열로 묶음

```sql
db.movies.aggregate([
	{
		$group: {
			_id: "$year",
			titles : { $push : "$title" }
		}
	}
])
```

4-5. $addToSet : 중복되지 않는 값들만 배열로 반환

- ex) 각 연도별 영화 장르(`genres`)를 중복 없이 배열로 묶습니다.

```sql
db.movies.aggregate([
	{
		$group: {
			_id : "$year",
			genres : { $addToSet : "$genres" }
		}
	}
])
```

4-6. $first & $last : 그룹에서 가장 첫번째 또는 마지막 문서를 반환

- 각 연도별로 가장 먼저와 마지막으로 등록된 영화의 제목(`firstMovie`, `lastMovie`)을 찾습니다. `$first`와 `$last`는 입력 문서의 순서에 의존하므로, 일반적으로 `$sort` 연산자와 함께 사용됩니다.

```sql
db.movies.aggregate([
    {
        $sort: { "year": 1, "title": 1 }
    },
    {
        $group: {
            _id: "$year",
            firstMovie: { $first: "$title" },
            lastMovie: { $last: "$title" }
        }
    }
])
```

4-7. $strLenCP : 문자열의 길이를 반환

- ex) 각 연도별 영화 제목의 평균 길이(`avgTitleLength`)를 계산

```sql
db.movies.aggregate([
    {
        $group: {
            _id: "$year",
            avgTitleLength: { $avg: { $strLenCP: { $toString: "$title" } } }
        }
    }
]);
```

### 5. $count

- `$count` 스테이지는 파이프라인에서 들어오는 문서 수를 계산합니다. 이 스테이지는 출력 필드의 이름을 파라미터로 받아 출력 문서에 추가합니다.
- ex) 2000년 이후에 출시된 영화의 수를 계산하는 쿼리

```sql
db.movies.aggregate([
    { $match: { year: { $gte: 2000 } } },
    { $count: "movies_since_2000" }
]);
```

### 6. $sort

- `$sort` 스테이지는 입력 문서를 지정된 필드를 기준으로 정렬합니다. 정렬 필드와 순서(오름차순 : 1, 내림차순 : -1)를 지정할 수 있습니다.
- ex) 영화를 출시 연도와 제목을 기준으로 오름차순으로 정렬하는 쿼리

```sql
db.movies.aggregate([
    { $sort: { "year": 1, "title": 1 } }
]);

db.movies.aggregate([
	{ $sort: { "year": 1, "title": 1 } }
]);
```

### 7. $unwind

- `$unwind` 스테이지는 배열 필드를 "풀어"서 각각의 배열 요소가 개별 문서로 처리될 수 있게 합니다. 배열에 포함된 각 요소에 대해 입력 문서의 복사본을 생성하며, 이렇게 생성된 문서는 배열에서의 요소와 함께 출력됩니다.
- ex) 각 영화의 장르별로 문서를 생성하는 쿼리

```sql
db.movies.aggregate([
    { $unwind: "$genres" }
]);
```

### 8. $limit

- `$limit` 스테이지는 파이프라인이 출력하는 문서의 수를 제한합니다. `$limit`는 숫자를 인자로 받으며, 이 숫자는 출력할 문서의 최대 개수를 지정합니다.
- `$limit`는 주로 정렬된 데이터셋에서 상위 혹은 하위 n개의 결과를 가져올 때 사용됩니다. 이 때는 `$sort` 와 함께 사용됩니다.
- ex) 가장 높은 평점을 가진 상위 5개의 영화를 선택하는 쿼리

```sql
db.movies.aggregate([
    { $sort: { "imdb.rating": -1 } },
    { $limit: 5 }
]);
```

### 9. $project

- `$project` 스테이지는 출력 문서에 특정 필드를 선택하거나 필드의 형식을 변환하는 데 사용됩니다. 필드 이름에 대한 명시적인 표현식, 새로운 필드 생성, 필드 제외 등 다양한 기능을 제공합니다.
- ex)각 영화의 제목과 출시 연도만 출력하는 쿼리

```sql
db.movies.aggregate([
    { $project: { _id: 0, title: 1, year: 1 } }
]);
```

### 10. $concat

- `$concat` 연산자는 문자열 필드를 연결하여 새로운 문자열을 생성하는 데 사용됩니다. 여러 문자열 필드를 조합하거나 문자열과 상수를 문자열로 만들어 조합하여 새로운 문자열 값을 만들 수 있습니다.
- ex)영화 제목과 출시 연도를 조합하여 새로운 필드인 `titleWithYear`를 생성하는 쿼리입니다:

```sql
db.movies.aggregate([
	{
			$project: {
					_id: 0,
					title: 1,
					year: 1,
					titleWithYear: { $concat: ["$title", " (", { $toString: "$year" }, ")"] }
			}
	}
]);
```

### 11. $lookup

- `$lookup` 연산자는 Aggregation Framework에서 다른 컬렉션과의 조인(join) 작업을 수행하여 관련 데이터를 결합하는 데 사용됩니다. 주어진 필드에서 값이 일치하는 문서를 다른 컬렉션에서 찾아서 연결합니다.
- `$lookup` 연산자는 다른 컬렉션과의 조인 작업을 통해 데이터를 결합할 수 있어 복잡한 데이터 질의와 관련된 정보를 가져올 때 유용합니다.

```sql
{
    $lookup: {
        from: <collection>,
        localField: <field>,
        foreignField: <field>,
        as: <newField>
    }
}
from: 조인할 컬렉션의 이름입니다.
localField: 현재 컬렉션에서 조인에 사용할 필드입니다.
foreignField: 조인할 컬렉션에서 조인에 사용할 필드입니다.
as: 조인된 결과를 저장할 필드의 이름입니다.
```

- ex) comments 컬렉션의 각 도큐먼트에 대해 movies 컬렉션의 도큐먼트와 일치하는 것을 찾으려면 다음과 같이 쿼리를 작성할 수 있습니다.

```sql

db.comments.aggregate([
   {
      $lookup:
        {
          from: "movies",
          localField: "movie_id",
          foreignField: "_id",
          as: "movie"
        }
   }
])
```

- ex) users 컬렉션의 각 도큐먼트에 대해 comments 컬렉션의 도큐먼트와 일치하는 것을 찾으려면 다음과 같이 쿼리를 작성가능.

```sql
db.users.aggregate([
   {
      $lookup:
        {
          from: "comments",
          localField: "email",
          foreignField: "email",
          as: "user_comments"
        }
   }
])
```

### 12. $skip

- `$skip` 스테이지는 파이프라인에서 일정 개수의 문서를 건너뛰고 그 다음 문서들을 출력합니다.
- ex1) 첫 번째 3개의 영화를 건너뛰고 나머지 영화를 출력하는 쿼리
- ex2) 러닝타임이 100분 이상인 영화 리스트에서 상위 5개의 영화를 건너띄고, 나머지 영화를 출력하는 쿼리

```sql
# ex1)
db.movies.aggregate([
    { $skip: 3 }
]);

# ex2)
db.movies.aggregate([
    { $match: { runtime: { $gte: 100 } } },
    { $skip: 5 }
]);
```

### 13. $facet

- `$facet` 스테이지는 단일 Aggregation 파이프라인 내에서 다중 Aggregation 파이프라인을 정의하여 여러 결과 집합을 생성
- ex) 출시 연도별로 영화 수와 최고 평점을 구하는 쿼리

```sql
db.movies.aggregate([
    {
        $facet: {
            movieCountByYear: [
                { $group: { _id: "$year", count: { $sum: 1 } } }
            ],
            maxRatingByYear: [
                { $group: { _id: "$year", maxRating: { $max: "$imdb.rating" } } }
            ]
        }
    }
]);
```

### 14. $redact

- `$redact` 스테이지는 문서 내에서 보안이나 필터링 규칙에 따라 문서를 제어하는 데 사용됩니다.
- ex) 평균 평점이 7 이상인 영화만을 출력하는 쿼리

```sql
db.movies.aggregate([
    {
        $redact: {
            $cond: {
                if: { $gte: ["$imdb.rating", 7] },
                then: "$$KEEP",
                else: "$$PRUNE"
            }
        }
    }
])
# $$KEEP: 이 변수를 사용하면 현재 도큐먼트 또는 임베디드 도큐먼트를 보존하고 다음 하위 도큐먼트를 검토.
# $$PRUNE: 이 변수를 사용하면 현재 도큐먼트 또는 임베디드 도큐먼트를 제거하고 추가 검토를 중지.
```