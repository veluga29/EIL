# 안전한 JWT 발급에 유의해야할 점들

## Intro 

현재 회사 프로젝트에서 JWT를 사용하면서, JWT를 쿠키로 안전하게 보내기 위해 겪었던 혼란들을 기록해보고자 합니다. 

​    

## HttpOnly 옵션

기본적으로, access token과 refresh token은 서버에서 `HttpOnly` 옵션을 사용해 쿠키로 발급해주는 것이 바람직합니다. 이는 클라이언트에서 JS를 통해 쿠키로 접근하는 것을 막아주는 옵션이며, JS 코드를 심어 악의적인 명령을 실행하는 XSS(Cross Site Scripting) 공격을 예방할 수 있습니다. (글로벌 변수인 document를 사용해 document.cookie로 접근하는 것을 막아줍니다.)

​    

## Secure 옵션

서버에서 `Secure` 옵션을 사용해 JWT를 보냅시다. `Secure`는 쿠키가 HTTPS 프로토콜에서만 보내지도록 합니다. HTTP에서는 전송 중간에 쿠키가 탈취될 위험이 있기 때문에, 안전하게 HTTPS에서만 보내질 수 있도록 설정합니다.

​    

## SameSite 옵션

무언가 JWT가 잘 발급되지 않는다 싶으면, SameSite 옵션을 꼭 의심해봅니다. 서버에서 `SameSite=None`을 설정해 JWT를 보냅시다. 특히, Chrome 브라우저는 SameSite의 default 값이 `lax`로 되어 있는데 이로인해 cross-site 간의 request에 cookie가 보내지지 않을 수 있습니다.

​    

## allow-origins, allow-credentials

서버에서 CORS관련 설정들을 잘 세팅합시다. `allow-origins`에는 CORS를 허락할 클라이언트의 주소를 꼭 입력해줍니다. `allow-credentials` 옵션도 True로 설정해, 쿠키가 잘 보내질 수 있도록 합니다.

​    

## 클라이언트가 HttpOnly 쿠키를 사용하는 방법

클라이언트가 HttpOnly 쿠키를 서버로부터 전달 받았다면, 이후 해당 HttpOnly 쿠키는 클라이언트가 어떠한 request를 보낼 때마다 자동으로 쿠키에 담겨 보내집니다. 이 때, 클라이언트에서 `withCredentials=True`를 설정하고 request를 보내야 쿠키가 올바르게 전송 됩니다. Access token과 refresh token이 자동으로 담겨지는 점이 편리하죠!

​    

## Outro

직접 주어진 실무 문제는 아니었지만, 문제 해결에 함께 참여하고 개인 프로젝트에서 잘 되지 않았던 점들을 되짚어 보면서 많은 공부가 되었습니다. HttpOnly 쿠키로 JWT를 보낼 때, 여러가지 조건이 갖춰져야 비로소 올바르게 전송됩니다. 생각보다 장애물이 많은데, 잘 기록해둬야 나중에 같은 문제를 마주했을 때 덜 헤맬 것 같습니다 :)