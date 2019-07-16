---
layout: default
permalink: stacks-instructions
---

The following are the (somewhat) detailed instructions to running your local copy of the Stacks project.

## plasTeX
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

## Importing tags
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

## Running the website
Having done all this, running the website should be easy. This is assuming you have installed all the dependencies. You might have to install quite a few Python packages, but they can all be easily `pip install`'d. Perform the following steps in `gerby-website/gerby`

1. `export FLASK_APP=gerby`
2. `flask run`

Now you have a website which you can access via `localhost:5000`.

