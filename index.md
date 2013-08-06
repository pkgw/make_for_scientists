---
layout: default
title: “Make” for Scientists
ghlink: pkgw/make_for_scientists
analyticsid: UA-26313513-1
gitid: "$Id$"
---

{% comment %} This is kind of silly, but it's nice to have the commit SHA1
accessible for substitution into the HTML. This is the best way I can find to
do the text processing. We have to split the capture into two lines because
otherwise Git sees it as an Id: substitution! {% endcomment %}
{% capture sha1tmp %}{{ page.gitid | remove:'$Id: ' }}{% endcapture %}
{% capture sha1 %}{{ sha1tmp | remove:' $' }}{% endcapture %}

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

> **Note**: this tutorial is a work in progress. It's not yet complete or
> polished.


What is “Make”?
===============

`make` is a venerable Unix command-line tool. It makes things.

Before running `make` you specify a set of *recipes*: “To make *A*, do *X*,
then *Y*, then *Z*.” All `make` does is execute certain of those recipes for
you. The things that `make` makes, such as our metasyntactic *A*, are called
*targets*.

You also tell `make` about *dependencies* between targets: “*A* is derived
from *B*. If *B* changes, *A* needs to be remade.” `make` can keep track of
“freshness,” so that it knows which targets need to be remade and which don't.
When you ask `make` to make a target, it looks at all of the dependencies to
make sure that it's fully up-to-date.

On an abstract level, that's really all there is to it.

The concrete details of `make`'s implementation are also easy to state.
*Targets* are files on your hard drive. *Freshness* is recorded in the
modification times of those files. And the *recipes* are short Unix shell
scripts.

This short summary might not make it clear why `make` can be so useful. We'll
explore its possibilities shortly. But first, a quick example to get you
grounded.


A Basic Example
===============

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

When you run `make`, it prints out the commands that it's running. So the
overall effect would be:

{% highlight bash %}
$ make
./extractor.py info.fits >table.tex
pdflatex paper
$
{% endhighlight %}

(Well, I've cheated in one way: each command that `make` runs prints its
output as normal, so you'd also get a bunch of output from `pdflatex`, as well
as anything that `extractor.py` happens to print out. This can get pretty
annoying, and I recommend using [a wrapper script to drive latex][latexdriver]
that hides its output by default.)

[latexdriver]: https://github.com/pkgw/pwpy/blob/master/scibin/latexdriver

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

If you give `make` the name of one or more targets as command-line arguments,
it will build only them. If you don't give it any arguments, as we've been
showing, it builds the *first* target listed in your `Makefile`. Generally,
then, you want your first target to be your final product.

{% highlight bash %}
$ make paper.pdf
pdflatex paper
$ make paper.tex
make: 'paper.tex' is up to date.
$
{% endhighlight %}


Stepping Back
=============

Our example has been very simple, but hopefully it's starting to convey a
couple of ways in which `make` is a very useful tool.

Firstly, there's `make`'s *raison d’être*: its dependency tracking, which lets
you keep a complicated data product up-to-date without completely recomputing
it from Square One. Even in our simple example, this could be very useful if,
say, the `extractor.py` program takes 10 minutes to run.

Secondly, there's the fact that all of your recipes are codified in your
`Makefile`, and you can access them just by typing `make`. It's not too hard
to type `pdflatex paper` once in a while, but some recipes are a lot more
complicated than that, and it's important to have them stored somewhere other
than your shell history. Even better, `make` can be your Swiss Army knife: a
single place you go to for access to a whole suite of useful tools. If you
have a data analysis process that generates dozens of intermediate products,
`make` can be the single point of access for creating or updating any of them
--- once you've written your `Makefile`, `make <filename>` is the only thing
you need to remember.

