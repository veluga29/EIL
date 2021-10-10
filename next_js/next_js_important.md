# Next.js 주요한 특징들

## Static Generation VS Sever-side Rendering VS Client-side Rendering

* Static Generation

  HTML이 build time에 pre-rendering되는 방식입니다. 즉, 외부에서 가져오는 데이터들도 build time에 요청하기 때문에 최신 데이터보다는 잘 변하지 않는 데이터들을 처리하기에 적합합니다.

  미리 HTML을 생성하기 때문에 SEO에 강점이 있습니다. 또한 미리 한 번 생성된 HTML을 재사용하는 것과 더불어 Sever-side Rendering과 달리 CDN에 캐시되는 덕분에 셋 중에 속도가 가장 빠르며, Next.js에서 가장 권장되는 방법입니다.

* Sever-side Rendering

  HTML이 유저로부터 request가 있을 때마다 pre-rendering되는 방식입니다. 즉, 외부에서 가져오는 데이터들이 request 시점에 요청된 데이터들이기 때문에, 최신 데이터들을 사용하기 용이하다는 장점이 있습니다.

  HTML이 pre-rendering되기 때문에, SEO에 강점이 있으며, Static Generation보다는 느리지만 Client-side Rendering 보다는 빠릅니다.

  다만, 사용자 측면에서는 페이지 이동마다 화면이 깜빡거리며 새로고침이 발생하게 됩니다.

* Client-side Rendering

  HTML의 pre-rendering 및 외부 데이터 API 요청을 하지 않고, 클라이언트 측에서 자바스크립트 코드로 모든 것을 처리하는 방식입니다. 사용자가 요청한 페이지만 불러온 후, 사용자의 행동에 따라 필요한 부분만 다시 읽어 들이는 single page application 방식으로 동작하게 됩니다. 따라서, 사용자 측면에서 리로딩없이 필요한 부분만 빠르게 인터랙션할 수 있습니다.

  다만, 초기 구동 속도가 느리고 SEO가 어렵다는 단점이 있습니다. (구글에서는 Client-side Rendering도 SEO를 잘 할 수 있다고 이야기하지만, Client-side Rendering은 SEO가 잘 안된다는 것이 정설입니다.)

위의 렌더링 방식들은 페이지마다 다르게 적용할 수 있고, 한 페이지 안에서도 부분마다 다르게 적용할 수 있습니다.

예를 들어, 보통 SEO가 가장 잘되어야 하는 부분은 상품 정보 페이지이므로 해당 페이지는 Static Generation이나 Sever-side Rendering으로 처리하는 것이 좋습니다. 또한, 상품 정보 페이지 내에서 title 같은 정보는 잘 변하지 않으므로 Static Generation을 사용하는 것이 좋습니다. 반면에, description이나 keyword 같은 부분들은 A/B Test 등으로 자주 변화를 시도해 볼 수 있기 때문에, Sever-side Rendering을 사용하는 것이 적합합니다. 이외의 데이터와 상관없는 navigation bar나 메뉴 같은 부분들은 Client-side Rendering을 적용해 보다 나은 인터랙션을 제공할 수 있습니다.



## Static file serving

Next.js는 static 파일을 public 디렉토리에서 처리합니다. 그리고 public 폴더 안에 있는 static file들은 base URL을 `/`로 사용할 수 있습니다. 예를 들어, `/public/me.jpg`는 `/me.jpg`로 사용하면 됩니다.

Public 디렉토리에 있는 파일들은 빌드 타임에만 서빙되므로, 런타임에 저장되는 파일들은 AWS S3 같은 다른 서드 파티 서비스를 사용해 처리하길 권장합니다.

​    

## Reference

[Next.js Document](https://nextjs.org/learn/basics/navigate-between-pages)
