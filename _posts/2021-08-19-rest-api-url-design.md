---
layout: post
title: "REST api URL 디자인"
subtitle: "패턴화 가이드라인들"
date: 2021-08-19 12:30:15 +0900
background: '/img/posts/05.jpg'
---


## 자주 언급되는 가이드들


### URL에 동사나 행동을 표현하지 않고, 명사만을 사용한다
URL은 리소스가 어디에 위치할 수 있고, 어떤 행동들이 수행될 수 있는지 나타내는 경로이다. 그렇기 때문에 동사 등으로 행동을 표현하기 보다, method를 통하여 표현하는 것이 올바르다.

예를 들어,
``` 
Get   /adduser
GET   /postuser
GET   /deleteuser
GET   /updateuser
```
보단,
```
GET     /user
POST    /user
DELETE  /user
PUT     /user
```
가 훨신 더 보기 좋다. 이러한 점에 있어서 아래의 URL과 메소드를 통하여 일관적이고 정확한 행위를 표현할 수 있게 된다.


### HTTP methods를 사용한다
위에서 언급한 내용과 일맥상통하는 내용이다. URL에서 행동을 설명하기 위한 단어들을 제거했을때, 그 행동은 HTTP methods를 통해서 설명하게 된다. 현재 명세에서 제공하고 있는 methods는 9가지가 있는데, 각 methods들에 대한 설명은 MDN의 글[링크](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods)를 통해서 더 알아보면 좋을 것 같다. 

1. GET
2. HEAD
3. POST
4. PUT
5. DELETE
6. CONNECT
7. OPTIONS
8. TRACE
9. PATCH

이 9가지의 Methods 중 자주 자용하는 것은 GET, POST, PUT, DELETE 일 것이다. 이 methods를 sql과 연관을 지어 보자면 select, insert, update, delete 정도로 설명할 수 있을 것이다. 

위의 예제를 조금 더 설명을 덧붙이자면, 다음과 같겠다
```
GET     /user                * user들을 조회
GET     /user/34             * user (id: 34)를 조회
POST    /user                * 새로운 user 생성
DELETE  /user                * 기존 user 전체 삭제
DELETE  /user/34             * 기존 user 특정 유저(34) 삭제
PUT     /user/34             * 기존 user 특정 유저(34) 업데이트
```
URL이 자원에 대한 경로를 의미하는 것이니까, /user라는 폴더를 가지고 생각하는 것 또한 편할 것이다. user라는 디렉토리가 있고, 그 하위에 user의 id가 디렉토리 명이 되어서 폴더들이 쭉 나열되어 있다. 그러한 폴더들에 대한 행동을 methods로 표현하게 된다.

### HTTP 응답 상태 코드를 사용한다
클라이언트에서 api 서버측에 요청을 보내고, 그 응답으로 요청이 성공적으로 실행되었다면 그 응답과, 필요시에 데이터를 첨부해서 보내야 할 것이고 그렇지 않을 경우에 에러를 반환하여야 클라이언트 측에서 내 요청이 정상적으로 실행 되었는지, 혹은 실행이 되었긴 했는데 어떻게 처리가 되는건지, 되고 있는건지 알 수 있을 것이다. 이러한 점을 알려주기 위해서 HTTP에서 미리 정해놓은 응답 상태 코드를 가지고 응답해 주는것이 좋다.

HTTP 응답 상태 코드에는 수십가지가 있고, 자주 쓰는 것만 뽑자면
1. 200: OK, 요청을 성공적으로 수행
2. 400: Bad request, 클라이언트의 요청사항을 서버가 이해하지 못하여 정상적으로 수행 불가
3. 404: Not Found, 요청 리소스 사용 불가
4. 500: Internal Server Error, 요청은 유효하지만, 서버 내 문제로 오류 발생
5. 503: Service Unavaiable, 서버 다운 등의 이유로 request 처리 불가 상태
정도가 있지 않을까 생각이 든다.

적절한 응답 코드와 함께 성공적으로 수행시 데이터 반환이 필요한 경후 첨부하여 응답하면 될 것이다.
더 많은 응답 상태 코드들에 대해서 알아보고 싶다면, MDN의 글[링크](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)를 통하여 알아보면 좋을 것이다.


### 네이밍 컨벤션
하나의 네이밍 컨벤션을 사용하기로 했다면, 전체적으로 일관성을 가지도록 하는것이 중요하다. 카멜케이스와 스네이크 케이스와 케밥케이스를 섞어서 자용하는 것 보다는, 하나의 컨벤션으로 통일하는것이 일관적이고 나중에 보기에 편리하다.

