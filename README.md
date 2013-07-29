Make for Scientists — The Source Code
=====================================

This repository has the source code for the "Make for Scientists" tutorial.
**The [actual site is here][livesite]**, hosted on [GitHub Pages][ghpages];
you only need to worry about this repository if you're interested in making
your own, improved version of the site.

[livesite]: http://pkgw.github.io/make_for_scientists/
[ghpages]: http://pages.github.com/

Improving the Site
------------------

The website is generated from [Markdown] files with the [Jekyll] templating
system, which have the advantage of being natively supported by GitHub Pages.
With the right setup, you can just push an updated file to your GitHub
repository and the fancy-formatted live site will update automatically.
Snazzy!

[Markdown] is the text format used in the main tutorial file, `index.md`.
There's a [reference guide][mdref] and [special GitHub extensions][gfm]; I
*assume* that [GitHub Pages][ghpages] uses the latter, but I should check!

[Jekyll] is the tool used to compile the files into a usable, live website. As
mentioned above, it's built into [GitHub Pages][ghpages] so we don't even need
to worry about that part. And it's super easy to use on your local computer to
preview the site before you publish something. You do need to [install
it][installjekyll] first, of course. Then in your repository directory, just
launch:

```shell
jekyll serve --watch
```

and you'll be able to view the built site at <http://localhost:4000/>.

[jekyll]: http://jekyllrb.com/
[markdown]: https://help.github.com/articles/github-flavored-markdown
[mdref]: http://daringfireball.net/projects/markdown/syntax
[gfm]: https://help.github.com/articles/github-flavored-markdown
[installjekyll]: http://jekyllrb.com/docs/installation/

Publishing your Own Version and Collaborating
---------------------------------------------

The Github infrastructure makes it really easy to publish your own, customized
version of the tutorial, and to collaborate to share improvements.

First you'll want to clone [the original repository][orig]. Because the main
branch has the magic name `gh-pages`, GitHub will automagically publish the
website at `http://yourgithubusername.github.io/make_for_scientists/`. That's
all there is to it! Make a change, commit, push, and see it (almost) instantly
show up in the live site.

To share improvements, please submit a [pull request][prhelp] to [the original
repository][orig]. Or you can start discussing ideas for improvements using
its [issue tracker][origissues].

[orig]: https://github.com/pkgw/make_for_scientists/
[prhelp]: https://help.github.com/articles/using-pull-requests
[origissues]: https://github.com/pkgw/make_for_scientists/issues

Branching Out
-------------

You're absolutely encouraged to completely replace the `index.md` file and use
this code as a template to create your own instant, pretty-looking,
collaboratively-editable website! The [GitHub Pages][ghpages] service really
takes care of all of the tough parts.

The HTML and CSS template code comes from the [Midnight theme][midnight]
created by [Matt Graham][mattgraham]. Please make sure that he gets credit
somewhere in anything you put together.

[midnight]: https://github.com/mattgraham/Midnight
[mattgraham]: http://madebygraham.com/
