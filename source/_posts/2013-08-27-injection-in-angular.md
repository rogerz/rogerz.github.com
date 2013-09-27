---
published: 
  - true
  - "true"
layout: post
title: Dependency injection in AngularJS
comments: "true"
categories: angularjs
---

One of the interesting feature of AngularJS is dependency injection.

Have a look at the last example on [AngularJS conceptual overview](http://docs.angularjs.org/guide/concepts)

```
angular.module('timeExampleModule', []).
  // Declare new object called time,
  // which will be available for injection
  factory('time', function($timeout) {
    var time = {};
 
    (function tick() {
      time.now = new Date().toString();
      $timeout(tick, 1000);
    })();
    return time;
  });
 
// Notice that you can simply ask for time
// and it will be provided. No need to look for it.
function ClockCtrl($scope, time) {
  $scope.time = time;
}
```

It worth attention that the argument name in `ClockCtrl` will be linked to the string `'time'` registered by `factory`. Or we shall say the function manufactured by `factory` under the name `time` will be injected into the function using `time` as argument name.

We can verify this by changing `time` to `time1` and we'll see the program breaks down. But if we change the order of `$scope` and `time`, it has no affect to the program.

Thus, the **name** of function argument plays an important role in AngularJS. The sequence of the argument does not matter much.

If you have a look at the code of [`injector`](https://github.com/angular/angular.js/blob/master/src/auto/injector.js), you may get an idea on how it works. The function declaration is converted to string using `.toString()` and the arguments are parsed and pushed into an array. The function will not be called directly but invoked by the `function invoke()`, which will then resolve the injection and apply the actual argument required.