## webpack 이란
브라우저 로딩시 여러개의 js를 로딩해야한다면 비효율적일것입니다. 이러한 문제를 해결하기위해서는 웹팩을 통해 html과 js 등을 빌드해서 minify, uglify 하여 배포해야합니다. 여기에서 minify, uglify 파일은 글자 압축 파일압축을 의미합니다. 먼저 웹팩을 사용하기위해서는 아래의 5가지 property 개념을 알아야합니다.

* entry
* output
* loader 
* plugins
* mode

차례대로 살펴보겠습니다.

### Entry
웹팩에서 모든것은 모듈이다. 자바스크립트, 스타일 시트, 이미지등 모든것은 모듈에 해당합니다. 아래와같이 각 모듈들은 다른 모듈에 의존합니다. 웹팩에서는 이러한 시작점을 엔트리라고 부릅니다.

![](https://i.imgur.com/qdYSkDQ.jpg)
웹팩은 엔트리를 통해서 필요한 모듈을 로딩하고 하나의 파일로 묶는다. 그럼 먼저 엔트리를 아래와같이 설정해보겠습니다.

webpack.config.js
```javascript
module.exports = {
  entry: {
    main: './src/main.js',
  }
}

```
위와같이 설정하게되면 HTML에서 사용할 자바스크립트이 시작점은 src/main.js입니다. 


### Output
output은 웹팩에게 번들링한 js파일을 어디에 위치시킬지를 설정해주는 property입니다. 기본으로 ./dist폴더에 main.js가 위치하게 됩니다. default 경로를 다시한번 정리하자면 ./dist/main.js입니다.


webpack.config.js
```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  }
};
```
다음과 같이 output 에 path와 filename을 정의해주면 위치와 파일이름을 커스터아미징해서 번들링을 할 수 있다. 저 지정된 위치는 기본으로 루트프로젝트에 아래에 위치 된다. 이렇게 dist에 떨어진 js를 사용하기위해서는 HTML에 아래와같이 설정하여 사용할 수 있습니다.

```HTML
<body>
  <script src="./dist/bundle.js"></script>
</body>
```

### Loaders
웹팩은 원래 자바스크립트 json파일만을 취급합니다. Loader는 웹팩에게 나머지 파일 모듈들을 웹팩이 유효한 모듈로 인식할 수 있도록 변환 해줍니다. 먼저 loader 2개의 property값에 대해 알아보겠습니다.
1. test property는 어떤 형식의 파일들을 변환할건지 설정하는 property입니다.
2. use property는 어느 loader를 통해 변환할지를 설정하는 property입니다.

```javascript
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};
```
위에 구성되어진 룰들을 풀어보면 웹팩은  이름이 .txt로 끝나는 파일들을 만나게되면 raw-loader라는 컴파일러로로 변환하게 된다.

### Plugins
플러그인은 번들 최적화 한경변수 주입등을할 수 있습니다. plugin을 사용하기위해서는 아래와같이 `require('html-webpack-plugin')` 를 추가해야합니다. 그리고 이러한 plugin은 옵션들을 통해 커스터마이징이 통한다. 이러한 플러그인은 설정은 또 쓰여질 수 있기 때문에 new operator를 사용합니다.

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
const webpack = require('webpack'); //to access built-in plugins

module.exports = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};
```


웹팩이 제공해주는 [플러그인 리스트](https://webpack.js.org/plugins/)입니다.



### Mode
웹팩은 `mode` 파라미터를 development, production or none, 으로 사용함으로써 각 환경에 맞게 최적화된 빌드를 할 수 있습니다. 기본 value는 production입니다.
```javascript
module.exports = {
  mode: 'production'
};
```


## 정리

* **웹팩**은 기본적으로 모듈 번들러이다.
* 의존성 그래프에서 **엔트리**로 그래프의 시작점을 설정하면 웹팩은 모든 자원을 모듈로 로딩한 후 아웃풋으로 묶어준다. 
* **로더**로 각 모듈별로 바벨(자바스크립트 버전 변환), 사스변환(css 변환) 등을 처리하고 이결과를 **플러그인**이이 받아 어글리파이, 텍스트 추출등의 작업을 한다.