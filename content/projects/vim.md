---
title: "Vim"
date: 2017-10-07T22:31:45-04:00
draft: false
---

Back to coding in C/C++ this year for an Advanced Operating System course. I've
been using vim the last year for the intro. Implementing a few utilities for
EXT2 file system required reading through a number of structs and jump around
quite a bit. Took me 5-6 hours to succumb and use IDE, namely Clion.

Multiple problem were encountered with basic vim setup:

* lack of auto-completion
* go-to-definition
* Poor organization and way too many plugins


All of that could be solved with YouCompleteMe.  Looking over vim 8.0 changelogs
got me hyped, - native plugin management!! Unfortunately our school computers
only have 7.4 version of vim available. Welp,
too bad - gonna compile from source.

```bash
$ git clone https://github.com/vim/vim.git
$ ./configure --with-features=huge \
--enable-multibyte \
--enable-rubyinterp=yes \
--enable-pythoninterp=yes \
--with-python-config-dir=/usr/lib/python2.7/config \
--enable-python3interp=yes \
--with-python3-config-dir=/usr/lib/python3.5/config \
--enable-perlinterp=yes \
--enable-luainterp=yes \
--enable-gui=gtk2
--enable-cscope \
--prefix=$HOME/userland
```

Gives me bunch undefined references to what looks like python libs. Welp, gonna
get my own python with blackjack and beautiful women.

```bash
$ wget https://www.python.org/ftp/python/2.7.14/Python-2.7.14.tgz
$ tar xvf Python-2.7.14.tgz
$ cd Python-2.7.14
$ make --enable-shared prefix=$HOME/userland
$ make install
```

And repeat pretty much the same for python 3.5. Now we are ready to get our
vim. Let's change our python directories when building. Dynamically linkable
version is required for YCM, --enable-shared.

```bash
$ git clone https://github.com/vim/vim.git
$ ./configure --with-features=huge \
--enable-multibyte \
--enable-rubyinterp=yes \
--enable-pythoninterp=yes \
--with-python-config-dir=$HOME/userland/lib/python2.7/config \
--enable-python3interp=yes \
--with-python3-config-dir=$HOME/userland/lib/python3.5/config \
--enable-perlinterp=yes \
--enable-luainterp=yes \
--enable-gui=gtk2 \
--enable-cscope \
--prefix=$HOME/userland
```

There is a good [article](https://shapeshed.com/vim-packages/) detailing a
combination of native plugin management and git. You can follow that. There are
also bash scripts that might be better solution for people that don't like git.


Now we are ready for YouCompleteMe
[plugin](https://github.com/Valloric/YouCompleteMe). Welp clang that is
available on cdf is too old to be used with YCM. I'll finish instruction on
clang in next week.
