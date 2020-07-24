# ✔ 웹팩(기본)
> - http://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html
## ✌ 과거의 경우
- html에 파일 두개를 올려서 다른 파일에 있는 함수를 사용했었다.
<pre>
< script src="src/math.js">< /script>
< script src="src/app.js">< /script>
</pre>
#### ❗ 하지만 이런 방법에는 문제가 존재한다.
- 전역 스코프가 오염된다.
- math.js의 sum이라는 함수는 math 모듈 안에서만 유효한 것이 아니라 해당 애플리케이션이 돌아가는 어느 곳에서나 sum 함수에 접근이 가능하다.

![sum](./img/1.PNG)

- 자바스크립트는 이런 함수 타입에다가 다른 값을 할당할 수 있다.

![sum2](./img/2.PNG)
- 이렇게 타입 에러가 발생하게 된다.
- 이렇듯 전역 스코프가 오염되게 되면 애플리케이션이 예측할수 없게 되고 결국엔 런타임 에러가 발생하게 된다.
- 이런 문제를 해결하기 위해서 IIFE 방식의 모듈을 사용을 한다.

## ✌ IIFE 방식의 모듈
- 즉시 실행 함수 표현(IIFE, Immediately Invoked Function Expression)은 정의되자마자 즉시 실행되는 Javascript Function 를 말한다.
- https://developer.mozilla.org/ko/docs/Glossary/IIFE
- 이렇게 되면 함수 안에 독립적인 스코프가 생기게 된다. (외부에서 접근할 수 없다.)
    - 즉, 전역 스코프가 오염되는 문제를 예방할 수 있다.
<pre>
var math = math || {};

(function () {
  function sum(a, b) {
    return a + b;
  }
  // 외부로 호출
  math.sum = sum;
})();
</pre>

![IIFE](./img/3.PNG)

