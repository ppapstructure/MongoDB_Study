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