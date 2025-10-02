![By Falkor](https://img.shields.io/badge/by-Falkor-blue.svg) [![github](https://img.shields.io/badge/git-github-lightgray.svg)](https://github.com/Falkor/makefiles) [![Falkor/makefiles issues](https://img.shields.io/github/issues/Falkor/makefiles.svg)](https://github.com/Falkor/Makefiles/issues)

        _____     _ _              _       __ __  __    ______ __       _         __ _ _
       |  ___|_ _| | | _____  _ __( )___  | _|  \/  |  / /  _ \_ | __ _| | _____ / _(_) | ___  ___
       | |_ / _` | | |/ / _ \| '__|// __| | || |\/| | / /| |_) | |/ _` | |/ / _ \ |_| | |/ _ \/ __|
       |  _| (_| | |   < (_) | |    \__ \ | || |  | |/ / |  _ <| | (_| |   <  __/  _| | |  __/\__ \
       |_|  \__,_|_|_|\_\___/|_|    |___/ | ||_|  |_/_/  |_| \_\ |\__,_|_|\_\___|_| |_|_|\___||___/
                                          |__|                |__|
       .              Copyright (c) 2012-2025 Sebastien Varrette

# Sebastien Varrette aka Falkor's `Makefiles`

This repository host a set of `Makefile` (configuration file for [GNU make](http://www.gnu.org/software/make/)) I used for various projects over time (some dates back from very old time).

__Warning:__ Use these `Makefiles` at your own risk!

They often integration a native way to be extended if files such as `.Makefile.local` or `.Makefile.custom` are present (in which case they are included).

The sub-directory name gives you an hint on the usage context.

| __Example__                                              | __Description__                                                                                                                                        |
|----------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| [`ansible/Makefile`](ansible/Makefile)                   | pilot Ansible control repository                                                                                                                       |
| [`drawio/Makefile`](drawio/Makefile)                     | pilot [draw.io](https://www.drawio.com/) diagram: generate PDF from `*.drawio`, export PNG from PDF                                                    |
| [`generic/Makefile.insubdir`](generic/Makefile.insubdir) | generic `Makefile` meant to call super directory (`../`) Makefile                                                                                      |
| [`gnuplot/Makefile`](gnuplot/Makefile)                   | process [Gnuplot](https://gnuplot.readthedocs.io/en/readthedocs/) sources  and data                                                                    |
| [`images/Makefile`](images/Makefile)                     | optimize images sizes (jpeg, png, pdf) and process [`xfig`](https://xfig.org/) and svg  files                                                          |
| [`ISOs/Makefile`](ISOs/Makefile)                         | automatically download and check installations ISOs for major Linux distributions                                                                      |
| [`latex/Makefile`](latex/Makefile)                       | process LaTeX projects, eventually coupled with markdown sources (converted with [`pandoc`](https://pandoc.org/))                                      |
| [`latex_src/Makefile`](latex_src/Makefile)               | handle LaTeX projects top directory (where LaTeX/markdown sources are hosted under `src/`): release PDF, cover page etc.                               |
| [`markdown/Makefile.to_html`](markdown/Makefile.to_html) | convert markdown to html with  [`pandoc`](https://pandoc.org/)                                                                                         |
| [`mermaid/Makefile`](mermaid/Makefile)                   | convert [Mermaid](https://mermaid.js.org/) diagramming sources `*.mmd` to (transparent) PDF, export PNG from PDF                                       |
| [`puppet/Rakefile`](repo/Rakefile)                       | **Rakefile** (not `Makefile`) piloting a Puppet control repository                                                                                     |
| [`repo/Makefile`](repo/Makefile)                         | pilot git repository action (assumed bootstrapped to follow my favorite [git-flow](https://nvie.com/posts/a-successful-git-branching-model/) workflow) |
| [`servers/Makefile`](servers/Makefile)                   | grab some configuration files from remote servers                                                                                                      |
| [`venv/Makefile.venv`](venv/Makefile.venv)               | pilot python virtual environment for your projet                                                                                                       |


## Issues / Feature request

You can submit bug / issues / feature request using the [`Falkor/Makefiles` Project Tracker](https://github.com/Falkor/Makefiles/issues)
