---
layout: post
published: true
title: "Maxiang - markdown editor for evernote"
comments: true
categories: misc
---

[maxiang](http://maxiang.info/) is a wonderful editor for evernote, especially for people who loves markdown writing.

At the beginning, I was wondering how it stores the original markdown text for futher editing. The author replied my mail and give me the answer. It turns out a very simple solution. **The original markdown text is stored in a hidden element in the notes**.

Export the notes and edit the HTML. At the end of the document, there is a hidden div.

```html
<center style="display:none">@%28Fusion%20C4C%29%5BLHMI%5D%0A%0A%23%20Inside%20and%20out%20Chinese%20disp
```

That's it.

This also implies that you should not edit the note created by maxiang in evernote directly because it is a one direction conversion.

The missing link to the [project page on Github](https://github.com/gockxml/dillinger)