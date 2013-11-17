---
published: true
layout: post
title: Using emacs as nodejs develop enviroment
comments: true
category: emacs
---

There was a nice [article](http://blog.deadpansincerity.com/2011/05/setting-up-emacs-as-a-javascript-editing-environment-for-fun-and-profit/) talking about using Emacs as javascript editing environment. Thanks to the support of [packages in Emacs 24](http://www.emacswiki.org/emacs/ELPA), it is now much easier to config it. And there are also many new packages available to improve the environment.

# General steps

1. Install [melpa](http://melpa.milkbox.net/#installing)
2. Install packages using `M-x list-packages` or `M-x package-install`
3. Add init command

## Recommended packages

* [helm](https://github.com/emacs-helm/helm), replacement of `anything`
* [auto-complete](http://auto-complete.org/)
* [ac-helm](https://github.com/yasuyk/ac-helm)
* [js3-mode](https://github.com/thomblake/js3-mode)
* [solarized-theme](http://ethanschoonover.com/solarized)

## Init command

Add a hook in `init.el`

	(add-hook 'after-init-hook (lambda () (load "~/.emacs.d/after-init.el")))

And call real init in `after-init.el`

	; color theme
	(load-theme 'solarized-dark t)

	; helm
	(helm-mode 1)

	; auto-complete
	(require 'auto-complete-config)
	(ac-config-default)

	; ac-helm
	(ac-complete-with-helm)

	; pair mode
	(show-paren-mode)
	(electric-pair-mode)
    
We can't call these command directly in `init.el` because packages are loaded and initialized after `init.el`, but before `after-init-hook`.
