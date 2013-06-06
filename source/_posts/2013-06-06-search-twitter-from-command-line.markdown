---
layout: post
title: "Search twitter from command-line"
date: 2013-06-06 20:55
comments: true
description: "Search twitter from command-line using their REST API v1.1."
categories: [python, twitter]
---

Recently, I started learning the Twitter API.
Twitter has deprecated [v1](https://dev.twitter.com/docs/api/1) of its API, and the [v1.1](https://dev.twitter.com/docs/api/1.1) of its API requires the use of `OAuth`.

So, the first step is to download an `OAuth` library. I am going to use [`simplegeo/python-oauth2`](https://github.com/simplegeo/python-oauth2).
You can download it from [pypi](https://pypi.python.org/pypi/oauth2/) and install it by executing `sudo ./setup.py install`.

Here is a script which uses the API to search twitter from the terminal.

{% gist 5722371 twitter_search.py %}

To use this script, you first need to create an application at https://dev.twitter.com/apps

You will get your `consumer_key` and `consumer_secret` from there.
You will also need to create an access token for your account.
After doing that you will get your `access_token_key` and `access_token_secret`.

Fill these four values in `twitter_search.py` and you are good to go.
{% codeblock lang:bash %}
$ python twitter_search.py SEARCH TERM(S)
{% endcodeblock %}
