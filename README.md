# oauth2-jwt-learn

## [RFC 7523] 요약

- https://datatracker.ietf.org/doc/html/rfc7523

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
