---
layout: default
---

# Gerby

## Introduction
If you have a LaTeX document which

* is large (probably several hundreds of pages at least)
* is regularly updated
* needs to be externally referenced often

you will run into the problem that

1. large PDFs are not easily navigable
2. PDFs of any size are not very searchable
3. the internal numbering changes often, making external references outdated

Gerby addresses these problems by providing an **online tag-based view**, instead of just having a big PDF. Gerby is tailored towards making large online textbooks and reference works more accessible.

In case you were wondering, a [*gerbe*](https://en.wikipedia.org/wiki/Gerbe) is a kind of [stack](https://en.wikipedia.org/wiki/Stack_(mathematics)) (in the mathematical sense), and the software was originally meant for the [Stacks project](https://stacks.math.columbia.edu).


### Tags
A *tag* is a short identifier (usually four symbols long, e.g. `02DX`), that is permanently associated to a label in your LaTeX document. If the number associated to the label changes, because you move it or because the text before the label changes, the tag remains the same.

Anything in your document which has a number (sections, theorems, equations, ...) needs to have a label associated to it in your LaTeX code, and then in a separate file (the tags file) you set up a dictionary between labels and tags. This file looks like

```
0000,first-definition
0001,first-lemma
0002,second-lemma
0003,second-definition
0004,second-lemma
0005,main-theorem
```

albeit it much longer probably.

At this point you only have a LaTeX document, and a tags file. These are all the ingredients you need for the next step.

### Online view
When everything has been assigned a tag, Gerby will convert your LaTeX document into small HTML files, one for each tag. We then import these into the database, which in turn is used by a [Flask](http://flask.pocoo.org) website. This way you get

* a page for every tag
* easy navigation around your document
* a fulltext search

Additionally

* the bibliography is taken care of
* there are statistics pages for each tag
* it is possible to comment on each tag, so that readers can interact when there are questions, typos or mistakes


## Showcase
The main example is the [Stacks project](https://stacks.math.columbia.edu), and Gerby was originally developed for this purpose.

We are currently working on [Kerodon](https://kerodon.net), which will be a collection of works by Jacob Lurie.

If you are interested in using the system for your own project and have questions, please get in touch.


## How to use it
The process consists of 3 parts, which we will explain in detail below.

1. run `plastex` on your LaTeX file and tags file
2. run the import script on the output from step 2
3. start the server

The result of each step is

1. a large number of small HTML and auxiliary files
2. an SQLite3 database containing the output of step 1, together with all the metadata
3. a local web server that serves your Gerby instance

The following detailed instructions allow you to run a local copy of the Stacks project website. Currently the website code is tailored towards this, but one can modify the Jinja2 templates for your own project. The instructions are based on the [continuous integration code for Gerby](https://github.com/gerby-project/gerby-website/blob/master/.travis.yml), and a possibly more up-to-date version of the instructions can be extracted from that Makefile.

We are in the process of making the code more generic, please get in touch if you are interested in running your own instance.

### plasTeX
First you need to install a modified version of [plasTeX](https://github.com/tiarno/plastex). We assume that you are using Python 3 by default, change accordingly if that is not the case. Perform the following in the location where you want to install plasTeX:

1. `git clone https://github.com/gerby-project/plastex.git`
2. `cd plastex`
3. `git checkout gerby`
4. `pip install .`

The following is only required if you have a complicated bibliography containing mathematical expressions in the titles, and can be safely skipped. Perform the following steps where you want to install [`pybtex`](https://bitbucket.org/pybtex-devs/pybtex).

1. `git clone https://github.com/live-clones/pybtex.git`
2. `wget https://bitbucket.org/pybtex-devs/pybtex/issues/attachments/110/pybtex-devs/pybtex/1514284299.07/110/no-protected-in-math-mode.patch`
3. `cd pybtex`
4. `git apply ../no-protected-in-math-mode.patch`
5. `pip install .`

Now we can run plasTeX on the Stacks project's LaTeX code. Perform the following steps where you want to place the Stacks project.

1. `git clone https://github.com/stacks/stacks-project.git`
2. `mkdir WEB`
3. `cd stacks-project`
4. `echo yes | python2 scripts/add_tags.py`
5. `make web`
6. `cd ../WEB`
7. `plastex --renderer=Gerby book.tex`

If all went well, you now have a directory called `WEB/book/` containing several tens of thousands small HTML files, and files named `book.paux` and `tags` inside `WEB/`.

### Importing tags
The next step is to install Gerby, and import the result from the previous step into the database.

1. `git clone https://github.com/gerby-project/gerby-website.git`
2. `cd gerby-website/gerby/static`
3. `git clone https://github.com/aexmachina/jquery-bonsai`
4. `cp jquery-bonsai/jquery.bonsai.css css/`
5. `cd ../../`
6. `pip install .`

The following is only necessary if you use `xypic`, as is the case for the Stacks project.

1. `cd gerby-website/gerby/static`
2. `git clone https://github.com/sonoisa/XyJax.git`
3. `sed -i -e 's@\[MathJax\]@/static/XyJax@' XyJax/extensions/TeX/xypic.js`

We can now import things into the database. The Python script for this is found in `gerby-website/gerby/tools/update.py`, and the configuration in `gerby-website/gerby/configuration.py` needs to make the files `tags` and `book.paux`, and the `book/` directory known to this script. With the default configuration, this is done by creating the following softlinks from within `gerby-website/gerby/tools`:

1. `ln -s ../../../WEB/book stacks`
2. `ln -s ../../../WEB/book.paux stacks.paux`
3. `ln -s ../../../WEB/tags stacks.tags`

Assuming Gerby is now correctly configured to read the output of plasTeX, it suffices to run the following.

1. `python update.py`

### Running the website
Having done all this, running the website should be easy. This is assuming you have installed all the dependencies. You might have to install quite a few Python packages, but they can all be easily `pip install`'d. Perform the following steps in `gerby-website/gerby`

1. `export FLASK_APP=gerby`
2. `flask run`

Now you have a website which you can access via `localhost:5000`.

## Contributors

* [Pieter Belmans](http://pbelmans.ncag.info)
* [Raymond Cheng](http://chngr.github.io/)
* [Johan de Jong](http://www.math.columbia.edu/~dejong/)
