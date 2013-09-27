---
published: 
  - true
  - "true"
layout: post
title: How to create AngularJS directive for jQuery input plugin
comments: "true"
categories: angularjs jQuery
---

To wrap a jQuery input plugin in AngularJS directive and allow data-binding, there are essentially three steps.

1. initialize the DOM element
2. update the element with value in model
3. apply the value in element to model

Let's take [spectrum](http://bgrins.github.io/spectrum) a jQuery colorpicker plugin for example.

We want to use colorpicker in angularjs' way like

```
<input colorpicker ng-model="color" />
```
When we modify the model value, we want the colorpicker to reflect the change. And vice versa, when we pick color in color picker, we need the model value to be updated.

## Initialization

In jQuery, we initialize the element by calling `$("#colorpicker").spectrum()` at the right moment. You *select* an element and attach magic things on it. That's the jQuery way.

In AngularJS, you compile the HTML to parse the directive and link it to the model. We should put DOM manipulation in directive `link` function. Obviously we don't have to select the element because it is already in the arguments and an angular element is currently compatible with jQuery if you include jQuery in your app. So what we need to do is

1. include jQuery in your HTML
2. call initialization function in directive link function

```
link: function (scope, elem, attrs) {
	elem.spectrum();
    ...
}
```

## Data binding with ng-model

Read the code first.

```
angular.module('foo').directive('colorpicker', function(){
  return {
    require: '?ngModel',
    link: function (scope, elem, attrs, ngModel) {
      elem.spectrum();
      if (!ngModel) return;
      ngModel.$render = function () {
        elem.spectrum('set', ngModel.$viewValue || '#fff');
      };
      elem.on('change', function () {
        scope.$apply(function () {
          ngModel.$setViewValue(elem.val());
        });
      });
    }
  }
});
```
The key points in the code above are

1. add dependency of `ng-model` by `require`
2. setup `$render` function to update the DOM element
3. add event listener to update value when DOM element changes

## More on NgModelController

You might be confused by `$viewValue` and `$setViewValue` at the beginning. Why model value is not mentioned here? After you read the document of [NgModelController](http://docs.angularjs.org/api/ng.directive:ngModel.NgModelController), you may wonder what are all these methods and properties.

I'll try to explain the document in a more understandable way.

### $render

> Called when the view needs to be updated.

Put a breakpoint on it and we know "when the view needs to be updated".

`ngModel.$render <- ngModelWatch <- Scope.$digest <- Scope.$apply`
 
> It is expected that the user of the ng-model directive will implement this method.

So we implement it in our directive.

### $setViewValue

> Read a value from view.

The first line of the document is misleading. The main purpose of this function is not read the view value but to update the model value as described in the third paragraph.

> It internally calls all $parsers (including validators) and updates the $modelValue and the actual model path. Lastly it calls all registered change listeners.

And we should usually put it in DOM event handler and parse the value in the DOM as argument to this function.

### $viewValue and $modelValue

If you don't push any `$formatters` or `$parsers`, the two values are actually the same. The relationship between these are as following

```
$viewValue ==($parsers)==> $modelValue
$modelValue ==($formatters)==> $viewValue
```

An example of this will be storing date as an integer and display it in specified format.

## Example

The full example can be found on [plunker](http://www.plnkr.co/edit/UIO0V2?p=preview)