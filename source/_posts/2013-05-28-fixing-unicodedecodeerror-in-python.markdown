---
layout: post
title: "Fixing UnicodeDecodeError in Python"
date: 2013-05-28 21:09
comments: true
categories: [troubleshoot, coding, python]
description: "Unicode, string encodings, utf-8 and ascii in python."
---
{% codeblock lang:pycon %}
>>> a = "He said, “Hi, there.” She didn't reply."

>>> type(a)
<type 'str'>

>>> a
"He said, \xe2\x80\x9cHi, there.\xe2\x80\x9d She didn't reply."

>>> print a
He said, “Hi, there.” She didn't reply.
{% endcodeblock %}

`a` is a string encoded in utf-8.

{% codeblock lang:pycon %}
>>> b = unicode(a)
 Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  UnicodeDecodeError: 'ascii' codec can't decode byte 0xe2 in position 9: ordinal not in range(128)
{% endcodeblock %}

This didn't work because the default encoding in python in ascii. So, python was not able to decode `a` assuming ascii encoding.

{% codeblock lang:pycon %}
>>> b = unicode(a, "utf-8")

>>> type(b)
<type 'unicode'>

>>> b
u"He said, \u201cHi, there.\u201d She didn't reply."

>>> print b
He said, “Hi, there.” She didn't reply.
{% endcodeblock %}

`b` is not a string. It is a unicode object. I think it has no encoding. You can encode it in different encodings.

{% codeblock lang:pycon %}
>>> c = b.encode("utf-8")

>>> type(c)
<type 'str'>

>>> c
"He said, \xe2\x80\x9cHi, there.\xe2\x80\x9d She didn't reply."

>>> print c
He said, “Hi, there.” She didn't reply.
{% endcodeblock %}

`c` is now same as `a`. It is a string encoded in utf-8. We created it by encoding a unicode object.

{% codeblock lang:pycon %}
>>> d = a.encode("utf-8")
 Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  UnicodeDecodeError: 'ascii' codec can't decode byte 0xe2 in position 9: ordinal not in range(128)
{% endcodeblock %}

`a` is already encoded in utf-8. What happens here is that python first tries to decode `a` and then encode `a`. But decoding `a` fails because default encoding is assume to be ascii.

{% codeblock lang:pycon %}
>>> e = a.decode("utf-8")

>>> type(e)
<type 'unicode'>

>>> e
u"He said, \u201cHi, there.\u201d She didn't reply."

>>> print e
He said, “Hi, there.” She didn't reply.
{% endcodeblock %}

Now, `e` is same as `b`. It is a unicode object.

{% codeblock lang:pycon %}
>>> f = a.decode("ascii")
 Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  UnicodeDecodeError: 'ascii' codec can't decode byte 0xe2 in position 9: ordinal not in range(128)
{% endcodeblock %}

Just to show what we have been saying earlier.

{% codeblock lang:pycon %}
>>> g = b.encode("ascii")
 Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  UnicodeEncodeError: 'ascii' codec can't encode character u'\u201c' in position 9: ordinal not in range(128)
{% endcodeblock %}

Note this is an `UnicodeEncodeError` and not a `UnicodeDecodeError`. We can't encode a unicode object which contains characters outside of ascii range to ascii encoding.
