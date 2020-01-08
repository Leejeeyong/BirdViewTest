# BirdView 화해 코딩테스트



#### token: 75a1f9372481f875e1963100aa301c56



## 기능 요구 사항

상품 목록과 상품 상세 화면으로 구성된 앱을 만드세요.
다음과 같은 기능을 가져야 합니다.

- 상품 목록
  - 스크롤에 따라 자동 로드 되어야합니다. (20개씩)
  - 상품을 터치하면 해당 상품의 detail view로 전환됩니다.
  - 목록 아래로 이동할 때 사라지고, 위로 이동할 때 나타나는 헤더를 구현하세요.
    - 정확한 동작 방식은 아래의 프로토타입에서 확인하세요.
    - 헤더의 필터 기능으로 피부 타입을 선택할 수 있어야 합니다.
    - 선택 창은 OS에서 제공하는 기본 선택창을 이용해 주세요.
  - 검색 창에 키워드를 입력하여 상품을 검색할 수 있습니다.
- 상품 상세 화면
  - 뒤로가기 버튼 클릭 시 상품 목록으로 돌아갈 수 있습니다.

각 화면은 다음과 같은 구성 요소를 가지고 있습니다.

```
- Index view
    |- 검색창 + 검색 아이콘
    |- 피부타입 필터
    |- 리스트 아이템
        |- 정사각형 썸네일
        |- 상품명
        |- 가격
- Detail view
    |- 뒤로가기 버튼
    |- 제품 이미지
    |- 제품명
    |- 제품설명
    |- 가격
```

------

## 디자인 요구 사항

- 디자인 요구사항은 Android/iOS공통입니다.

- 상세 동작은

   

  여기의 프로토 타입에서

   

  확인하실 수 있습니다.

  - [상품 목록]에서 [상품 상세]로의 전환시 애니메이션이 적용되어야 합니다.

- 디자인 가이드는 제플린으로 제공되며

   

  여기를

   

  참고하세요.

  - ID: [prgms.public+1@gmail.com](mailto:prgms.public+1@gmail.com)
  - PW: programmers

- 필요한 아이콘은 [여기에서](https://material.io/design/color/#color-usage-palettes) 다운로드 받으실 수 있습니다.

------

## API

### BASE URL

- https://6uqljnm1pb.execute-api.ap-northeast-2.amazonaws.com/prod

  > BASE URL 뒤에 원하는 리소스를 붙혀 사용합니다.
  >
  > ex) {BASE URL}/products?skin_type=oily&page=5

## Products API - Get 메소드

상품 목록을 조회 할 수 있는 기능

```
GET /products -> 피부타입이 주어지지 않으면, default로 지성 피부 성분 점수 순으로 상품 목록을 반환합니다.

GET /products?skin_type=oily -> 지성 피부에 대한 성분 점수 내림차순으로 상품 목록을 반환합니다. (20개 단위 페이징)

GET /products?skin_type=oily&page={페이지 번호} -> 페이지 번호에 해당되는 상품 목록을 반환합니다. 

GET /products?skin_type=oily&search={검색 키워드} -> 키워드에 해당되는 상품 목록을 반환합니다.
```

#### Request Header 구조

```
GET /products
Content-Type: application/json
```

### 1. 페이징 및 검색 기능

#### QueryParameter

| Parameter | Type    | Description                                                  |
| --------- | ------- | ------------------------------------------------------------ |
| skin_type | String  | (선택) 피부 타입을 의미하는 oily dry sensitive 중 1개 값을 가져야 함 |
| page      | Integer | (선택) 페이지 번호                                           |
| search    | String  | (선택) 검색 키워드                                           |

#### 요청 예시 - cURL

```
1. curl -G https://6uqljnm1pb.execute-api.ap-northeast-2.amazonaws.com/prod/products -H "Content-Type: application/json"

curl -G https://6uqljnm1pb.execute-api.ap-northeast-2.amazonaws.com/prod/products?skin_type=oily&page=10 -H "Content-Type: application/json"

2. 웹 브라우저에서 테스트
https://6uqljnm1pb.execute-api.ap-northeast-2.amazonaws.com/prod/products?skin_type=oily&page=10
```

