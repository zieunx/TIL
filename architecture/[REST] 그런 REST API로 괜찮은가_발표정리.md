# [REST] 그런 REST API 로 괜찮은가?

발표영상 : https://youtu.be/RP_f5dMoHFc

---

알다가도 모르겠는 REST..

- **RE**presentational **S**tate **T**ransfer
- 컴퓨터 시스템간의 상호 운영성을 제공하는 방법(?)

## 역사

### Web (1991)

- 어떻게 인터넷에서 정보를 공유할 것인가?
	- 정보들을 하이퍼텍스트로 연결한다.
	- 표현형식: HTML
	- 식별자: URL
	- 프로토콜: HTTP
	- 을 만들었다.

### HTTP/1.0 (1994-1996)

기존의 기능을 개선한 1.0 으로 고치는 작업이 시작됐다.
기존의 HTTP 규약에 문제가 없이 HTTP/1.0을 진보시킬까?

해결책: HTTP Object Model (1998)

이걸 개선해서 만든게

### REST (2000)

> 분산 하이퍼 미디어 시스템(예: 웹)을 위한 **아키텍처 스타일**


**아키텍처 스타일?**

제약조건의 집합.
즉, 제약조건 모두를 지켜야 REST를 따르는 것.

REST는 아키텍처 스타일 이면서 하이브리드 아키텍처 스타일 이라고 한다.

## REST 를 구성하는 6가지 스타일

- Client-Server
- stateless
- cache
- **uniform interface**
- layered system
- code-on-demand (optional)
	- 코드를 서버에서 만들어서 클라이언트로 보낼 수 있어야 한다. (SSR)

대체적으로 잘 지키면서 쓴다. 왜냐하면 대부분의 특징은 HTTP만 잘 써도 지켜지는 요소다.
하지만, **uniform interface** 는 잘 지켜지지 못하는 경우가 많다.

### **uniform interface**의 제약조건

- indentification resources
	- 리소스가 URL로 식별
- manipulation of resources through representations
	- representation 전송을 통해서 리소스를 조작해야 한다. (GET, PUT, POST, DELETE, ...)
- **self-descriptive messages**
- **hypermedia as the engine of application state (HATEOAS)**


### **self-descriptive messages**: 메시지는 스스로를 설명해야한다.

**예) 요청의 경우**

```http
GET / HTTP/1.1
```

위의 경우 목적지가 빠져있다.

```http
GET / HTTP/1.1
Host: www.example.org
```

목적지를 추가해주면 된다.


** 예) 응답받는 경우**

```http
HTTP/1.1 200 OK

[ { "op": "remove", "path": "/a/b/c" } ]
```

응답을 해석하기 위한 문법을 알 수 없다.

```http
HTTP/1.1 200 OK
Content-Type: application/json

[ { "op": "remove", "path": "/a/b/c" } ]
```

`Content-Type` 을 추가해줘야 한다.

근데 이거로 되는거냐? 아니다.

```http
HTTP/1.1 200 OK
Content-Type: application/json-patch+json

[ { "op": "remove", "path": "/a/b/c" } ]
```

`json-patch+json` 미디어타입
`json-patch`라는 명세가 있다. 해당 명세를 읽고 메시지를 해석하도록 해야한다.

### HATEOAS: 애플리케이션의 상태는 Hyperlink를 이용해 전이되어야한다.

**애플리케이션 상태의 전이**

게시판을 상상해보자.

- 글목록보기 -> 글쓰기 -> 글저장 -> 글목록얻기

아래의 `html`은 HATEOAS를 만족한다.
```http
HTTP/1.1 200 OK
Content-Type: text/html

<html>
<head></head>
<body><a href="/test">test</a></body>
</html>
```
링크로 전이가 가능하기 때문이다.

**`Link` 헤더 사용하기**

```http
HTTP/1.1 200 OK
Content-Type: text/html
Link: </articles/1>; rel="previous",
      </articles/3>; rel="previous";

<html>
<head></head>
<body><a href="/test">test</a></body>
</html>
```

Link를 통해 전이되는 uri를 알 수 있다.

### 왜 uniform interface를 지켜야 하는가?

- 서버와 클라이언트가 각각 독립적으로 진화한다.
- **서버의 기능이 변경되어도 클라이언트를 업데이트할 필요가 없다.**
- REST를 만들게 된 계기

### REST 가 잘 지켜지고 있는가? = 웹

- 웹페이지를 변경해도 웹브라우저를 업데이트 할 필요가 없다.
- HTTP 명세가 바뀌어도 웹은 잘 동작한다.
- HTML 명세가 바뀌어도 웹은 잘 동작한다.

### 상호운용성을 위한 노력

- HTTP, HTML, 브라우저 등..
- 하위호환성을 지키기 위해 수많은 노력, 집착을 보인다.


**집착**

- Referer 오타지만 안고침
- charset은 인코딩으로 짓는게 적절하지만 안고침
- HTTP 상태코드 416 포기함..(만우절에 장난으로 만든 코드라 함..ㅋㅋㅋ)
- HTTP.0.9 아직 지원함(크롬, 파이어폭스)

### REST가 웹의 독립적 진화에 도움을 주었나

- HTTP에 지속적으로 영향을 줌
- Host 헤더 추가
- ,...
- HTTP/1.1 명세엔 REST에 대한 언급이 들어감
	- (represantation 에 대한 정의는 REST에서 온것이다...)

## REST API는?

저 제약조건들을 다 지켜야 하는가? 그렇다.

하지만 REST가 아니어도 된다...
시스템 전체를 통제할 수 있다고 생각하거나, 진화에 관심이없다면 ^^ ('어디한번해봐라' 인가보네요.)

**진화하는 시스템**을 만들고 싶다면 잘 지키자.

## self-descriptive 와 HATEOAS가 어떻게 독립적인 진화에 도움이 되는건가?

### self-descriptive: 확장 가능한 커뮤니케이션
서버나 클라이언트가 변경되더라도 오고가는 메시지는 언제나 self-descriptive 하므로 **언제나 해석이 가능**하다.

### HATEOAS: 애플리케이션 상태 전이의 late binding

어디서 어디로 전이가 가능한지 미리 결정되지 않는다. 어떤 상태로 전이가 완료되고 나서야 그 다음 전이될 수 있는 상태가 결정된다.

즉, **링크는 동적으로 변경될 수 있다.**

## self-descriptive 방법

### 1) Media Type

IANA 에

1. 미디어타입을 하나 정의한다.
2. 미디어타입 문서를 등록한다. (id가 뭐고 title이 뭔지..)
3. 메시지를 보고 해석..


### 2) Profile

1. 각 프로퍼티가 뭔지 정의한 명세를 작성한다.
2. Link 헤더에 profile relation 으로 명세를 링크한다.
3. 메시지를 보는 사람은 명세를 찾아갈 수 있으므로 온전히 해석 가능

```http
HTTP/1.1 200 OK
Content-Type: text/html
Link: <https://excample.org.docs/todos>; rel="profile"

<html>
<head></head>
<body><a href="/test">test</a></body>
</html>
```

단점)
1. 클라이언트가 Link 헤더(RFC 5988)와 profile(RFC 6906)을 이해해야한다.
2. Content negotiation을 할 수 없다.

## HATEOAS 방법

### 1) data로

데이터에 정의해서 내린다.

JSON으로 하이퍼링크를 표현하는 방법을 정의한 명세들도 있으니 이를 활용한다.

