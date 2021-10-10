## CSS, assets and metadata

Next.js에서는 CSS를 어떻게 적용하여 스타일링할 수 있을까요? 그리고 이미지와 같은 정적 파일들과 `<title>`과 같은 페이지 내 메타 데이터들은 Next.js에서 어떻게 다뤄야 할까요?

​    

## Asset with `<Image>` and image optimization

이미지와 같은 정적 파일들은 `public` 디렉토리에 위치시킵니다. Next.js는 `public`에 있는 파일들을 자동으로 참조합니다. 이미지를 저장하기 위해 `public` 디렉토리에  `images` 디렉토리를 생성하고, 그 안에 원하는 이미지를 저장하세요. (예를 들어, 프로필 사진을 사용하기 위해 `profile.jpg`를 저장해보세요.)

```jsx
import Image from 'next/image'

const YourComponent = () => (
  <Image
    src="/images/profile.jpg" // Route of the image file
    height={144} // Desired size with correct aspect ratio
    width={144} // Desired size with correct aspect ratio
    alt="Your Name"
  />
)
```

저장한 이미지는 `Image` 컴포넌트를 `next/image`에서 임포트해 사용합니다. `height`와 `width` 속성을 사용해 이미지의 렌더링 사이즈를 지정해주고, `src`로 이미지의 위치를 설정해줍니다.

기존 HTML `<img>` 태그는 브라우저의 화면 크기가 바뀔 때마다 변화에 대한 이미지의 resizing을 지원하지 않습니다. 반면에, Next.js의 `<Image>` 컴포넌트를 사용하면, 해당 이미지의 resizing을 자동으로 지원해줍니다.

또한, `<Image>` 컴포넌트는 이미지의 포멧도 브라우저에서 WepP와 더 나은 이미지 포멧을 지원한다면, 자동으로 포멧을 변환해서 이미지 파일을 optimization해줍니다. 뿐만 아니라, 애플리케이션의 빌드 타임에서 이미지를 로딩하는 대신, 이미지가 viewport에 나올 때 비로소 lazy-loading하여, 페이지 전체 로딩 시간을 원활히 합니다.

​    

## Metadata

페이지의 메타 데이터를 변경하고 싶다면, Next.js의 `<Head>` 컴포넌트를 사용합니다. HTML `<head>`와는 달리, `<Head>`는 리액트 컴포넌트입니다.

```jsx
import Head from 'next/head'
```

`<Head>` 컴포넌트는 `'next/head'`에서 임포트합니다.

```jsx
export default function FirstPost() {
  return (
    <>
      <Head>
        <title>First Post</title>
      </Head>
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

그리고 원하는 메타 데이터를 `<Head>` 컴포넌트 안에서 설정해줍니다. 위 코드는 페이지의 `<title>` 속성을 변경했습니다. 개발자 도구에서 해당 페이지의 HTML 문서를 확인해보면, 실제로 `<head>`에 `<title>` 태그가 추가되어 있는 것을 볼 수 있습니다.

​    

## CSS styling

```jsx
<style jsx>{`
  …
`}</style>
```

Next.js에서 CSS는 `<style jsx>` 태그에 작성하면 됩니다. `<style jsx>`는  styled-jsx 라이브러리를 사용해 지원되는 것이며, Next.js는 built-in으로 제공됩니다. CSS와 Sass 역시 마찬가지로 built-in으로 지원됩니다.

​    

## Layout component & CSS module

CSS 스타일을 적용하기 위해, `Layout` 컴포넌트와 `CSS module`을 사용해봅시다.

먼저 최상위 디렉토리에 `components` 디렉토리를 하나 생성합니다. 

```jsx
export default function Layout({ children }) {
  return <div>{children}</div>
}
```

그리고 `components/layout.js`를 생성하여 위와 같은 Layout 컴포넌트를 작성합니다. 이 Layout 컴포넌트는 모든 페이지에 걸쳐 사용될 것입니다.

```jsx
import Head from 'next/head'
import Link from 'next/link'
import Layout from '../../components/layout'