### 검색, 정렬, 필터링, 페이지네이션
GET을 통해서 데이터를 조회할 때, 검색이나 소팅, 필터링, 페이지네이션을 위한 methods는 제공되지 않는다. 이러한 경우를 지원하기 위해서 query parameter를 통해 행동을 처리할 수 있게 된다.

```
GET    /user?search=34
GET    /user?sorting=asc
GET    /user?member-type=admin
GET    /user?page=3
```
위의 예제를 볼 때, 전체 유저들을 대상으로 검색하고, 오름차순으로 정렬하여 반환하고, 필터링하고, 페이지로 나누어 특정 페이지만을 조회하는 URL에 대해서 간략하게 나타내고 있다. 이러한 query paramter를 지원하기 위해서는 추가적인 기능 개발이 필요하지만, 클라이언트가 필요로 하는 정보를 검색하고 나타내는데 많은 도움을 줄 수 있을 것이다.


### 버저닝
개발하고 있는 api가 특정 버전을 기점으로 사양 변경이 이루어질 경우, 버전 표시를 통해서 이전 버전과의 차이점을 두고 명시를 할 수 있을 것이다
```
GET http://api.my.site/v1/user
GET http://api.my.site/v2/user?member-type=admin
```
예를 들어 초기 버전에서 지원하지 않던 필터 등이 버전 2에서 지원된다거나, 응답 json의 형식이 버전 2에서 달라지거나 하는 등의 변경이 있을때 버저닝을 통해서 알리는 것이 좋다.




### 캐싱
모든 데이터를 데이터베이스에서 불러오는 것은 처리 성능과 데이터베이스 서버에 많은 영향을 끼칠 수 있다. 이러한 것을 사전에 방지하고 성능 향상을 위해서 캐싱을 하는 것이 권장된다.
node.js의 express 경우에는 apicache라는 미들웨어가 존재하고 많은 설정 없이도 메모리 캐싱을 할 수 있다.
하지만 처리량이 많아짐에 따라 분산 처리 등을 고려했을때 메모리 케싱에 대해서 고민해야 할 부분들 또한 많아지게 된다.


### 보안
요청 데이터에 대한 검증은 기본적이고, 인증된 사용자에게 서비스를 제공하기 위해서 api 키와 같은 것들을 고려할 수 있을 것이다. 
API KEY는 해싱 등의 알고리즘을 이용해서 생성하고, 시간단위, 일 단위로 재발급을 통해서 KEY가 탈취당했을 때 피해를 최소화 할 수 있다. 
이러한 기본적인 인증을 넘어, Oauth와 같은 프레임워크와, 파생되어 나온 JWT등을 이용하여서 보안을 강화할 수 있을 것이다.




## 로이 필딩이 말하는 REST Api

### REST Api === REST 아키텍쳐를 따르는 API
REST 는 분ㅅ나 하이퍼 시스템을 위하여 만들어진 아키텍쳐 스타일이고, 아키텍쳐 스타일이란 제약 조건의 집합이다.
흔히 인식되는 REST API는 쉽고, 제약 조건이 적고, 단순하다는 인삭과 달리 REST 개념을 창시한 로이 필딩은 자신이 만든 REST의 개념을 잘 지키고 있는 REST 서비스가 없다며 Microsoft에서 만든 CMIS(Rest 바인딩을 지원하는 CMS 표준)의 깃 레포까지 찾아가서 NO REST in CMIS라는 말을 남기며 이렇게 바꿔달라고 풀 리퀘스트까지 남겼다.
그러면서 REST 아키텍쳐를 따르지 않을꺼라면 REST API라는 말을 쓰지 않거나, 다른 단어를 써 달라고 까지 하는데, 이 창시자가 말하는 REST API는 뭔가??


### REST 가 가져야 할 필수 요건들
1. Client-Server
2. Stateless
3. Cache
4. Uniform Interface
5. Layered System
6. Code-on-demand(optional)


이중 필수인 1-5 까지, 4를 제외하고는 http 명세만 잘 따라도 지킬수 있는 것들이다. 하지만 4, Uniform Interface는 잘 지켜지지 않는 제약 조건이다.

그럼 Uniform Interface에서 지켜야 할 세부 항목들이 뭐가 있는지도 살펴보자


