---
layout: default
title: “Make” for Scientists
ghlink: pkgw/make_for_scientists
analyticsid: UA-26313513-1
---

Welcome to the **"Make” for Scientists** tutorial! We're aiming to explain a
little bit about the useful Unix tool `make`: *what* it is, *when* you might
want to use it, and *how* it works. We're not going to be able to teach you
all the details, but hopefully we can put something new into your bag of
computational tricks.

Why do we call this **“Make for Scientists**? Well, `make` is most often used
by computer programmers for compiling software --- so most of the tutorials
that are out there focus on that use case. We'll focus on scientific workflows
instead, mostly drawn from astronomy. A few mathematical topics will be
*briefly* touched on, but none of them are central to understanding what's
going on. If you're not a scientist, there's nothing scary in here; the
examples will just be tilted in a direction that might be less directly useful
to you.


What is “Make”?
===============

`make` is a venerable Unix command-line tool. It makes things.

You give `make` a set of *recipes*: “To make *A*, do *X*, then *Y*, then *Z*.”
Make can execute those recipes for you. Anything that `make` makes, such as
our metasyntactic *A*, is called a *target*.

You also tell `make` about *dependencies* between targets: “*A* is derived
from *B*. If *B* changes, *A* needs to be remade.” `make` can keep track of
“freshness,” so that it knows which targets need to be remade and which don't.

On an abstract level, that's really all there is to it.

The concrete details of `make`'s implementation are also easy to state.
*Targets* are files on your hard drive. *Freshness* is recorded in the
modification times of those files. And the *recipes* are short Unix shell
scripts.

This short summary might not make it clear why `make` can be so useful. We'll
explore its possibilities shortly. But first, a quick example to get you
grounded.


A Short, Unthrilling Example
============================

You tell `make` about your targets, their dependencies, and your recipes by
writing an ASCII text file called `Makefile`. When you run `make` on the
command line, it looks for `Makefile` in your current directory, loads up the
specifications, and then ensures that your targets are up-to-date. That is, if
any of them are insufficiently fresh, it reruns the appropriate recipes. If
they're all fresh enough, nothing happens.

Here's a basic example `Makefile`:

{% highlight make %}
paper.pdf: paper.tex table.tex
	pdflatex paper

table.tex: extractor.py info.fits
	./extractor.py info.fits >table.tex
{% endhighlight %}

Each `Makefile` has a series of stanzas defining targets. The filename of each
target comes before the colon. The targets upon which it depends come after
the colon. Then come indented lines of shell code that, when run, will create
the target. In the above example, there are two targets: `paper.pdf` and
`table.tex`. The first target, `paper.pdf`, depends on `paper.tex` and
`table.tex`, and is created by running `pdflatex`. If `paper.tex` or
`table.tex` change, `pdflatex` needs to be rerun.

The target `table.tex` in turn depends on `extractor.py` and `info.fits`. It
gets created by running this bit of shell code:

{% highlight bash %}
./extractor.py info.fits >table.tex
{% endhighlight %}

Imagine that you start in a directory that contains `Makefile`, `paper.tex`,
`extractor.py`, and `info.fits`. If you run `make`, it will try to ensure that
`paper.pdf` is up-to-date. `paper.pdf` doesn't exist at all, which is (sensibly)
interpreted as being *not* up-to-date. Therefore it needs to be rebuilt.

But before that can be done, we need to consider the dependencies of
`paper.pdf`: `paper.tex` and `table.tex`. There's no entry in the `Makefile`
for `paper.tex`. This is also treated sensibly: as long as the file exists,
it's considered up-to-date. If `paper.tex` did *not* exist, `make` would
signal an error, saying something like

> No rule to make target "paper.tex".

`table.tex`, on the other hand, *is* listed in the `Makefile` with its own
dependencies. These need to be checked before `paper.pdf` can be built. `make`
sees that `table.tex` depends on `extractor.py` and `info.fits`, both of which
exist, and both of which are not listed in the `Makefile`. No sub-dependencies
need to be checked, so `table.tex` can be built. Once this is done, all of the
dependencies of `paper.pdf` have been dealt with, so it too can be built.
Overall,

{% highlight bash %}
$ make
./extractor.py info.fits >table.tex
pdflatex paper
$
{% endhighlight %}

Imagine that we write up some amazing results in `paper.tex` and rerun `make`.
What happens? The same evaluation from before is repeated. This time, however,
`table.tex` exists, and none of its dependencies have been updated: the
modification time of the file `table.tex` is newer than the times of
`extractor.py` and `info.fits`. So we don't need to regenerate it. However,
`paper.pdf` is a different story. It exists, and its dependencies exist, but
it's out-of-date: `paper.tex` has a more recent modification time. Therefore,
the `pdflatex` command *does* need to be rerun:

{% highlight bash %}
$ make
pdflatex paper
$
{% endhighlight %}


<h1 id="credits">Credits</h1>

Contributors include:

* [Peter K. G. Williams](http://newton.cx/~peter/)
* Your name could be here! All you need to do to create your own modified
  version of this tutorial is to [fork it on 
  GitHub](https://github.com/{{ page.ghlink }}).

This site is hosted on [GitHub Pages](http://pages.github.com/). The site
design is based on the [Midnight](https://github.com/mattgraham/Midnight)
theme created by [Matt Graham](http://madebygraham.com/).
