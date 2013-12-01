---
title: Tools
author: Alex Beal
date: 9-22-2012
type: page
permalink: tools
---

> **Laziness** – The quality that makes you go to great effort to reduce overall energy expenditure. It makes you write labor-saving programs that other people will find useful, and document what you wrote so you don't have to answer so many questions about it. Hence, the first great virtue of a programmer. Also hence, this book. See also impatience and hubris.
>
> -- *Programming Perl* by Larry Wall

I'm an efficiency nut. I like to spend a month writing a tool, so that I can save a lifetime of work. Below are some of my favorites. If you like what you see, you're only a `git clone` away from dev environment heaven: [dotfiles repo](https://github.com/beala/dotfiles).

### Tools I've Written
- [LaMark](https://github.com/beala/lamark): A meta-markup language that compiles down to Markdown. Its main purpose is for embedding LaTeX in Markdown documents. Used to generate posts for this blog.
- [PaleoBlogger](https://github.com/beala/paleoblogger): A static blog generator with a focus on simplicity and readability. Used to generated this blog!
- [xkpa](https://github.com/beala/xkcd-password): An [xkcd style password](http://xkcd.com/936/) generator with lots of options.
- [irm](https://github.com/beala/irm): A wrapper for `rm` written in `bash`. Moves files to a trashcan rather than deleting them.
- [ButterBird](https://gist.github.com/3471267): A tiny Python script for queuing tweets.

###Vim Plugins###
- [Pathogen](https://github.com/tpope/vim-pathogen): Easy management of Vim plugins.
- [Command-T](https://wincent.com/products/command-t): Fuzzy search your directory tree from within Vim. Much better than trying to navigate a source directory with something like NERDTree.
- [Powerline](https://github.com/Lokaltog/vim-powerline): Pimp out your Vim status line with file type info, color coded modes, git status integration, and more.
- [NERDCommenter](https://github.com/scrooloose/nerdcommenter): As the Github page explains, "A Vim plugin for intensely orgasmic commenting." I don't really have anything to add to that.
- [Tagbar](http://majutsushi.github.com/tagbar/): ctags integration. View function signatures, class outlines, etc in Vim.
- [EasyMotion](https://github.com/vim-scripts/EasyMotion): Quick navigation of files.

###Python###
- [nose](http://nose.readthedocs.org/en/latest/): Improved unit testing in Python. Easy test discovery and test running, along with a host of other plugins.
- [virtualenvwrapper](http://www.doughellmann.com/projects/virtualenvwrapper/): Allows you to install Python packages in isolated environments for testing and development.
- [setuptools](http://pypi.python.org/pypi/setuptools): setuptools is a bit old and seems to be falling out of favor, but its [development mode](http://peak.telecommunity.com/DevCenter/setuptools#development-mode) is completely indispensable, especially in conjunction with virtualenvwrapper.
- [pudb](http://pypi.python.org/pypi/pudb): A beautiful text-based graphical replacement for Python's `pdb` debugger.
- [pyflakes](http://pypi.python.org/pypi/pyflakes): A static analyzer for basic logic errors such as importing but never using a module, or dereferencing an undefined variable.
- [pylint](http://www.logilab.org/project/pylint): Another static analyzer that checks your code for style errors, using [PEP-8](http://www.python.org/dev/peps/pep-0008/) as the Platonic form of the good Python program. It's very verbose, and will probably destroy your ego the first time you run it.
- [IPython](http://ipython.org/): A replacement for the standard Python shell, with lots of niceties like syntax highlighting and tab completion.