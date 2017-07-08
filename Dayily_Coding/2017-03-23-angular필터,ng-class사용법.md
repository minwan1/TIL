# Daily Coding 2017-03-23일 angular 필터,ng-클래스

## LDAP이란
> LDAP(LightWeight Directory Access Protocol)이란 네트워크상에서 조직이나 개인정보 혹은 파일이나 디바이스 정보등을 찾아 보는것을 가능하게 만든 프로토콜


2개이상 필터 html에서
```html
{{transferInfo.srcMoney | addMoneyFormat : transferInfo.srcCurrency}}
```

```javascript
app.filter('addMoneyFormat',function(){

	return function(money,currency){
    var tmp = Number(money.toString().replace(/,/g,""));	//remove commas
    var numberLength = 0;
    if(currency.toUpperCase()=='USD'){
      numberLength = 2;
    }
    return tmp.format(numberLength,3,',','.');
	}

});

//http://stackoverflow.com/questions/16227325/how-do-i-call-an-angular-js-filter-with-multiple-arguments
```

angular js ng-class사용법
```javascript
ng-class="{on:true}"
```






참고
* [조대협의 블로그](http://bcho.tistory.com/851)
* [백발의 개발자](http://m.blog.naver.com/jjoommnn/130181901609)



##semantic ui
active << 클래스 색넣는거 파란색

visible<< 가장앞에서 화면 보여주는것(드럽박스할떄)
