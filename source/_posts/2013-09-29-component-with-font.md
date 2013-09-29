---
published: 
  - true
  - "true"
layout: post
title: Using javascript components with customized font
comments: "true"
categories: css font
---

Some front-end components includes customized font. When using these modules in your project, it is important to put the font files in the right place.

## Code never lies.

* [bootstrap.css](https://github.com/twbs/bootstrap/blob/master/dist/css/bootstrap.css)

```
@font-face {
  font-family: 'Glyphicons Halflings';
  src: url('../fonts/glyphicons-halflings-regular.eot');

src: url('../fonts/glyphicons-halflings-regular.eot?#iefix') format('embedded-opentype'), url('../fonts/glyphicons-halflings-regular.woff') format('woff'), url('../fonts/glyphicons-halflings-regular.ttf') format('truetype'), url('../fonts/glyphicons-halflings-regular.svg#glyphicons-halflingsregular') format('svg');
}

.glyphicon {
  ...
  font-family: 'Glyphicons Halflings';
  ...
}
```
It creates a new font-family and use it in class define.

* [video-js.css](https://github.com/videojs/video.js/blob/master/src/css/video-js.less)

```
@font-face{
  font-family: 'VideoJS';
  src: url('font/vjs.eot');
  src: url('font/vjs.eot?#iefix') format('embedded-opentype'),
  url('font/vjs.woff') format('woff'),
  url('font/vjs.ttf') format('truetype');
  font-weight: normal;
  font-style: normal;
}

.vjs-default-skin .vjs-slider-handle:before {
  /*content: "\f111";*/ /* Circle icon = f111 */
  content: "\e009"; /* Square icon */
  font-family: VideoJS;
  ...
```
To display a character of the font, use `content` to specify the encoding.

See [reference on MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face) for details.

## Summary

* The font file should be put in the path specified in `css` file or you may override it with your own `css` define.