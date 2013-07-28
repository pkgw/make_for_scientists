---
layout: default
title:  Make for Scientists
tagline: A work in progress!
---

Welcome to the "Make for Scientists" tutorial! We're aiming to explain a
little bit about the useful Unix tool `make`: *what* it is, *when* you might
want to use it, and *how* it works. We're not going to try to teach you
everything, but hopefully we can add another arrow to your computational
quiver.

{% highlight make %}
foo: foo.c
    gcc -o $@ $^
{% endhighlight %}

This isn't the place for advanced techniques or detailed reference
information. For those, try [StackExchange] or our old friend [Google].

[StackExchange]: http://stackexchange.com/
[Google]: https://google.com/
