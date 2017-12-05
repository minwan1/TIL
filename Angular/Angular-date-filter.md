## Angular date filter

{{ "1511145798906" | date:'medium' }}


{{ "1511145798906" | date:'medium' }}



```javascript
angular.module('test').filter('dateFormatLongToMedium', function($filter) {
  var filter = function(miliseconds) {
    console.warn(miliseconds);
    return $filter('date')(miliseconds, 'medium');
  };
  filter.$stateful = true;

  return filter;
});
```
