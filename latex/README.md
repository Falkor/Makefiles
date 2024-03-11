-*- mode: markdown; mode: auto-fill; fill-column: 80 -*-
`README.md`

Copyright (c) 2012 [Sebastien Varrette](mailto:<Sebastien.Varrette@uni.lu>) [www](http://varrette.gforge.uni.lu)

        Time-stamp: <Wed 2012-10-03 00:03 svarrette>

-------------------

# LaTeX project 

This section provides a generic `Makefile` to compile LaTeX projects. 

Note that you should better rely on [`latexmk`](https://mg.readthedocs.io/latexmk.html), a  [Perl](https://www.perl.org/) script which you just have to run once and it does everything else for you … completely automagically. One drawback is that this assumes that you have installed the corresponding package. 

The present `Makefile` does **NOT** make this assumption and thus should be self-sufficient. 

## Installation / Usage 

* Either copy the present `Makefile` directly in your project. 
* Or: create a git submodule targetting this repository and symlink this Makefile to the appropriate directory
  * The advantage is that you may inherit 'automatically' of any further commit and changes made in this repository 

## Customization

As for all my Makefiles, this makefile supports the inclusion of special files (if present) `.Makefile.{before,local,after}` or `.Makefile.custom`

Use these files to specialize some variables or introduce new targets.
