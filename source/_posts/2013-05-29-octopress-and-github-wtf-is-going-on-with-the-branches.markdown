---
layout: post
title: "Octopress and Github: WTF is going on with the branches?"
date: 2013-05-29 19:50
comments: true
categories: [git, github, octopress]
description: "Understanding the local and remote branches of your octopress blog deployed using github pages."
---
This blog is powered by Octopress and hosted using Github Pages.
When I checked the github repository for this blog, I found that there were two branches: `master` and `source`.
But locally there was only one: `source`.
{% codeblock %}

$ git branch
* source
{% endcodeblock %}

To understand what was going on, I dug up the [docs](http://octopress.org/docs/deploying/github/).

Initially when you clone the octopress repo from [imathis/octopress](https://github.com/imathis/octopress), the local branch is called `master` and the remote `imathis/octopress` is called `origin`.

The [magic](https://github.com/imathis/octopress/blob/1bd2b62a80f3a7be330cb9600a2206a678e6863d/Rakefile#L315) happens when you do `rake setup_github_pages`.

It renames the remote pointing to `imathis/octopress` from `origin` to `octopress`; adds your Github Pages repository as the default `origin` remote and renames the `master` branch to `source`.
It then creates a new directory `_deploy/` and initializes a new git repository there.
*Finally, it adds your Github Pages repository as the default `origin` remote for this new repository also.*

Now, when you do `rake deploy`, the `master` branch of the git repo in `_deploy/` is pushed to the `master` branch of your Github Pages repo. And when you push the source repo to github, it becomes the `source` branch of your Github Pages repo.

Here are some more details:

{% codeblock %}
$ cd octopress/  # THE_DIRECTORY_WHERE_YOU_CLONED_OCTOPRESS
$ git remote show origin
* remote origin
  Fetch URL: git@github.com:mindprince/mindprince.github.io.git
  Push  URL: git@github.com:mindprince/mindprince.github.io.git
  HEAD branch: master
  Remote branches:
    master new (next fetch will store in remotes/origin)
    source tracked
  Local ref configured for 'git push':
    source pushes to source (up to date)
{% endcodeblock %}

{% codeblock %}
$ cd octopress/_deploy/
$ git remote show origin
* remote origin
  Fetch URL: git@github.com:mindprince/mindprince.github.io.git
  Push  URL: git@github.com:mindprince/mindprince.github.io.git
  HEAD branch: master
  Remote branches:
    master tracked
    source new (next fetch will store in remotes/origin)
  Local ref configured for 'git push':
    master pushes to master (up to date)
{% endcodeblock %}

To summarize, the two branches `source` and `master` on your Github Pages repo correspond to two separate git repositories: one in your `octopress/` directory and another in the `octopress/_deploy/` directory.

The fact that separate git repositories on your local machine can act as different branches of the same repository on github is interesting. It can probably be misused to host more than five private repositories in [Github's Micro plan](https://github.com/plans).
