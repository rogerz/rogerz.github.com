---
layout: post
published: false
title: "Inside and out co's example"
comments: true
categories: javascript
---

```javascript
co(function *(){
  var a = yield get('http://google.com');
  var b = yield get('http://yahoo.com');
  var c = yield get('http://cloudup.com');
  console.log(a.status);
  console.log(b.status);
  console.log(c.status);
})()
```

```javascript
function co(fn) {
  return function (done) {
    var ctx = this;
    next();
    
    function next(err, res) {
      var ret;
      ret = gen.next(res);
      if (ret.done) return done(null, ret.value);
      ret.value = toThunk(ret.value, ctx);
      ret.value.call(ctx, function (){
      	next.apply(ctx, arguments);
      })
      return;
    }
  }
}
```