## 페이지 이동 구현 방법

Next.js를 사용해 여러 개의 페이지를 만들고 이동하는 방법을 소개합니다. Next.js는 code splitting, client-side navigation, prefetching 등을 통해, 자동으로 애플리케이션의 성능을 best performance로 최적화합니다.

​    

## 페이지 만들기

먼저 새로운 페이지를 만들어봅시다. Next.js에는 `pages` 디렉토리가 존재합니다. 해당 디렉토리에 원하는 URL로 js 파일을 생성하면, 쉽게 새로운 페이지를 만들 수 있습니다. 예를 들어, `pages/posts/first-post.js`라는 경로로 새로운 페이지를 만들었다면, 해당 페이지의 URL은 `/posts/first-post`이 됩니다.

```jsx
export default function FirstPost() {
  return <h1>First Post</h1>
}
```

그리고 위와 같이 컴포넌트를 만들고 서버를 실행하면, `http://localhost:3000/posts/first-post`에 해당 페이지가 뜨게 됩니다. 이 때, 컴포넌트는 항상 default export가 되어야 함을 유의합니다.

이러한 방식은 HTML과 PHP를 사용하여 웹사이트를 구축하는 방식과 비슷하지만, HTML 대신에 JSX와 React component를 사용했다는 점이 다릅니다. 이제 남은 것은 홈페이지에 새로운 페이지로 가는 링크만 걸어주는 것입니다!

​    

## Link component

Next.js에서 페이지의 링크를 걸어주는 것은 `<Link>` 컴포넌트를 사용해서 수행합니다.

```jsx
import Link from 'next/link'
```

이를 위해, 먼저 `'next/link'`로부터 `Link` 컴포넌트를 import합니다.

```jsx
<h1 className="title">
  Read{' '}
  <Link href="/posts/first-post">
    <a>this page!</a>
  </Link>
</h1>
```

그리고 `index.js`에서 위와 같이 코드를 작성하면, 새로 만든 페이지의 URL `/posts/first-post`로 이동하는 링크를 만들 수 있습니다. 여기서 `<Link>`가 `<a>` 태그를 감쌌다는 점, `href` 속성은 `<Link>` 태그에 주었다는 점을 유의합니다.

참고로, `{' '}`은 multiple line text를 나누기 위해 사용됩니다.

```jsx
import Link from 'next/link'

export default function FirstPost() {
  return (
    <>
      <h1>First Post</h1>
      <h2>
        <Link href="/">
          <a>Back to home</a>
        </Link>
      </h2>
    </>
  )
}
```

앞선 `pages/posts/first-post.js`에도 위와 같이 홈으로 돌아가는 링크를 만들면, 페이지끼리 서로 이동할 수 있게 됩니다.

​    

## Link and client-side navigation

 `<Link>`의 사용은 client-side navigation을 가능하게 합니다. 즉, 페이지 전환이 클라이언트 측에서 자바스크립트를 이용해 일어나기 때문에, 페이지의 모든 부분을 서버에서부터 새로 가져와 로딩하는 브라우저 기본 navigation 방식보다 훨씬 빠르게 동작합니다.

만일 `<Link>`가 아닌 `<a>` 태그를 사용했다면, 브라우저는 해당 링크에 접근할 때마다 페이지 전체를 refresh할 것입니다.

​    

## Code splitting

Next.js에서는 code splitting이 자동적으로 일어나므로, 페이지의 로딩도 해당 페이지에 반드시 필요한 것들만 로딩됩니다. 예를 들어, 홈페이지가 렌더링될 때는 다른 페이지들은 로딩되지 않습니다. 특히, 애플리케이션에 수 많은 페이지가 있을 때, 유저는 자신이 요청한 페이지를 보다 빠르게 볼 수 있게 됩니다. 즉, 페이지들의 코드는 각각 분리되어 있고, 어떤 특정 페이지가 오류를 일으켜도 애플리케이션의 나머지 부분은 문제없이 동작합니다.

​    

## Prefetching

브라우저의 viewport(메뉴 바, 탭 영역을 제외한 브라우저의 순수 화면 영역)에 `<Link>` 컴포넌트가 있을 때마다, Next.js는 `<Link>`에 연결된 페이지들을 자동으로 미리 로딩해둡니다. 이를 prefetching이라고 하며, 이러한 페이지들은 유저가 링크를 누를 때 background에서 이미 로딩되어 있어서 매우 빠르게 페이지가 전환됩니다.

​    

## Reference

[Next.js Document](https://nextjs.org/learn/basics/navigate-between-pages)