## ✌ 다양한 모듈 시스템
- https://www.zerocho.com/category/JavaScript/post/5b67e7847bbbd3001b43fd73
- 대표적으로 `AMD` 와 `CommonJS`
- `CommonJS`는 자바스크립트를 사용하는 모든 환경에서 모듈을 하는 것이 목표로 `exports` 키워드로 모듈을 만들고 `require()` 함수로 불러 들이는 방식으로 대표적으로 서버 사이드 플래폼인 Nodejs에서 사용한다.
- 브라우저 환경에서는 [AMD](https://en.wikipedia.org/wiki/Asynchronous_module_definition)(*Asynchronous Module Definition*)는 비동기로 로딩되는 환경에서 모듈을 사용한다. (브라우저처럼 외부자바스크립트를 로딩해야할 때(비동기 환경))
- `CommonJS`와 `AMD`를 보두 지원해주는 것이 `UMD`(*Universal Module Definition*)
- 이렇게 각 커뮤니티에서 각자의 스펙을 제안하자 ES2015에서 표준 모듈 시스템을 내 놓았다.
    - 바벨이나 웹팩을 사용해서 모듈 시스템을 사용하는 것이 일반적이다.
<pre>
// export 할 때
export function sum(a, b) {
  return a + b;
}
// import 할 때
import * as math from "./math.js";
import { sum } from "./math.js";
</pre>

## ✌ 브라우져의 모듈 지원
- 모든 브라우져에서 모듈 시스템을 지원하지 않는다.(explorer..)
- 크롬 브라우저에서 사용할때
<pre>
 < script type="module" src="src/app.js">< /script>
</pre>


## ✌ 엔트리/아웃풋 실습
- https://webpack.js.org/
- app.js에서 math.js를 불러오기 때문에 의존관계가 있다고 표현한다.
#### 📌 웹팩이 하는 역할
- 모듈로 연결된 여러개의 javascript 파일을 하나로 합쳐주는 역할을 한다.
- 이렇게 하나로 합쳐진 파일을 번들(bundle)이라고 한다.
- 웹팩이 번들을 만드는 번들러 역할을 한다. (보통 웹팩을 번들러라고도 부른다.)
<pre>
$ npm i -D webpack webpack-cli
</pre>
- 설치하면 *node-modules*안에 *.bin* 폴더안에 webpack과 webpack-cli가 설치되어있는 것을 확인할 수 있다.
- webpack과 webpack-cli로 웹팩을 터미널에서 실행할 수 있게 된다.
#### 📌 웹팩을 실행할 때 필수적인 옵션 3가지
1. `--mode` 옵션 : [선택: "development", "production", "none"] 3가지 값이 올 수 있다.
    - 개발 환경이냐 운영 환경이냐에 따라서 각각 `development`, `production` 을 설정한다.
2. `--entry` 옵션 : 의존 관계가 있는 모듈들의 시작점을 entry혹은 entry point 라고 한다.
    - 모듈의 시작점을 넣어 주어야한다.
    - 이 entry를 통해서 웹팩의 모든 모듈들을 하나로 합친다.
    - 웹팩은 이 시작점을 기준으로 모든 모듈들을 찾아서 하나의 파일로 번들링 해준다.
3. `--outpout, -o` 옵션 : 그 결과를 저장해야 되는데 저장하는 경로를 설정하는 옵션

- 루트 디렉터리에서 명령어를 치면 웹팩 번들링 결과가 나온다.
<pre>
$ node_modules\.bin\webpack --mode development --entry ./src/app.js --output dist/main.js
</pre>
- dist/main.js 파일이 생겼다.
- index.html에 웹팩으로 번들링된 `dist/main.js`를 불러온다.
<pre>
< script src="dist/main.js">< /script>
</pre>
- 이렇게 되면 모든 브라우저에서 동작하게 된다.
- 결과적으로 웹팩은 여러개의 모듈을 하나의 파일로 만들어주는 역할을 한다.
- 매번 이런식으로 터미널에 긴 명령어를 입력할 수 없기 때문에 웹팩 설정 파일을 만든다.
4. `--config` 옵션 : 웹팩 설정을 지정할 수 있다. 
  -  `[문자열] [기본: webpack.config.js or webpackfile.js]`
  - `webpack.config.js` 파일 생성
<pre>
const path = require("path");

module.exports = {
  mode: "development",
  entry: { // 시작점
    main: "./src/app.js",
    main2: "./src/app2.js" // 예
  },
  output: { // 두 가지 인자가 온다
    path: path.resolve("./dist"), // 아웃풋 디렉토리 명을 입력한다.(절대경로)
    filename: "[name].js", // 번들링된 파일명을 입력한다. [name]: entry에서 설정한 키 값(main 이라는 값으로 치환된다.)
  },
};
</pre>
- `filename: "[name].js"` 이런 방식으로 하면 동적으로 파일 이름을 지정할 수 있게 된다.
- 웹팩으로 코드를 번들링하는 과정을 npm script에 등록한다.
<pre>
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    <b>"build": "webpack"</b>
  },
</pre>
- npm은 현재 프로젝트에 있는 node모듈을 찾아서 기본 파일 이름인 `webpack.config.js`을 읽어서 `webpack`으로 적는 것이 가능하여 웹팩 번들링 작업을 하게 된다.
<pre>
$npm run build
</pre>
- 터미널에 명령어를 입력했을 때랑 똑같은 결과가 나온다.

![webpack](./img/4.PNG)

## ✌ 로더
- 웹팩은 모든 파일을 모듈로 바라본다.
- 자바스크립트 뿐만 아니라, CSS, 이미지, 폰트까지도 전부 모듈로 보기 때문에 import 구문을 사용하면 자바스크립트 코드 안으로 가져올 수 있다.
- 이것이 가능한 이유는 웹팩에 **로더**가 있기 때문이다.
    - 모든 파일을 자바스크립트의 모듈처럼 만들어준다.
    - CSS 파일을 자바스크립트에서 직접 로딩해서 사용할 수 있게 해준다.
    - IMG 같은 파일 형식을 데이터 URL 형식의 문자열로 변환한 다음에 자바스크립트에서 이미지 파일을 사용할 수 있게 해준다.
### 🔸 커스텀 로더 만들기
- `my-webpack-loader.js` 작성
- `webpack.config.js` 수정
    - `test` : 로더가 처리해야될 파일들의 패턴을 입력한다. (정규 표현식)
    - `use` : 사용할 로더를 명시한다.
