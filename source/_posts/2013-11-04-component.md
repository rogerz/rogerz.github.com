---
published: 
  - true
  - "true"
layout: post
title: Developing web app client with component
comments: "true"
categories: webapp
---

[component](https://github.com/component/component) is a client package management ecosystem. It aims to resolve serveral pain points in web development.

## Advantage

With component, you may

1. include just one java-script file instead of adding required library one by one
2. the same as css
3. you may package js, css and html template in one component

Nice, huh.

## Background

component is initiated by [TJ](https://github.com/visionmedia). The idea is very similar to `npm`. So if you are familiar with nodejs. It should not be too difficult to understand it.

There is something they didn't say much but could be useful.

## Debugging

component build all javascript into one file, which makes it difficult to follow when debugging. Luckily it provide `--dev` option to generate script file with `sourceUrl`. You shall read lots of code like

```
require.register("mbostock-d3/d3.js", Function("exports, require, module",
"d3 = function() {\n\
  var d3 = {\n\
```

The original code is converted to a concated string! This is supposed to be parsed by the development tools. In Chrome, you may find the original files in `(no domain)` under `source` panel.

## Auto building

component requires to be built before use. You may find [grunt-component-build](https://github.com/anthonyshort/grunt-component-build) combine with `watch` very useful.

## Wrapping existing modules

component requires the script to be written in [commmonjs format](https://github.com/component/component/wiki/F.A.Q#why-commonjs-instead-of-amd). The [d3.js](https://github.com/mbostock/d3) project gives an example of converting existing file to that. It is as simple as three lines.

```
require("./d3");
module.exports = d3;
(function () { delete this.d3; })(); // unset global
```

(To be continued)