#### Response Body 예시

```
{
  "statusCode": 200,
  "body": [
    {
      "id": 258,
      "thumbnail_image": "https://grepp-programmers-challenges.s3.ap-northeast-2.amazonaws.com/2020-birdview/thumbnail/2c7672aa-5112-4f28-bd43-79ee817d67bf.jpg",
      "title": "화해 브라이트닝 스킨 케어",
      "price": "23000",
      "{skin_type}_score": 86
    },
    ....
    ....
}
```

#### Response Body 설명

| Column          | Type    | Description      |
| --------------- | ------- | ---------------- |
| id              | Integer | 상품 코드        |
| thumbnail_image | String  | 썸네일 이미지    |
| title           | String  | 상품명           |
| price           | String  | 가격             |
| oily_score      | Integer | 지성 성분 점수   |
| dry_score       | Integer | 건성 성분 점수   |
| sensitive_score | Integer | 민감성 성분 점수 |

- 요청한 skin_type 에 대한 {skin_type}_score 점수만 반환됩니다. (default: oily)

### 2. 상세 정보 조회 기능

#### PathParameters

| Parameter | Type    | Description |
| --------- | ------- | ----------- |
| id        | Integer | 상품 코드   |

#### 요청 예시 - cURL

```
1. curl -G https://6uqljnm1pb.execute-api.ap-northeast-2.amazonaws.com/prod/products/250 -H "Content-Type: application/json"

2. 웹 브라우저에서 테스트
https://6uqljnm1pb.execute-api.ap-northeast-2.amazonaws.com/prod/products/250
```

#### Response Body 예시

```
  "statusCode": 200,
  "body": {
    "id": 250,
    "full_size_image": "https://grepp-programmers-challenges.s3.ap-northeast-2.amazonaws.com/2020-birdview/image/88acd4f2-765b-4687-930d-c34bfe6aa932.jpg",
    "title": "새라제나 노보스트라타 프리미엄 에멀전 130ml",
    "description": "something description ....",
    "price": "44910",
    "oily_score": 79,
    "dry_score": 49,
    "sensitive_score": 30,
  }
}
```

#### Response Body 설명

| Column          | Type    | Description      |
| --------------- | ------- | ---------------- |
| id              | Integer | 상품 코드        |
| full_size_image | String  | 상세 정보 이미지 |
| title           | String  | 상품명           |
| description     | String  | 상품 설명        |
| price           | String  | 상품 가격        |
| oily_score      | Integer | 지성 성분 점수   |
| dry_score       | Integer | 건성 성분 점수   |
| sensitive_score | Integer | 민감성 성분 점수 |

#### Response Status Code

| Status Code               | Description                                                  |
| ------------------------- | ------------------------------------------------------------ |
| 200 OK                    | 성공                                                         |
| 400 Bad Request           | 클라이언트 요청 오류 - page, id 요청 변수가 Integer 타입이 아닐 때, skin_type이 형식에 맞지 않을 때 |
| 404 NotFound              | 조회한 데이터가 비어있을 때, URL 경로, HTTP method 오류로 페이지를 찾을 수 없을 때 |
| 500 Internal Server Error | 서버에 문제가 있을 경우                                      |

------

## 전반적인 구현과 관련한 요청 사항

- 테스트 코드를 작성하는 경우 가산점이 있습니다.
- 프로젝트 구조 및 성능: 사용하는 프레임워크의 Best practice를 활용해서 프로젝트를 구성해 주세요.
- 기능성: 버그 없이 기능이 정상적으로 동작해야 합니다.
- 코딩 스타일: 다른 사람이 읽기 쉽고, 디버그하기 쉽도록 Clean한 코딩 스타일을 유지해 주세요.