export default function FirstPost() {
  return (
    <Layout>
      <Head>
        <title>First Post</title>
      </Head>
      <h1>First Post</h1>
      <h2>
        <Link href="/">
          <a>Back to home</a>
        </Link>
      </h2>
    </Layout>
  )
}
```

그리고 CSS를 추가하고 싶은 페이지에 `<Layout>` 컴포넌트를 감싸서 적용해줍니다.

```css
.container {
  max-width: 36rem;
  padding: 0 1rem;
  margin: 3rem auto 6rem;
}
```

`<Layout>`에 적용해줄 CSS는 CSS Module을 사용해 생성합니다. CSS Module은 CSS 파일을 임포트해 리액트 컴포넌트에서 사용하는 것을 도와줄 것입니다. `components/layout.module.css` 파일을 생성하여, 위와 같이 원하는 CSS 코드를 작성합니다. 특히, CSS Modules를 사용하기 위해서는 생성한 CSS 파일의 이름이 반드시 `.module.css`로 끝나야함을 유의합니다.

```jsx
import styles from './layout.module.css'

export default function Layout({ children }) {
  return <div className={styles.container}>{children}</div>
}
```

끝으로, Layout 컴포넌트에 CSS를 적용합니다. `layout.module.css` 파일을 임의의 이름에 임포트해 사용합니다. 여기서는 `styles`를 사용합니다. 그리고 Layout 내에서 `className` 속성을 사용해 `styles.container`를 적용합니다. 이 후, http://localhost:3000/posts/first-post 페이지에 들어가보면, CSS가 잘 적용된 것을 확인할 수 있습니다.

​    

> Unique class name의 자동 생성
>
> CSS가 적용된 해당 페이지에서 개발자 도구를 열어 HTML 문서를 확인해보면, Layout 컴포넌트로 인해 렌더링된 다음과 같은 class name으로 새로운 `<div>`가 생성되어 있는 것을 볼 수 있습니다.
>
> * `<div class="layout_container__2t4v2">`
>
> 이는 CSS Module이 자동으로 생성한 고유한 class name입니다. 뒷 부분의 고유 문자열 덕분에 class name이 충돌할 여지는 없습니다.
>
> 또한, Next.js의 code splitting은 CSS Module에서도 적용되어, 현재 페이지가 로딩될 때 필요한 최소한의 CSS만 함께 로딩되게 됩니다. 

​    

## Global CSS

만일 모든 페이지에서 항상 적용 및 로딩되는 CSS를 원한다면, `pages/_app.js` 파일을 생성하고 `_app.js` 파일 내부에서 해당 CSS 파일을 임포트하면 됩니다.

```jsx
export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

먼저, `pages/_app.js` 파일을 생성하고 파일 내부에 위 컴포넌트를 작성합니다. App 컴포넌트는 가장 최상위 컴포넌트로서 모든 페이지에 영향을 줍니다. 특히, 페이지들 간의 이동이 있을 때, App 컴포넌트에 state을 저장해두면 유용합니다.

그리고 `npm run dev`로 서버를 다시 실행해줍니다. `_app.js`를 추가했을 때는 항상 서버를 다시 실행해줘야 변경사항이 저장됨을 유의합니다!

```css
html,
body {
  padding: 0;
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen, Ubuntu,
    Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
  line-height: 1.6;
  font-size: 18px;
}

* {
  box-sizing: border-box;
}

a {
  color: #0070f3;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

img {
  max-width: 100%;
  display: block;
}
```

그리고 최상위 디렉토리 밑에 `styles` 디렉토리를 하나 만들어, 위와 같이 원하는 CSS 코드를 `styles/global.css`로 파일을 생성해 저장합니다.

```jsx
import '../styles/global.css'

export default function App({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

그리고 `pages/_app.js`에서 `global.css`를 임포트해주면, 페이지를 이동해도 `global.css`의 내용이 항상 적용되는 것을 확인할 수 있습니다.

여기서 주의할 점은 `global.css`는 항상 `_app.js` 내에서 임포트해줘야 한다는 것입니다. `global.css`는 항상 모든 페이지에 영향을 주어야 하기 때문입니다.

​    

## Reference

[Next.js Document](https://nextjs.org/learn/basics/navigate-between-pages)
