# Git Commit 전에 ESLint 자동화하기
보통 프론트엔드에서는 2인 이상 개발할 때 코드 컨벤션을 잡아주는 ESLint 오픈 소스를 사용합니다. 그런데 실제로 커밋을 하기전에 lint 검사를 하지 않거나 build 명령어를 실행하고 커밋하지 않는 이상 ESLint 코드 컨벤션을 어긴 코드들이 커밋될 수 있습니다. 이러한 문제점을 해결해주는것이 [husky](https://github.com/typicode/husky), [lint-staged](https://github.com/okonet/lint-staged), [prettier](https://github.com/prettier/prettier)가 있습니다. 이것에 대해 간단히 알아보겠습니다.


 ESLint를 사용하더라도 

## husky 
깃 커밋 또는 푸쉬전에 eslint, test 등을 실행 해볼 수 있는 도구입니다. 만약 테스트 또는 eslint 문법을 어겼다면 방금 커밋하려고했던 파일들은 커밋이 되지 않습니다.

### husky 설치
```
npm install husky --save-dev
```

### husky 사용법
아래와같이 커밋이나 푸쉬전에 테스트나 eslint등을 테스트할 수 있습니다.
```
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "eslint --fix",
      "pre-push": "npm test",
      "...": "..."화
      
    }
  }
}
```

## lint-staged
staged된 파일들을 lint 해주는 도구입니다. 전체 프로젝트를 ESLint를 실행하는것은 속도가 느릴 수 있는데 lint-staged를 사용하면 특정 패턴에 파일에대해서만 lint 수행을 가능하게 해줍니다.

### lint-staged 설치
```
npm install --save-dev lint-staged husky
```


### lint-staged 사용법
아래는 .js파일에대해 "eslint --fix" 명령어를 실행하고 해당 파일들을 스테이지에 올려줍니다.
```json
{
  "husky": {
    "hooks": {  
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.js": ["eslint --fix", "git add"]
  }
}
```

## prettier
코드 컨벤션에 맞지 않는 코드를 고쳐주는 도구입니다. ES2017을 포함해서 JSX, typescript, flow를 지원하고, 심지어 CSS, LESS, SCSS 까지 지원합니다.

```
foo(reallyLongArg(), omgSoManyParameters(), IShouldRefactorThis(), isThereSeriouslyAnotherOne());
```
위와 같은 코드가있으면 prettier에 옵션에 맞는 코드를 작성하면 커밋전에 아래와같이 코드를 수정해줍니다.
```
foo(
  reallyLongArg(),
  omgSoManyParameters(),
  IShouldRefactorThis(),
  isThereSeriouslyAnotherOne()
);
```

### prettier 설치
npm install husky lint-staged prettier --save-dev

### prettier 사용법
아래 설정은 커밋 전에 staged된 모든 js파일을 대상으로 콤마를 찍어주고, 홑따옴표를 사용하고, 탭의 길이가 4로 수정되도록 설정했다.
```json
{
  "scripts": {
    "precommit": "lint-staged"
  },
  "lint-staged": {
    "*.js": [
      //prettier [opts] [filename ...]
      "prettier --write --trailing-comma es5 --single-quote true --tab-width 4",
      "git add"
    ]
  }
}
```
나머지 옵션은 [여기](https://prettier.io/docs/en/options.html)를 확인해주시면 됩니다.