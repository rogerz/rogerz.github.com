---
layout: post
title: "Octopress+Prose+Github+Travis CI = coders' blog"
date: 2013-02-21 23:40
comments: true
categories: 
published: true
---

# Introduction

This is powered by the following project or services

* [Octopress](http://octopress.org): blogging framework
* [Github pages](http://pages.github.com/): site hosting
* [Travis CI](http://travis-ci.org): site generating
* [Prose](http://prose.io): content editor

# How to

## Step 1 - deploy Octopress on Github

This is not difficult, just follow the steps on [Octopress Document](http://octopress.org/docs/).

## Step 2 - add configuration for Prose

Just add the following content to `_config.yml`. This file is used both by Octopress and Prose.

    #prose.io settings 
    prose: 
      rooturl: "source" 
      metadata: 
        "source/_posts": | 
          layout: post 
          title: "Title" 
          comments: true 
          categories:  
          published: true 

Refer to [Prose handbook](prose.io/help/handbook.html) for details

## Step 3 - create a Github OAuth token for Travis CI

This provide a relative secure way to push content to your Github repository from Travis CI.

    curl -u 'rogerz' -d '{"scopes":["public_repo"], "note":"Travis access"}' https://api.github.com/authorizations
    
    Enter host password for user 'rogerz':
    
    {
      "id": 1695515,
      "url": "https://api.github.com/authorizations/1695515",
      "app": {
        "name": "Travis access (API)",
        "url": "http://developer.github.com/v3/oauth/#oauth-authorizations-api"
      },
      "token": "dfb6d666b6535cf19df9398273d4beb854b61345",
      "note": "Travis access",
      "note_url": null,
      "created_at": "2013-02-20T12:03:28Z",
      "updated_at": "2013-02-20T12:03:28Z",
      "scopes": [
	    "public_repo"
      ]
    }

More details on [Github blog](https://github.com/blog/1270-easier-builds-and-deployments-using-git-over-https-and-oauth).

## Step 4 - add configuration for Travis CI

Encrypt your Github token

	travis encrypt GH_TOKEN=your_token

and add it to `.travis.yml`

    ---
    branches:
      only:
      - source
    language: ruby
    rvm:
    - 1.9.3
    before_script:
    - export REPO_URL="https://$GH_TOKEN@github.com/$GH_REPO.git"
    - rake setup_github_pages[$REPO_URL]
    script:
    - rake generate
    after_script:
    - rake deploy
    env:
      global:
      - GH_REPO="rogerz/rogerz.github.com"
      - secure: "V+j9SSuxyhwN5fB3b6Xj7WZ6kuuaSgCTHaiHHfGtPfwSCBZtxmaLrUdbuOEG\nKKpk0UX5nJoqEM0nmgAT1I5KKI2vkx4TX+mHnNQ0rUInipXTf7C9z/42OxKM\nrY5wgcD0G8ChccFAzJNYqFEXXkmiaYDWxjwlslA8pOZKSmHH8z4="
      
More on [Travis CI help](http://about.travis-ci.org/docs/user/build-configuration/#Secure-environment-variables)

## Step 5 - customize the `Rakefile`

Hide Github token during `rake`

    -      puts "Added remote #{repo_url} as origin"
    +      puts "Added remote as origin" # don't put repo_url in travis-ci as it may contains token

    -    system "git push origin #{deploy_branch} --force"
    +    system "git push origin #{deploy_branch} --force --quiet" # hide github token

Modify regex to parse url of HTTPS

    -    puts "(For example, 'git@github.com:your_username/your_username.github.com)"
    +    puts "(For example, 'git@github.com:your_username/your_username.github.com' or 'https://github.com/your_username/your_username.github.com')"

    -  user = repo_url.match(/:([^\/]+)/)[1]
    +  user = repo_url.match(/[\/:]([^\/]+)\/[^\/]+$/)[1]

Skip unnecessary building in Travis CI ([More](http://about.travis-ci.org/docs/user/how-to-skip-a-build/)).

    -    message = "Site updated at #{Time.now.utc}"
    +    message = "Site updated at #{Time.now.utc}\n\n[ci skip]"

    -    system "git commit -m \"Octopress init\""
    +    system "git commit -m \"Octopress init\n\n[ci skip]\""

## Step 6 - turn on Travis CI and push

Check [my commit]( https://github.com/rogerz/rogerz.github.com/commit/49e2c9c4b20748300a2db67a7b23a8a584cad86e) for all modification required. Turn on Travis CI service hook and push the changes to `source` branch. You are ready to post in [Prose](http://prose.io) now.
