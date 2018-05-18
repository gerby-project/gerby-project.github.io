---
layout: default
---

# Introduction
If you have a LaTeX document which

* is large (several hundreds of pages at least)
* is regularly updated
* needs to be referenced often

you will run into the problem that

a) large PDFs are not easily navigable
b) it is not very searchable
c) the internal numbering changes often

Gerby tries to give a solution, by providing a **online tag-based view**.

## Tags
A *tag* is a short identifier (usually four symbols long), that is permanently associated to a label in your LaTeX document. If the number associated to the label changes, because you move it or because the text before the label changes, the tag remains the same.

Anything in your document which has a number (sections, theorems, equations, ...) needs to have an associated label, and then in a separate file you set up a dictionary between labels and tags.

## Online view
When everything has been assigned a tag, Gerby will convert your LaTeX document into small HTML files, one for each tag. We then import these into the database, used by a (Flask)[https://flask.pocoo.org] website. This way you get

* a page for every tag
* easy navigation around your document
* a fulltext search


# Showcase
The main example is the (Stacks project)[https://stacks.math.columbia.edu], and Gerby was originally developed for this purpose.

We are currently working on (Kerodon)[https://kerodon.net], which will be a collection of works by Jacob Lurie.

If you are interested in using the system for your own project and have questions, please get in touch.

# Usage
We need to explain how to use Gerby here.
