---
layout: default
---

## Introduction
If you have a LaTeX document which

* is large (probably several hundreds of pages at least)
* is regularly updated
* needs to be externally referenced often

you will run into the problem that

* large PDFs are not easily navigable
* PDFs of any size are not very searchable
* the internal numbering changes often, making external references outdated

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
The main example are

* [The Stacks project](https://stacks.math.columbia.edu): _an open source textbook and reference work on algebraic geometry_, by Johan de Jong (Gerby was originally developed for this purpose)
* [Kerodon](https://kerodon.net): _an online resource for homotopy-coherent mathematics_, by Jacob Lurie.

If you are interested in using the system for your own project and have questions, please get in touch!


## How to use it
The process consists of 3 parts, which we will explain in detail below.

1. run `plastex` on your LaTeX file and tags file
2. run the import script on the output from step 2
3. start the server

The result of each step is

1. a large number of small HTML and auxiliary files
2. an SQLite3 database containing the output of step 1, together with all the metadata
3. a local web server that serves your Gerby instance

We are in the process of making the code more generic, please get in touch if you are interested in running your own instance.


## Hello world
In [https://github.com/gerby-project/hello-world](`gerby-project/hello-world`) we have collected a _minimal working example_ of what it means to use Gerby and plasTeX for your own project.

The main files here are:

* [https://github.com/gerby-project/hello-world/blob/master/document.tex](`document.tex`) contains a minimal LaTeX document, with labels
* [https://github.com/gerby-project/hello-world/blob/master/tagger.py](`tagger.py`) is an example script to automate the tag assignment
* [https://github.com/gerby-project/hello-world/blob/master/configuration.py](`configuration.py`) is the Gerby configuration file for our example project

and most importantly [https://github.com/gerby-project/hello-world/blob/master/.travis.yml](`.travis.yml`) contains all the steps that are needed to setup the system. This script is run automatically on a virtual server, so all steps are guaranteed to work (provided your configuration approximates that of the virtual server).

Some remarks:

* the file `document.tex` cannot be built by `pdflatex`: we have to remove the `amsthm` package
* similarly other packages might break plasTeX, so you should keep your packages as vanilla as possible, and work with custom preambles

The file [https://github.com/gerby-project/hello-world/blob/master/.travis.yml](`.travis.yml`) should be self-explanatory, as it lists the shell commands one needs to execute to set up everything. The subtle steps are probably step 4 and 5 of the installation. One way of avoiding this is by using full paths in the configuration file.


If you are more ambitious, you can also set up the Stacks project, [/stacks-instructions](following some more elaborate instructions).


## Contributors

* [Pieter Belmans](http://pbelmans.ncag.info)
* [Raymond Cheng](http://chngr.github.io/)
* [Johan de Jong](http://www.math.columbia.edu/~dejong/)
