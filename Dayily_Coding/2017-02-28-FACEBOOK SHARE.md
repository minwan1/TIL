# Daily Coding 2017-02-28-FACEBOOK SHARE


### facebook share
1.가장 먼저 앱키를 발급받아야한다. [앱키발급](https://developers.facebook.com/apps/)
2.자신의 URL서비스 주소등록 (설정-설정-플랫폼추가(자신의 서비스URL등록)

```javascript
$.ajaxSetup({ cache: true });
  $.getScript('//connect.facebook.net/ko_KR/sdk.js', function(){
  FB.init({
    appId      : '앱키', //앱키입력
      xfbml      : true,
      version    : 'v2.8'
  });
  FB.ui({
       method: 'share',
       mobile_iframe: true,
       href: 'https://www.naver.com', //공유하고자하는 URL
       title: 'test', //공유제목
         description: 'test', //공유 내용
         picture:'https://encrypted-tbn1.gstatic.com/images?q=tbn:ANd9GcQ7wXxZt-_vCPKBU6obu44T9PtcrGRQ6GwBrKgmvO2OaBuULpxR-A',
         message: 'zxczcxz'
     }, function(response){});
});
```
링크를 통한 공유
```javascript
<a href="https://www.facebook.com/sharer/sharer.php?u='your address'" target="blank">
```







참고링크
* [https://developers.facebook.com/apps](https://developers.facebook.com/apps)
