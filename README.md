# oauth2-jwt-learn

## [RFC 6749] 요약

(<https://datatracker.ietf.org/doc/html/rfc6749#section-1.3.3>)
​

## 1\. Introduction

​

- 전통적인 client-server관계에서는 3rd-party에 제한된 리소스를 제공받기 위해, Resource Owner는 credentials ( id, pw등 개인정보 )를 3rd-party에게 공유했다. 하지만 이러한 방식은 하기와 같은 문제점들이 있다.
​

1. 3rd-party가 받은 credential을 미래에 사용하기 위해 저장할수도 있다
2. 서버가 취약한 비밀번호 인증을 지원해야한다.
3. 3rd-party가 지나치게 광범위한 자원접근 권한을 가지더라도 기간적으로도 제한할 수 없고, 하위 자원에대한 제한도 할수 없다.
4. Resource Owner는 개별적으로 3rd-party 권한취소를 할 수 없다.
5. 3rd-party에 문제가 생기면 엔드유저 ( Resource Owner ) 의 패스워드에도 영향을 끼치고, 또한 그 패스워드로 보호되는 데이터 리소스에도 문제가 생긴다.
​

- 이러한 문제점을 없애기 위해, Resource Owner의 역할에서 리소스를 요청하는 역할인 Client 역할을 분리해내었다. 또한 Resource Owner가 직접 credential을 사용하는 것을 Client가 Access Token을 획득하는 것으로 대체 하였다. ( Access Token은 기간과 범위에 대한 내용이 들어가있다. )
​

## 1.1 역할

​

### Resource Owner

​

- 보호된 자원에 엑세스 권한을 부여할수 있는 개체
- 사람이면 end-user라고도 한다.
​

### Resource Server

​

- 보호된 자원들을 호스팅하는 서버
- Access Token 을 이용한 보호된 자원 요청에 허가 및 응답해준다.
​

### Client

​

- Resource Owner를 대신해 권한과 함께 보호된 자원요청을 하는 어플리케이션
- 구체적인 설정을 내포하지는 않는다. ( 원격 서버, 로컬 컴퓨터, 또는 다른 디바이드가 될 수 있다. )
​

### Authorization Server ( 인증 서버 )

​

- Resource Owner와 권한을 인증하고, Client에게 Access Token을 부여하는 서버.
​

## 1.2 프로토콜 흐름

​

### Figure 1: Abstract Protocol Flow

​

```
+--------+                               +---------------+
|        |--(A)- Authorization Request ->|   Resource    |
|        |                               |     Owner     |
|        |<-(B)-- Authorization Grant ---|               |
|        |                               +---------------+
|        |
|        |                               +---------------+
|        |--(C)-- Authorization Grant -->| Authorization |
| Client |                               |     Server    |
|        |<-(D)----- Access Token -------|               |
|        |                               +---------------+
|        |
|        |                               +---------------+
|        |--(E)----- Access Token ------>|    Resource   |
|        |                               |     Server    |
|        |<-(F)--- Protected Resource ---|               |
+--------+                               +---------------+
```

​

### A

​

- Client는 Resource Owner로 부터 권한요청을 한다. ( 직접적 )
- 하지만 Client가 Authorization Server의 중개자 역할만하는 간접적인 방식을 더 선호한다. "Authentication Grant Type"
​

### B

​

- Resource Owner는 Client에게 credential을 준다.
- 하지만 B과정은 "Password Grant Type" 또는 확장된 Grant Type 에서만 이루어진다.
​

### C

​

- Client는 Authorization Server에게 Authorization Grant ( credential ) 로 Access Token을 요청한다.
​

### D

​

- Authorization Server는 Client와 Authorization Grant가 유효하면, Access Token을 Client에게 준다.
​

### E

​

- Client는 Access Token로 보호된 리소스를 Resource Server에게 요청한다.
​

### F

​

- Resource Server는 Access Token을 검사하고 유효하다면 요청에 응답한다.
​

## 2\. Client Registration

​

- 프로토콜을 시작하기에 앞서, Client는 Authorization Server에 등록되어야 한다.
- 신뢰 구축과 client type, redirection url과 같은 Client type이 획득 가능하다면 다른 방법을 사용해도 괜찮다. ( \* 반드시 직접적인 상호작용이 필요한 것은 아니다. )
- 등록은 자체 발행, 3자 발행, 신뢰 채널을 통한 Client Discovery를 통해 이루어 질 수 있다.
- Client를 등록할 때, 개발자는 다음 3가지는 꼭 해야한다.
  - Client Type 지정하기
  - Client Redirection URIs 제공하기
  - Authorization Server가 요구하는 정보 제공하기 ( 앱 이름, website, logo, 법적 동의서, 등)
​

### 2.1 Client Type

​

- "confidential", "public" 2가지가 존재한다
​

#### confidential

​

- Credential을 지킬만한 능력이 있는 보안 Client
- e.g., credential에 제한된 액세스를 가지는 보안서버
​

#### public

​

- Credential을 지킬만한 능력이 없는 일반 Client
- e.g., Resource Owner가 사용하는 Device또는 브라우저 기반의 어플리케이션

## [RFC 7523] 요약

- <https://datatracker.ietf.org/doc/html/rfc7523>

### Title : JSON Web Token (JWT) Profile for OAuth 2.0 Client Authentication and Authorization Grants

- JSON Web Token - RFC 7159 에서 정의

### Introduction

- JWT가 보안토큰으로써 어떻게 OAuth2.0 프레임워크내에서 발행되고 사용될 수 있는지 기술하고 있다.

- 또한 어떻게 JWT Bearer Token가 어떻게 인증서버의 직접적인 사용자승인 단계없이  Access Token을 요청하는지에 대해 기술하고있다.

### Authorization Grant에서 JWT

- access token 요청은 OAuth Assertion Framework[RFC7521]에서 기술

#### 요청 예시

```txt
POST /token.oauth2 HTTP/1.1
Host: as.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&assertion=eyJhbGciOiJFUzI1NiIsImtpZCI6IjE2In0.
eyJpc3Mi[...생략...].
J9l-ZhwP[...생략...]
```

- "grant_type" - "urn:ietf:params:oauth:grant-type:jwt-bearer"
- "assertion" - JWT
- "scope" - (Optional) 권한 범위에 대한 정의

### Client Authentication에서 JWT

```txt
POST /token.oauth2 HTTP/1.1
Host: as.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&
code=n0esc3NRze7LTCu7iYzS6a5acc3f0ogp4&
client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3A
client-assertion-type%3Ajwt-bearer&
client_assertion=eyJhbGciOiJSUzI1NiIsImtpZCI6IjIyIn0.
eyJpc3Mi[...생략...].
cC4hiUPo[...생략...]

```

- "grant_type" - "authorization_code"
- "client_assertion_type" - "urn:ietf:params:oauth:client-assertion-type:jwt-bearer"
- "client_assertion" - JWT

### 보안토큰으로서의 JWT의 필수요소

```json
{
  "iss":"https://jwt-idp.example.com",
  "sub":"mailto:mike@example.com",
  "aud":"https://jwt-rp.example.net",
  "nbf":1300815780, // not before: 이시간 이전에는 invalid
  "exp":1300819380,
  "http://claims.example.com/member":true // other claims
}
```

- iss (issuer) - JWT를 발행한 엔티티의 고유 식별자 (ex. 인증서버 url)
- sub (subject) - JWT의 주체 식벌 (ex. ys538.kim@samsung.com)
  - Authorization Grant에서는 승인된 접근자( 리소스 소유자나 위임자 )
  - Client Authentication에서는 "client_id"
- aud (audience) - 의도된 대상으로서 승인 서버 (ex. 토큰 endpoint url)
- exp (expiration time) - 토큰 만기 시간
