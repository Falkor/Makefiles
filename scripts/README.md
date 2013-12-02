-*- mode: markdown; mode: auto-fill; fill-column: 80 -*-
`README.md`

Copyright (c) 2013 [Sebastien Varrette](mailto:<Sebastien.Varrette@uni.lu>) [www](http://varrette.gforge.uni.lu)

        Time-stamp: <Sam 2013-11-16 11:09 svarrette>

-------------------

# NAME

`init_gitrepo` -- Initialize a fresh new (empty) git repo with my favorite
layout and configuration.

## INSTALLATION

Assuming your `PATH` includes `~/bin`, simply make a symbolic link in this folder: 

    cd ~/bin
    ln -s /path/to/repo/Makefile/scripts/init_gitrepo .

## SYNOPSIS

    init_gitrepo [-V | -h]
    init_gitrepo [--debug] [-v] [-n]
    init_gitrepo [--debug] [-v] [-n] --article --name sc13
    init_gitrepo [--debug] [-v] [-n] --slides  --name sc13

## DESCRIPTION

`init_gitrepo` takes care of initiating a freshly cloned Git repository the way
I like i.e.

* initialize git-flow and publishing the associated branch remotely
* takes care of my favorite git submodules
* initialize `README`, `VERSION` file and root `Makefile` to pilot the default
  operations on the repository such as:
  
  * `make setup` so that collaborators simply duplicate my configuration
  * `make start_bump_{major,minor,patch}` and `make release` to bump a new
    version of the repository and release it 
  * (eventually) bootstrap a LaTeX project, typically the sources of a new
    research article / book chapter / beamer slides 

## OPTIONS

    --debug
        Debug mode. Causes init_gitrepo to print debugging messages.
    -h --help
        Display a help screen and quit.
    -n --dry-run
        Simulation mode.
    -v --verbose
        Verbose mode.
    -V --version
        Display the version number then quit.
    -a  --with-article  --article
        Bootstrap an article directory
    -s  --with-slides   --slides
        Bootstrap a beamer slides directory
    -b  --with-bookchapter  --with-chapter
        Bootstrap a boot chapter directory


## EXAMPLES

Bootstrap the repository:
   
    init_gitrepo [--debug]
    
Initiate a new article for the conference SC'13 (i.e. bootstrap a directory `articles/2013/sc13`):
         
    init_gitrepo --article --name sc13

Initiate new Beamer slides for the conference SC'13 (i.e. bootstrap a directory `slides/2013/sc13`):
         
    init_gitrepo --slides --name sc13


## AUTHOR

Sebastien Varrette <Sebastien.Varrette@uni.lu> aka [Falkor](https://github.com/Falkor). 

## REPORTING BUGS

Please report bugs to the Github [here](https://github.com/Falkor/Makefiles/issues)

## COPYRIGHT
     
    This is free software; see the source for copying conditions.  There is
    NO warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR
    PURPOSE.

##SEE ALSO

This script makes an heavy usage of the makefiles I propose on the [Github project]( https://github.com/Falkor/Makefiles/)


