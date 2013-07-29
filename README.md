Make for Scientists
===================

This repository has the source code for the "Make for Scientists" tutorial.
The actual site is hosted on [Github Pages][livesite]; you only want to
look at this repository if you're interested in making your own, improved
version of the site.

[livesite]: http://pkgw.github.io/make_for_scientists/

Improving the Site
------------------

The website is generated with the [Jekyll] templating system, which has the
advantage of being natively supported by GitHub Pages. That means that with
the right setup, you can just push an updated file to your GitHub repository
and the fancy-formatted live site will update automatically. Snazzy!

[Jekyll]: http://jekyllrb.com/
[Markdown]: https://help.github.com/articles/github-flavored-markdown

Jekyll is super easy to use by itself. You do need to [install
it][installjekyll] first, of course. Then in your repository directory, just
launch:

```shell
jekyll serve --watch
```

and you'll be able to view the built site at http://localhost:4000/.

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

[orig]: https://github.com/pkgw/make_for_scientists/

To share improvements, please submit a [pull request][prhelp] to [the original
repository][orig]. Or you can start discussing ideas for improvements using
its [issue tracker][origissues].

[prhelp]: https://help.github.com/articles/using-pull-requests
[origissues]: https://github.com/pkgw/make_for_scientists/issues

Branching Out
-------------

Feel free to completely replace the `index.md` file and use this code as a
template to create your own instant, pretty-looking, collaboratively-editable
website! The HTML and CSS template code comes from the [Midnight
theme][midnight] created by [Matt Graham][mattgraham]. Please make sure that
he gets credit somewhere in anything you put together.

[midnight]: https://github.com/mattgraham/Midnight
[mattgraham]: http://madebygraham.com/
