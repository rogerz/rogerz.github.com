---
layout: post
published: true
title: The core of connect in nodejs
comments: true
categories: nodejs
---

[Connect](http://www.senchalabs.org/connect/) describes itself as a middleware framework. The [core of connect](https://github.com/senchalabs/connect/blob/master/lib/proto.js) is in less than hundreds lines of code.

## Philosophy

As the name implies, the core function of connect is connecting the **middleware** together to build a http server. In fact it may also connect the "connect" **apps**.

A simple visualization by text list

* app
    * mw1
    * sub-app
        * mw2
        * mw3
    * mw4

The http request is handled by the root app, passed through the middleware or down to sub-app. During this procedure, the response is constructed and finally sent to the http client.

## How does it work

In connect, sub-apps are wrapped as a middleware. So the basic structure connect is

* app
    * mw1
    * mw2
    * mw3

The control flow is implemented in `app.handle()`. All the middlewares are connected by a magic `next()` call.

The `function next() {...}` just iterate through `app.stack[]`. The trunk of it is like:

```javascript
function next() {
    // get next layer from stack
    layer = stack[index++];
    // let the layer handle the request
    layer.handle(req, res);
    next();
}
```

But we won't call it a nodejs module without callback. So `next` is passed to the handler so **err can be returned**.

The majority part of the `next()` is the default error handling of connect.

```javascript
function next(err) {
    // error handling
    try {
        // call the handler
    } catch (e) {
        next(e);
    }
}
```

## Conclusion

Connect has some other parts such as routing and http header process. Before 3.x, it also binds many basic middlewares. But they are more like application sugar to make it easier to create a http server.

The core of connect is not binded to http, it is applicable for all request-response model. 

This is something I'm working on recently. A connect like middleware framework for wechat: [wechat-handler](https://github.com/rogerz/wechat-handler).