A good thing about `make` that may be less obvious is the way that it lets you
build a complicated data pipeline from small pieces that you can reason about.
Writing the `Makefile` stanza for a target is usually not difficult: you
generally know how the recipe should go, and you just need to think about what
its dependencies are. But by just writing a bunch of straightforward targets
in a row, you can construct a sophisticated pipeline with complicated
dependency structure. For instance, the example that we showed above could be
approximated with a shell script decently; in a C shell, you could write
something like

{% highlight tcsh %}
#! /bin/tcsh
# run as "./script.sh extract" or "./script.sh latex",
# depending on how much reprocessing you want to do.

goto $1

extract:
./extractor.py info.fits >table.tex

latex:
pdflatex paper
{% endhighlight %}

But imagine a data processing pipeline that implements a complicated
flowchart. It can't be easily linearized into a shell script, and it's *very*
difficult to write something that will rerun only the recipes that truly need
it.

> You can think of the targets in a `Makefile` as constructing a graph, in the
> mathematical sense. Each target is a node, and has links to other nodes: its
> dependencies. A `Makefile` actually defines a very well-known kind of graph,
> a [Directed Acyclic
> Graph](http://en.wikipedia.org/wiki/Directed_acyclic_graph) or DAG. It's
> *directed* because the links between nodes have a direction: “*A* depends on
> *B*” does not imply “*B* depends on *A*”. It's *acyclic* because you're not
> allowed to have cycles, that is, closed loops. Such a loop is a “circular
> dependency” in `make`, and it should be clear that loops like this are not
> compatible with `make`'s processing model.
>
> There is a well-developed literature regarding DAGs and thinking about
> dependency trees in terms of graph theory can be helpful, if your mind has a
> certain mathematical bent.
>
> Incidentally, another well-respected piece of software,
> [Git](http://git-scm.com/), also has a DAG as its core data structure, even
> though it applies it to solve a vastly different problem.

Another way of putting it is that `make` lets you build pipelines that get
arbitrarily sophisticated, but each step along that path is straightforward
and easy to check for correctness.


Some Details
============

We’ve talked a little bit about the cases where `make` is useful and given a
short example of a `Makefile`, the recipe rule that `make` uses to know what
to do. Now let's delve into some finer-grained details that are important for
getting the most out of `make`.

Tabs, not Spaces
----------------

`make` is an old program, and as such it has some warts that are just really
dumb. The most famous of these is encapsulated in the title of this
subsection. Let's look at another example fragment of a `Makefile`:

{% highlight make %}
mytool: mytool.c mytool.h mytable.h
	gcc -g -O2 -Wall -o mytool mytool.c

mytable.h: maketable.py mytable.dat
	python maketable.py mytable.dat >mytable.h
{% endhighlight %}

We mentioned before that the recipes to make each target are indented. What we
*didn't* mention is that this indent must be made from a single hit of the
“tab” key, and nothing else. Not four spaces, not eight spaces, not spaces and
a tab. This is dumb because the difference between these variations is
essentially invisible, but that’s just how things are.

Most decent text editors are aware of this and make sure that the `Makefile`s
they generate have the right contents. But some programs won’t get this right.
Another thing to look out for is copying and pasting code from terminal
windows or webpages (such as this one!). Even if the original file uses tabs,
the conversion can sometimes change them to spaces and break the `Makefile`.

Variables
---------

You can define variables in `Makefile`s.


<h1 id="credits">Credits</h1>

Contributors include:

* [Peter K. G. Williams](http://newton.cx/~peter/)
* Your name could be here! All you need to do to create your own modified
  version of this tutorial is to [fork it on
  GitHub](https://github.com/{{ page.ghlink }}). The [README][readme] has
  instructions.

[readme]: https://github.com/{{ page.ghlink }}/#readme

This site is hosted on [GitHub Pages](http://pages.github.com/). The site
design is based on the [Midnight](https://github.com/mattgraham/Midnight)
theme created by [Matt Graham](http://madebygraham.com/).

The identifier of this particular version of the tutorial is
[{{ sha1 }}][shalink].

[shalink]: https://github.com/{{ page.ghlink }}/commit/{{ sha1 }}
