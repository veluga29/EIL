# 서버에서 JWT를 안전하게 발급하는 방법은 무엇일까?

## Intro

이전 개인 프로젝트에서 JWT로 로그인을 구현할 때, access token을 response body에 담아 보낸 기억이 있습니다. 사실 httpOnly 쿠키로 보내려고 했지만, 그 때는 서버에서 전달받은 쿠키를 프론트에서 어떻게 사용해야 할지 방법을 찾지 못해 불가피하게 이용한 방법이었습니다. (여러 JWT 인증 예제에서 request body로 access token을 보내는 경우가 심심치 않게 보인 점도 한 몫했습니다.)

JWT의 access token은 서버에서 httpOnly로 보내는게 무조건 옳은 것일까? 프론트에서는 httpOnly로 받은 access token을 어떻게 처리할까? Response body로 보냈을 때 생기는 문제는 무엇일까? 해결하지 못한 고민들은 계속 남아 맴돌기에, 이번 기회에 가볍게 정리해보고자 합니다.

​    

## XSS(=CSS), CSRF(=XSRF)

먼저, JWT에서 주요하게 이슈가되는 기본적인 보안 문제는 XSS와 CSRF 공격입니다. 따라서, 단순하게는 XSS와 CSRF를 막는 방식으로 접근하는 편이 바람직해보입니다.

* XSS(=Cross Site Scripting)
  * 해커가 JS같은 스크립트 코드를 URL 혹은 Input에 악의적으로 삽입해 피해자의 웹브라우저에서 실행시키는 공격을 말합니다.
  * 피해자의 브라우저에 저장된 중요한 정보들을 빼내올 수 있습니다.
  * CSS가 이미 약자로 있기 때문에, XSS라고 더 많이 불리는 것 같습니다.
  * 보통 중요한 데이터를 전송할 때 httpOnly 쿠키를 사용하면, XSS 공격을 막을 수 있습니다.
* CSRF(=Cross Site Request Forgery)
  * 해커가 정상적인 request를 가로채 피해자인척하고 변조된 request를 서버에 보내, 서버에서 악의적인 동작을 수행하도록 만드는 공격을 말합니다.
  * 피해자의 개인정보가 수정 및 유출, 원치 않는 광고성 포스팅 작성 등의 피해가 있을 수 있습니다.

​    

## 프론트와 httpOnly 쿠키 옵션

서버에서 쿠키를 설정할 때 (set_cookie) httpOnly 옵션을 줄 수 있습니다. 서버에서 httpOnly를 적용해 쿠키로 보낸 값들은 클라이언트에서 직접 접근이 불가능합니다. (`document.cookie`로 접근 불가능) 다만, 이후 request를 할 때마다 **해당 쿠키가 자동으로 쿠키 헤더에 담겨 request와 함께 보내집니다.**

httpOnly는 JS로 쿠키에 접근할 수 없으므로, XSS 공격을 막을 수 있습니다. 반면에, 매 request마다 자동으로 쿠키 헤더에 담겨 보내지는 특징 때문에 CSRF 공격에 취약점을 가질 수 있습니다.

​    

## Secure 쿠키 옵션

Secure은 클라이언트 혹은 서버에서 https에서만 쿠키를 전송할 수 있도록 허용하는 옵션입니다. httpOnly는 클라이언트에서 JS를 통한 탈취 문제는 해결할 수 있지만, 네트워크를 직접 감청하여 쿠키를 가로채는 공격을 막을 수 없습니다. 특히, http에서는 데이터가 암호화되지 않고 전달되기 때문에, request나 response가 중간에 탈취당하면 그대로 데이터를 노출하게 됩니다. 

따라서, 데이터가 암호화되어 보내지는 https에서만 통신 가능하도록 secure 옵션을 설정할 필요가 있습니다.

​    

## JWT를 발급하는 경우의 수

경우의 수는 refresh token과 access token을 모두 사용하는 것을 기준으로 고려합니다.

### Case 1 - refresh token, access token을 모두 httpOnly 쿠키로 보내기

- access token을 httpOnly 쿠키 헤더로 보내면, XSS 공격을 충분히 막을 수 있습니다.
  - httpOnly이기 때문에, 프론트에서 JS를 통해 쿠키에 접근할 수 없고 해커도 이를 이용할 수 없습니다.
  - refresh token도 마찬가지입니다.
- 반면 CSRF에 취약합니다.
  - Request에 access token이 항상 자동으로 담겨 보내지므로, request를 위조하는 CSRF를 막기 어렵습니다.
  - refresh token도 마찬가지입니다.

### Case 2 - refresh token은 httpOnly 쿠키로, access token은 response body로 보내기

- access token은 프론트에서 클로저 등을 통해 private variable로 저장하고 관리합니다.
  - 이 때, XSS, CSRF 문제는 없어집니다.
  - 혹시나 https 이외의 통신이라면 response body의 중간 탈취 위험은 있을 수 있지만, refresh token은 탈취되지 않기 때문에 유효 기간이 짧은 access token만 탈취되고 이후 갱신은 어려울 것입니다.
  - 다만, 새로고침이 일어날 때마다 access token이 휘발성으로 사라지기 때문에, refresh token으로 새로운 access token을 발급받아야 합니다. (access token 유효기간의 의미가 사라지는 것 같기도...)
- refresh token의 경우
  - httpOnly이므로 XSS 공격 문제가 없습니다.
  - Request에 refresh token이 항상 자동으로 담겨 보내지지만, CSRF를 시도해도 해커는 access token을 알 수 없습니다. 
    - 해커가 refresh token을 사용해 새로운 access token을 서버에 요청할 수는 있어도, response body로 날라오는 access token은 해커가 아닌 사용자에게로 갈 뿐입니다.

### Case 3 - refresh token, access token을 모두 response body로 보내기

* access token과 refresh token을 프론트에서 클로저 등을 통해 private variable로 저장하고 관리합니다.
  * 새로고침이 일어날 때마다 refresh token과 access token이 사라지므로, 로그인 유지가 되지 않습니다.
  * 즉, XSS, CSRF 공격 위험과 멀어지지만 로그인 기능과도 멀어(?)집니다.
* 만일 https 이외의 통신이라면, access token 뿐만 아니라 refresh token까지 탈취당하여 더 오랜 기간동안 위험할 수 있습니다.

​    

## Outro

결론적으로 위 Case 중에서는 Case 2가 보안상으로 가장 best한 방법으로 생각됩니다. 다만, 새로고침 시 access token이 유지되지 않는 점에서 다시 cookie의 필요성이 생각나는 무언가 아쉬운 부분이 느껴집니다.

이 포스팅은 더 좋은 방법을 알게될 때마다 계속 업데이트해 나가야 할 것 같습니다 :)

​    

## Reference

[JWT는 어디에 저장해야할까? - localStorage vs cookie](https://velog.io/@0307kwon/JWT%EB%8A%94-%EC%96%B4%EB%94%94%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%B4%EC%95%BC%ED%95%A0%EA%B9%8C-localStorage-vs-cookie)

[프론트에서 안전하게 로그인 처리하기 (ft. React)](https://velog.io/@yaytomato/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%90%EC%84%9C-%EC%95%88%EC%A0%84%ED%95%98%EA%B2%8C-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%B2%98%EB%A6%AC%ED%95%98%EA%B8%B0)

[01. 시큐리티 - HTTP Only 와 Secure Cookie](https://theheydaze.tistory.com/550)