<pre>
  module:{
    rules:[
      {
        test: /\.js$/,   //(.js로 끝나는 모든 파일명을 로더로 돌리겠다.)
        use: [
          // 모든 .js로 끝나는 파일들에서 my-webpack-loader.js가 실행되게끔 설정
          path.resolve('./my-webpack-loader.js')
        ] 
      }
    ]
  }
</pre>
- `npm run build` 로 웹팩 실행

![loader](./img/5.PNG)

- 콘솔에 `myWebpackLoader가 동작함` 두번찍힌 이유는 src에 app.js와 math.js가 존재하고 js파일별로 로더를 실행하라고 명시했기 때문이다.
- `my-webpack-loader.js`를 `console.log`를 만나면 `alert`창을 띄우게 바꿔본다.
    - `app.js`에 있는 `console.log`를 `alert`창으로 변경하여 alert이 뜬다.
<pre>
return content.replace('console.log(','alert(');
</pre>
- 웹팩의 로더는 각 파일을 처리하기 위한 용도이다.
- 처리해야될 파일의 패턴을 명시하고 이 패턴에 걸리는 파일들은 `use`에 설정한 로더함수를 돌리도록 한다.

### 🔸 자주 사용하는 대표적인 로더
### 🌂 css-loader
- 자바스크립트에서 css 파일을 모듈로 불러올 수 있게 해준다.
- es6의 import 구문으로 가져오려면 css 파일이 모듈이 되어야만 하는데 웹팩의 로더가 이 css 파일을 모듈로 바꾸는 역할을 한다.
- 만약 로더를 사용하지 않은채 `import './app.css';` 하면 오류가 난다. (`Module parse failed`)

![cssloader](./img/6.PNG)

- 사용하기 위해서 `css-loader`를 설치한다.
<pre>
$ npm i css-loader
</pre>
- `webpack.confing.js` 수정
<pre>
{
  test: /\.css$/,
  use:[
      // 웹팩은 엔트리 포인트부터 시작해서 연결된 모든 모듈을 검색하여 css-loader를 찾는다.
    'css-loader'
  ]
}
</pre>
- 설정 후 `npm run build` 성공
- dist/main.js에 css가 있는게 확인된다.
- **하지만 브라우저상에서는 적용이 되지 않는다.**
    - html 코드가 DOM이라는 모습으로 변환이 되어야 브라우저에서 문서가 보이듯이 css 코드도 CSSOM(CSS 객체 모델)으로 바뀌어야만 브라우저에서 모습을 들어낸다.
    - 이렇게 하려면 HTML 파일에서 css 코드를 직접 불러오거나 아니면 인라인 스크립트로 넣어주어야 하는데 아직 그런 처리를 하지 않고 이 자바스크립트 파일에만 CSS 코드가 있어서 브라우저에서 나타나지 않는다.
    - 그래서 나온 것이 **style-loader**이다.
### 🌂 style-loader

- **style-loader**는 자바스크립트로 변경된 style 코드를 HTML에 넣어주는 loader 이다.
- css코드를 모듈로 사용하고 웹팩으로 번들링하려면 `css-loader`와 `style-loader`를 같이 사용해야 한다.
- `style-loader` 설치
<pre>
$ npm i style-loader
</pre>
- `webpack.config.js`에 `style-loader`추가
- **중요한 점은 로더는 한 파일에 대해서 여러 개가 실행될 수 있는데 use의 순서는 뒤에서부터 앞으로 실행되기 때문에 유의해서 설정해주어야 한다.**
<pre>
{
  test: /\.css$/,
  use:[
    // css-loader부터 실행해야한다.
    'style-loader',
    'css-loader'
  ]
}
</pre>
- 설정 후 `npm run build`를 하면 성공적으로 배경화면이 바뀌게 된다.
    - inline style로 head태그 안에 style이 정의 되어 있다.

