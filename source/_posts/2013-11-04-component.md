---
published: true
layout: post
title: Developing web app client with component
comments: true
categories: component package-manager
---

[component](https://github.com/component/component) is a client package management ecosystem. It aims to resolve serveral pain points in web development.

## Advantage

With component, you may

1. include just one java-script file instead of adding required library one by one
2. the same as css
3. you may package js, css and html template in one component

Nice, huh.

With `bower`, after all dependencies installed, you shall still need to include them one by one in `html` page, like

```
<script src="bower_components/es5-shim/es5-shim.js"></script>
<script src="bower_components/json3/lib/json3.min.js"></script>
```

And you need to maintain the list manually when a new package is installed

With `component`, things are much nicer. All you need to do is add

```
<link rel="stylesheet" href="build/build.css">
<script src="build/build.js"></script>
```

Rebuild the target js and css to keep it updated.

## Background

component is initiated by [TJ](https://github.com/visionmedia). The idea is very similar to `npm`. So if you are familiar with nodejs. It should not be too difficult to understand it and use it.

For scripts, component will define `require` function which acts the same as in `nodejs`. All the scripts included in `component.json` will be wrapped into a function which is registered to `require`.

For styles, it is just concatenation.

For htmls, it will convert it to a requirable javascript file. Or you may use files to include the original one but you shall need to manage the path manually.

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

Example settings in `Gruntfile.js`

    componentbuild: {
      options: {
        dev: true,
        sourceUrls: true
      },
      components: {
        options: {
        },
        src: '.',
        dest: 'app/build'
      }
    },
    watch: {
      component: {
        files: [
          'component.json',
          'component/{,**/}*',
          'local/{,**/}*',
          'Gruntfile.js'
        ],
        tasks: ['componentbuild']
      }
    }

## Wrapping existing modules

component requires the script to be written in [commmonjs format](https://github.com/component/component/wiki/F.A.Q#why-commonjs-instead-of-amd). The [d3.js](https://github.com/mbostock/d3) project gives an example of converting existing file to that. It is as simple as three lines.

```
require("./d3");
module.exports = d3;
(function () { delete this.d3; })(); // unset global
```

Another example is the [bootstrap as component](https://github.com/brighthas/bootstrap) which shows how to package font files required by css. All you need is to include them as `files` in `component.json` and update the path. See [github commit](https://github.com/brighthas/bootstrap/commit/71911f6c7f01555ff2e4a5140d4ff3dbea51d294) for details

## Local development

During development, you may want to create some local component. You don't want to push it to Github because you know you will modify them from time to time before development is done. `component` provide `local` for that. See [todo example](https://github.com/component/todo) for how to use local and also the typical folder structure for developing app with component.

## Component with angular

In AngularJS, we have `services`, `directives`, `controllers` and etc. It is better to manage them by component rather than type. This will improve the reusability greatly.

See [jayway's article](http://www.jayway.com/2013/06/04/sharing-angularjs-modules-with-component-js) for the ideas and my ongoing work to make [control-panel-for-angular](https://github.com/rogerz/signature-cloud/tree/master/local/control-panel-for-angular) a reusable drop-in component.

(To be continued)