### Uniform Interface
1. Identification of resources
2. Manipulation of resources through representations
3. Self-descriptive messages
4. Hypermedia as the engine of application state(HATEOAS)

1은 URI로 리소스가 식별되는것이기 때문에 대부분의 REST들이 만족하고 있고, 2는 HTTP 헤더에 GET, POST등의 메소드등을 통해서 리소스를 조작하는 것, 이것 또한 대부분의 REST API가 만족하고 있다. 
하지만 3번, 메세지만 보고도 해석이 가능한지와 4번, 어플리케이션의 상태는 Hyperlink를 이용해 전이되어야 한다와 같은 명세는 지켜지지 않는 것이 대부분이다.

이제 안지켜진다는 3번과 4번 항목에 대해서 또한 알아보자



### Self-descriptive messages
REST API가 반환하는 메세지는 스스로 설명 가능해야 한다 라는 뜻이다.
그 단순하게 설명하자만, 반환하는 메세시 Content-type을 정의하고 같이 보내라! 라고 할 수 있겠다.

``` http
HTTP/1.1 200 OK

[{"op": "remove", "path":"/a/b/c"}]
```
위와 같은 메세지를 API가 반환했을 때, Body에 어떤 타입의 데이터가 들어가 있는지 클라이언트에서는 알 방법이 없다. 이와 같은 메세지를 이렇게 바꿔보자.

``` http
HTTP/1.1 200 OK
Content-Type: application/json

[{"op": "remove", "path":"/a/b/c"}]
```
위의 메시지는 Body의 데이터가 application/json 타입이라는 것을 명시하고 있다. 하지만 op가 뭔지, path가 무엇을 의미하는지는 알 방법이 없다.
이러한 점을 보완하기 위해서
``` http
HTTP/1.1 200 OK
Content-Type: application/json-patch+json

[{"op": "remove", "path":"/a/b/c"}]
```
으로 변경하게 된다면, op가 무엇을 의미하는 것인지, path가 무엇을 의미하는지 알 수 있다.
위 명세에 대한 것을 rfc6902에서 명세하고 있으며, 해당 [링크](https://www.rfc-editor.org/rfc/rfc6902.html)에서 그 내용을 볼 수 있다.

근데 사람들이 다 rfc에서 등록된 것들을 해야 하는가? 이 점에 대해서는 IANA에서 뭐 미디어 타입을 등록할 수 있도록 폼을 열어놓긴 했는데...[링크](https://www.iana.org/form/media-types) 사실상 할 수 있을까..?

그래서 Profile이라는 방법이 있다.
자기가 op가 뭐고, path가 뭔지 정의한 명세를 작성해서, 헤더 link에 rel="profile"로서 해당 도큐를 같이 보내는 것이다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
Link: <http://example.org/docs/operation; rel="profile">

[
    { "op": "remove", "path": "/a/b/c"}
]
```
클라이언트가 Link 헤더와 profile을 이해할 수 있다면, 해당 메세지는 self-descriptive라고 할 수 있다.

### HATEOAS
data에 다양한 방법으로 하이퍼링크를 표현하면 된다.
```
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json
Link: <http://example.org/docs/todos>; rel="profile"
{
    "data":[{
        "type": "todo",
        "id"  : "1",
        "attributes": {"title": "출근하기" },
        "links": {"self" : "http://example.com/todos/1"}
    },
    {
        "type": "todo",
        "id"  : "2",
        "attributes": {"title": "퇴근하기" },
        "links": {"self" : "http://example.com/todos/2"}
    }]
}
```

JSON으로 하이퍼링크를 표현하는 방법을 정의한 명세들을 활용하면 되겠다.
다른 방법으로는 HTTP 헤더로 링크를 표현할 수 있다
``` http
HTTP/1.1 204 No Content
Location: /todos/1
Link: </todos>; rel="collection"
```

결국 HATEOAS는 두가지 방법 다 적제적소에서 사용하면 된다




## 결론
설계된 아키텍쳐를 따라 API를 설계하고 구현하는 것에는 많은 제약조건들이 따른다
하지만 그 명세들을 지켰을 때, 서버와 클라이언트의 독립적인 성장이 이루어질 수 있게 되고, 그 결과 버저닝에 비교적 자유로운 API를 만들수 있게 된다는 것이다.
지금 하고 있는 프로젝트에도, 이 모든것들을 다 적용시켜서 완성시킬 수 있었으면 좋겠다.
갈 길은 멀지만, 그래도 어떻게 하면 되지 않을까 싶다