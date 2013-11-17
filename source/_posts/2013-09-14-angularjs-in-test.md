---
published: true
layout: post
title: Understanding AngularJS in unit test
comments: true
categories: angularjs
---

Below is a typical unit test case for `directives` in AngularJS. In a unit test, we try to isolate the module under test from others.

In the following example, we are testing the directive `sign-pad` which will insert a `canvas` into the DOM and attach a `signPad` to the scope. How shall we test it without a real DOM?

```
describe('Directives: signPad', function () {
  var element, scope;

  beforeEach(module('ngSignPadApp'));
  beforeEach(module('directives/sign-pad.html'));

  beforeEach(inject(function ($compile, $rootScope) {
    element = angular.element('<sign-pad></sign-pad>');
    scope = $rootScope;
    $compile(element)(scope);
    scope.$digest();
  }));

  it('should contain a canvas', function () {
    expect(element.find('canvas').length).toBe(1);
  });

  it('should bind signPad to scope', function () {
    expect(scope.signPad).toBeDefined();
  });
});
```

It actually carry out every underlying steps required in real AngularJS app. Take a look at the conceptual view.

![concept](http://docs.angularjs.org/img/guide/concepts-startup.png)

## register modules

First, we register the required modules for `inject` later. `module` is exported to `window` in `angular-mocks.js`.

## inject

Inside `inject()`, we create $injector from the registered modules and `invoke` the anonymous function. The `invoke` will apply `$compile` and `$rootScope` service to the function.

## compile

Next, we create an element using `angular.element` and compile it by `$compile(element)` then link it to the `scope` by `$compile(element)(scope)`

## digest

Process all the watches.