### 🌂 [file-loader](https://webpack.js.org/loaders/file-loader/)
- CSS 뿐만 아니라 소스코드에서 사용하는 모든 파일을 모듈로 사용하게끔 할 수 있다.
- css에서 이미지를 불러올 때 에러가 발생한다.
- 이미지를 처리할 수 있는 로더가 `file-loader`이다.
<pre>
body{
    background-image: url(bg.jpg);
}
</pre>
- `file-loader` 설치
<pre>
$ npm i file-loader
</pre>
- `webpack.config.js`에 `file-loader` 추가
<pre>
{
  test: /\.jpg$/,
  use:[
    'file-loader'
  ]
}
</pre>
- 설정 후 `npm run build`를 하면 dist/ 파일에 해시값으로 이미지가 생성된 것을 확인할 수 있다.

![img](./img/7.PNG)
- 웹팩은 빌드를 할 떄 마다 unique한 값을 생성한다.
    - 하는 이유는 캐시 갱신을 위해서 처리한 것이다.
    - 정적 파일인 경우에 브라우저에서 캐시하는 경우는 자바스크립트나 css, 이미지, 폰트들을 성능을 위해서 캐시를 한다.
- 하지만 이렇게 해서 background image가 설정되지 않는다
    - 이미지 url이 설정이 제대로 안되어있기 때문이다.
- `webpack.config.js`의 `file-loader` 수정
    - `options`의 `publicPath` : `file-loader`가 처리하는 파일을 모듈로 사용했을 때 그 경로 앞에 추가되는 문자열로(아웃풋 경로가 dist로 설정되어 있다.), 파일을 호출할 때 앞에 dist를 붙치고 호출하게 된다.
    - `options`의 `name` : `file-loader` 파일 아웃풋으로 복사 할 때 사용하는 파일의 이름을 설정한다.(`[원본 파일명].[확장자 명][해시]`)
    - `[해시]` 캐시 무력화를 위해서 쿼리 스트링으로 매번 달라진 해시값을 입력한다.
<pre>
{
  test: /\.jpg$/,
  loader: 'file-loader', // 로더 이름 설정
  options:{ // 로더에 대한 옵션
    publicPath:'./dist/',
    name: '[name].[ext]?[hash]' 
  }
}
</pre>
- 설정 후 `npm run build`를 하면 ./dist 폴더에 이미지 이름대로 저장되어있는것이 확인이 가능하다.

![img](./img/8.PNG)

- `./dist/main.js`에서 `bg.jpg`를 찾아보면 접두사와 쿼리스트링으로 매번바뀌는 해시값이 설정되어 호출된 것을 확인 할 수 있다.
- 때문에 호출할 때마다 해시값이 바뀌기 때문에 캐시와 관련된 문제를 해결할 수 있다.

![img](./img/9.PNG)

### 🌂 url-loader
- 사용하는 이미지 개수가 많아지면 네트웍 리소스를 사요하는 부담이 있고 사이트 성능에도 영향을 줄 수 있다.
- 한 페이지에서 작은 이미지를 여러개 사용한다면 [Data URI Scheme](https://en.wikipedia.org/wiki/Data_URI_scheme)을 이용하는 방법이 더 낫다.
- `url-loader`라는 것은 `Data URI Scheme`처럼 이미지를 base64로 인코딩하여 문자열 형태로 소스코드에 넣는 형식이다.
- `url-loader`는 이러한 처리를 자동화해준다.
<pre>
$ npm i url-loader
</pre>
- `url-loader` 설치후 `webpack.config.js`를 수정
    - `url-loader`가 파일들을 처리할 때 `limit` 크기 미만의 파일은 `url-loader`로해서 `base64`로 변환한다.
    - `limit` 크기 이상인 경우는 `file-loader`가 실행하도록 한다.
    - `limit` 크기 미만은 javascript 문자열로 변환하고 그 이상은 파일을 복사한다.
    - 이렇게 하면 `limit` 크기 미만 파일은 `./dist/main.js`에 들어오게 된다.
<pre>
{
  test: /\.(jpg|PNG|gif|svg)$/,
  <b>loader: 'url-loader', </b>
  options:{ 
    publicPath:'./dist/',
    name: '[name].[ext]?[hash]' ,
    <b>limit: 80000,</b> // 80kb 파일 크기를 설정할 수 있다.
  }
}
</pre>
- `./dist/main.js`에 확인해보면 80kb 보다 작은 파일은 main.js에 적혀있는것을 확인할 수 있다.

![url](./img/10.PNG)