####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
# Time-stamp: <Sat 2024-02-24 19:57 svarrette>
#     __  __       _         __ _ _            __   _         _____   __  __
#    |  \/  | __ _| | _____ / _(_) | ___      / /  | |    __ |_   _|__\ \/ /
#    | |\/| |/ _` | |/ / _ \ |_| | |/ _ \    / /   | |   / _` || |/ _ \\  /
#    | |  | | (_| |   <  __/  _| | |  __/   / /    | |__| (_| || |  __//  \
#    |_|  |_|\__,_|_|\_\___|_| |_|_|\___|  /_/     |_____\__,_||_|\___/_/\_\
#
# Copyright (c) 2004-2015 Sebastien Varrette <Sebastien.Varrette@uni.lu>
# .             http://varrette.gforge.uni.lu
#
####################################################################################
# Compilation of files written in LaTeX.
# If you can, consider [Latexmk](http://www.ctan.org/pkg/latexmk/) as a far more
# complete solution for the compilation of your LaTeX documents.
# This Makefile appeared sufficient for all my workflows.
#
# Grab the lastest version of this Makefile from:
#            https://github.com/Falkor/Makefiles/tree/devel/latex
# Or consider using git submodulers toward the reference repository (see below)
#
# --------------------------------------------------------------------------------
# This is a generic makefile in the sense that it doesn't require to be
# modified when adding/removing new source files.
# --------------------------------------------------------------------------------
#
# This makefile search for LaTeX sources from the current directory, identifies
# the main files (i.e the one containing the sequence '\begin{document}') and
# launch the compilation for the generation of PDFs using the pdflatex compiler
# (or others such as lualatex for instance) specified by the LATEX variable.
#
# Upon compilation:
# * all the intermediate files (main.aux, main.log etc.) will be moved
#   to $(TRASH_DIR)/ (if it exists).
# * the gererated files (pdf etc.) will stay in the current directory.
#
# Available Commands : see `make help`
# Customization: typically create a .Makefile.local file
#
# How do I use this file in my project? Typically via git submodules.
# Here are the bootstrap commands I use (automized in the Falkorlib):
#   git submodule add -b devel https://github.com/Falkor/Makefiles.git .submodules/Makefiles
#   cd subpath/to/LaTeX/project
#   mkdir src    # I generally prefer to separate LaTeX sources into src/
#   cd src
#   ln -s $(git rev-parse --show-cdup) .root       # symlink to repository root
#   ln -s .root/.submodules/Makefiles/ .makefile.d # convenient symlink to Makefile repo
#   ln -s .makefile.d/latex/Makefile Makefile      # main Makefile for LaTeX projects
#

############################## Variables Declarations ##############################
SHELL = /bin/bash

# set to 'yes' to use pdflatex for the direct generation of pdf from LaTeX sources
# set to 'no' to use the classical scheme tex -> dvi -> [ps|pdf] by dvips
#USE_PDFLATEX = yes

# Directory where PDF, Postcript files and other generated files will be placed
# /!\ Please ensure there is no trailing space after the values
OUTPUT_DIR  = .
TRASH_DIR   = .Trash
HTML_DIR    = $(OUTPUT_DIR)/HTML
SUPER_DIR   = $(shell basename `pwd`)

# Check availibility of source files
TEX_SRC      = $(wildcard *.tex)

MARKDOWN_SRC = $(filter-out README.md, $(wildcard *.md))
# Add to MARKDOWN_SRC the eventual markdown files added via \input{}
MARKDOWN_SRC += $(shell grep '^\s*\\input{.*\.md}' *.tex | sed -e 's/.*\\input{\(.*\)}.*/\1/' -e 's/}//')

MARKDOWN_DST = $(MARKDOWN_SRC:%.md=%.md.tex)
STYLE_SRC    = $(wildcard *.sty)
BIB_SRC      = $(wildcard *.bib)
ifeq ($(TEX_SRC),)
$(error "No source files available - compilation is not possible -- Kindly check LaTeX source files")
endif

# Main tex file and figures it may depend on
MAIN_TEX    = $(shell grep -l "[\]begin{document}" $(TEX_SRC) | xargs echo)
USE_BEAMER  = $(shell grep -l "{beamer}" $(MAIN_TEX) | xargs echo)
#FIGURES    = $(shell find . -name "*.eps" -o -name "*.fig" | xargs echo)
#IMAGES_DIR = images

# Various commands used during compilation
LATEX        = $(shell which pdflatex)
CMDLINE_OPTS = -synctex=1 -halt-on-error -interaction=batchmode -shell-escape
HTLATEX      = $(shell which htlatex)
LATEX2RTF    = $(shell which latex2rtf)
PANDOC       = $(shell which pandoc)
BIBTEX       = $(shell which bibtex)

# Generated files
PDF          = $(MAIN_TEX:%.tex=%.pdf)
RTF          = $(MAIN_TEX:%.tex=%.rtf)
TARGET_PDF   = $(PDF)
ifneq ($(OUTPUT_DIR),.)
TARGET_PDF   = $(PDF:%=$(OUTPUT_DIR)/%)
endif
BACKUP_FILES = $(shell find . -name "*~")
# Files to move to $(TRASH_DIR) after compilation
# Never add *.tex (or any reference to source files) for this variable.
TO_MOVE      = *.aux *.log *.toc *.lof *.lot *.bbl *.blg  *.maf *.mtc* *.out *.nav *snm *.vrb *.rel *.thm *.bcf *-blx.bib *.run.xml *.idx *.lol *.ptc *.cb* *.tmp

# Ghostscript for the help of producing optimized PDF
GS      = $(shell which gs)
GS_OPTS = -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/prepress

##################### Main targets #####################
# Default targets - append your own with TARGETS += <xXx> or TARGETS_DEPS += <images/...>
BEFORE_TARGETS =
TARGETS = $(BEFORE_TARGETS) $(TARGET_PDF)
TARGETS_DEPS = $(TEX_SRC) $(MARKDOWN_SRC) $(STYLE_SRC) $(BIB_SRC) $(FIGURES)

# Default targets for 'make [dist]clean' - append your own with [DIST]CLEAN_TARGETS += [dist]clean-<xXx>
CLEAN_TARGETS = clean-latex
DISTCLEAN_TARGETS =

# Local configuration - Kept for compatibity reason
LOCAL_MAKEFILE = .Makefile.local

# Makefile custom hooks
MAKEFILE_BEFORE = .Makefile.before
MAKEFILE_AFTER  = .Makefile.after

.PHONY: all archive test versioninfo

#clean create_output_dir dvi force help html move_to_trash pdflatex ps release rtf setup singlerun start_bump_major start_bump_minor start_bump_patch test versioninfo

############################### Now starting rules ################################
# Load local settings, if existing (to override variable eventually)
ifneq (,$(wildcard $(LOCAL_MAKEFILE)))
include $(LOCAL_MAKEFILE)
endif
ifneq (,$(wildcard $(MAKEFILE_BEFORE)))
include $(MAKEFILE_BEFORE)
endif

ifeq ($(LATEX),)
$(error "Cannot find the LaTeX compiler '$(LATEX)' in your PATH")
endif

ifeq ($(MAIN_TEX),)
$(error "Cannot find any .tex file with a '\begin{document}' directive among '$(TEX_SRC)'. Please define a main tex file!")
endif

# Markdown files processing
ifneq ($(MARKDOWN_SRC),)
ifeq ($(PANDOC),)
$(error "pandoc cannot be found on your system. Kindly install it to convert markdown to latex")
endif
BEFORE_TARGETS += $(MARKDOWN_DST)
endif


# Required rule : what's to be done each time
all: $(TARGETS)

# Test values of variables - for debug purposes
info:
	@echo "--- Directories --- "
	@echo "OUTPUT_DIR   -> '$(OUTPUT_DIR)'"
	@echo "TRASH_DIR    -> '$(TRASH_DIR)'"
	@echo "HTML_DIR     -> '$(HTML_DIR)'"
	@echo "SUPER_DIR    -> '$(SUPER_DIR)'"
	@echo
	@echo "--- Compilation commands --- "
	@echo "LATEX        -> '$(LATEX)'"
	@echo "CMDLINE_OPTS -> '$(CMDLINE_OPTS)'"
	@echo "HTLATEX      -> '$(HTLATEX)'"
	@echo "LATEX2RTF    -> '$(LATEX2RTF)'"
	@echo "PANDOC       -> '$(PANDOC)'"
	@echo "BIBTEX       -> '$(BIBTEX)'"
	@echo "GS           -> '$(GS)'"
	@echo "GS_OPTS      -> '$(GS_OPTS)'"
	@echo
	@echo "--- Files --- "
	@echo "TEX_SRC      -> '$(TEX_SRC)'"
	@echo "MARKDOWN_SRC -> '$(MARKDOWN_SRC)'"
	@echo "STYLE_SRC    -> '$(STYLE_SRC)'"
	@echo "BIB_SRC      -> '$(BIB_SRC)'"
	@echo "MAIN_TEX     -> '$(MAIN_TEX)'"
	@echo "USE_BEAMER   -> '$(USE_BEAMER)'"
	@echo "FIGURES      -> '$(FIGURES)'"
	@echo "PDF          -> '$(PDF)'"
	@echo "RTF          -> '$(RTF)'"
	@echo "BACKUP_FILES -> '$(BACKUP_FILES)'"
	@echo "TO_MOVE      -> '$(TO_MOVE)'"
	@echo
	@echo "--- Main targets --- "
	@echo "MARKDOWN_DST -> '$(MARKDOWN_DST)'"
	@echo "TARGET_PDF   -> '$(TARGET_PDF)'"
	@echo "BEFORE_TARGETS -> '$(BEFORE_TARGETS)'"
	@echo "TARGETS      -> '$(TARGETS)'"
	@echo "TARGETS_DEPS -> '$(TARGETS_DEPS)'"
	@echo "CLEAN_TARGETS     -> '$(CLEAN_TARGETS)'"
	@echo "DISTCLEAN_TARGETS -> '$(DISTCLEAN_TARGETS)'"

############################### Archiving ################################
archive: clean
	tar -C ../ -cvzf ../$(SUPER_DIR).tar.gz --exclude ".svn" --exclude ".git"  --exclude "*~" --exclude ".DS_Store" $(SUPER_DIR)/

# force recompilation
force :
	@touch $(MAIN_TEX)
	@$(MAKE)


####################### LaTeX Compilation rules ########################
# Markdown files processing
ifneq ($(MARKDOWN_SRC),)
md markdown: $(MARKDOWN_DST)

ifeq ($(USE_BEAMER),)
%.md.tex: %.md
	@echo "==> generating LaTeX content from markdown file $<"
	$(PANDOC) --from markdown --to latex -o $@ $<
else
%.md.tex: %.md
	@echo "==> generating Beamer LaTeX content from markdown file $<"
	$(PANDOC) --from markdown --to beamer --slide-level 3 -o $@ $<
endif
endif

# Single LaTeX compilation
singlerun:
	@for f in $(MAIN_TEX); do \
		echo -e "==> Now running '$(LATEX) $(OPTIONAL_OPT) $(CMDLINE_OPTS) $$f'"; \
		$(LATEX) $(OPTIONAL_OPT) $(CMDLINE_OPTS) $$f; \
		exit_status=$$?; \
		if [ $$exit_status -ne 0 ]; then \
			tail -n 50 `basename $$f .tex`.log; \
			echo -e "\n\n"; \
			exit $$exit_status; \
		fi; \
	done

### Fast processing for a quick compilation
fast: $(TARGETS_DEPS)
	@$(MAKE) singlerun OPTIONAL_OPT=-draftmode

### Bibliography aspects
bib:
	@for f in $(MAIN_TEX); do \
		bib=`grep -e "^\s*[\][add]*bib.*{" $$f | cut -d '{' -f 2 | tr '}' ' '`;\
				if [ ! -z "$$bib" ]; then \
	  		echo "==> Now running BibTeX ($$bib used in $$f)";   \
			$(BIBTEX) `basename $$f .tex`;                       \
			echo -e "\n==> Now running second pdfLaTeX pass";    \
			$(MAKE) singlerun OPTIONAL_OPT=-draftmode; \
	   	fi; \
	done

# PDF generation
pdf: $(TARGET_PDF)

ifeq ($(GS),)
optimized-pdf:
	@echo "Unable to produce optimized PDF: gs command (from Ghostscript) not found"
else
optimized-pdf: $(TARGET_PDF)
	@for pdf in $(TARGET_PDF); do \
		targetpdf=$$(basename $$pdf '.pdf')-optim.pdf; \
		echo "=> producing optimized PDF '$$targetpdf' using Ghostscript"; \
		echo $(GS) $(GS_OPTS) -sOutputFile=$$targetpdf $$pdf; \
		$(GS) $(GS_OPTS) -sOutputFile=$$targetpdf $$pdf; \
	done
endif


create_output_dir:
	@if [ ! -d $(OUTPUT_DIR) ]; then                                                  \
		echo "    /!\ $(OUTPUT_DIR)/ does not exist ==> Now creating ./$(OUTPUT_DIR)/"; \
		mkdir -p ./$(OUTPUT_DIR);                                                 \
	fi;

$(TARGET_PDF): $(TARGETS_DEPS)
	@$(MAKE) singlerun OPTIONAL_OPT=-draftmode
	@$(MAKE) bib
	@$(MAKE) singlerun
	@$(MAKE) move_to_trash
	@if [ "$(OUTPUT_DIR)" != "." ]; then              \
		$(MAKE) create_output_dir;                    \
		for pdf in $(PDF); do                         \
			echo "==> Now moving $$pdf to $(OUTPUT_DIR)/"; \
			mv $$pdf $(OUTPUT_DIR);                   \
		done;                                         \
	fi
	@echo "==> $@ successfully generated"


# Useful hack for generating Exercise Correction CORRECTION_<main>.tex
# see https://ctan.org/pkg/exercise
ifneq (, $(shell grep '\\usepackage.*{exercise}' *.tex *.sty 2>/dev/null))
TMP_FILE4CORRECTION = .putSolutions.tex
correction: clean md
	@if [ ! -f "$(TMP_FILE4CORRECTION)" ]; then  \
	   	echo "\clearpage{\footnotesize \shipoutAnswer}" > $(TMP_FILE4CORRECTION); \
	else \
		echo "/!\ The file $(TMP_FILE4CORRECTION) already exists!"; \
	fi
	@for f in $(MAIN_TEX); do \
		correction_file=CORRECTION_$$f; \
		echo "===> generating '$$f' and correction file '$$correction_file"; \
	   	if [ -f "$$correction_file" ]; then            \
				 echo "/!\ correction file already present"; \
	  	else \
			   echo " -> generating symlink '$$correction_file' and compiling it"; \
			   ln -s $$f $$correction_file;         \
			   $(MAKE) MAIN_TEX=$$correction_file;  \
			   rm -f $$correction_file $(TMP_FILE4CORRECTION) *.synctex.gz; \
			fi;           \
	done
	@[ -f $(TMP_FILE4CORRECTION) ] && rm $(TMP_FILE4CORRECTION) || true

else

correction:
	@echo "Nothing to do: package exercise does not seem to be used"
endif

############################### clean ################################
TO_TRASH=$(shell ls $(TO_MOVE) 2>/dev/null | xargs echo)
move_to_trash:
	@if [ ! -z "${TO_TRASH}" -a -d $(TRASH_DIR) -a "$(TRASH_DIR)" != "." ]; then  \
		echo "==> Now moving $(TO_TRASH) to $(TRASH_DIR)/";                   \
        mv -f ${TO_TRASH} $(TRASH_DIR)/;                                      \
    elif [ ! -d $(TRASH_DIR) ]; then                             \
		echo "*****************************************************"; \
        echo "*** /!\ The trash directory $(TRASH_DIR)/ does not exist!!!";       \
        echo "***     May be you should create it to hide the files ${TO_TRASH}";\
        echo "***     Consider running 'make create_trash_dir'"; \
		echo "*****************************************************"; \
    fi;

create_trash_dir:
	@if [ ! -d $(TRASH_DIR) ]; then \
		echo "/!\ $(TRASH_DIR)/ does not exist ==> Now creating ./$(TRASH_DIR)/"; \
		mkdir -p ./$(TRASH_DIR); \
  else echo "... $(TRASH_DIR)  directory already exists"; \
	fi;
	@if [ ! -f $(TRASH_DIR)/.gitignore ]; then \
	  echo "/!\ $(TRASH_DIR)/.gitignore does not exist: creating it"; \
		echo '*' > $(TRASH_DIR)/.gitignore; \
		echo '!.gitignore' >> $(TRASH_DIR)/.gitignore; \
  else echo "... $(TRASH_DIR)/.gitignore already exists"; \
	fi;


# Clean option
clean: $(CLEAN_TARGETS)
distclean: $(DISTCLEAN_TARGETS)

clean-latex:
	rm -f $(TARGETS) $(RTF) $(TO_MOVE) $(BACKUP_FILES) *.synctex.gz* CORRECTION_*
	@if [ ! -z "$(OUTPUT_DIR)" -a -d $(OUTPUT_DIR) -a "$(OUTPUT_DIR)" != "." ]; then       \
		for f in $(MAIN_TEX); do                                  \
			base=`basename $$f .tex`;                            \
			echo "==> Now cleaning $(OUTPUT_DIR)/$$base*";       \
			rm -rf $(OUTPUT_DIR)/$$base*;                        \
		done                                                      \
	fi
	@if [ "$(OUTPUT_DIR)" == "." ]; then                         \
	   for f in $(MAIN_TEX); do                                  \
		base=`basename $$f .tex`;                            \
		echo "==> Now cleaning $$base.pdf"; \
		rm -rf $$base.pdf;                	     \
	   done							     \
	fi
	@if [ ! -z "$(TRASH_DIR)" -a -d $(TRASH_DIR)  -a "$(TRASH_DIR)" != "." ];   then       \
	   for f in $(MAIN_TEX); do                                  \
		base=`basename $$f .tex`;                            \
		echo "==> Now cleaning $(TRASH_DIR)/$$base*";        \
		rm -rf $(TRASH_DIR)/$$base*;                         \
	   done                                                      \
	fi
	@if [ ! -z "$(HTML_DIR)" -a -d $(HTML_DIR) -a "$(HTML_DIR)" != "." ]; then       \
	   echo "==> Now removing $(HTML_DIR)";                      \
	   rm  -rf $(HTML_DIR);                                      \
	fi

# RTF generation using latex2rtf
rtf $(RTF): $(TARGET_PDF)
ifeq ($(LATEX2RTF),)
	@echo "Please install latex2rtf to use this option!"
else
	@echo "==> Now generating $(RTF)"
	-cp $(TRASH_DIR)/*.aux $(TRASH_DIR)/*.bbl .
	@for f in $(MAIN_TEX); do    \
	   $(LATEX2RTF) -i english $$f;  \
	done
	@$(MAKE) move_to_trash
	@echo "==> $(RTF) is now generated"
endif

# HTML pages generation using  htlatex (see https://tug.org/tex4ht/)
ifeq ($(HTLATEX),)
html:
	@echo "Please install 'htlatex' to use this option!"
	@echo "('apt-get install texlive-plain-generic' under Debian)"
else
html:
	@echo "=> converting main LaTeX file to HTML using htlatex (see https://tug.org/tex4ht/)"
	@if [ ! -d ./$(HTML_DIR) ]; then                                    \
	   echo "$(HTML_DIR)/ does not exist => Now creating $(HTML_DIR)/"; \
	   mkdir -p ./$(HTML_DIR);                                          \
	fi
	@for f in $(MAIN_TEX); do    \
		$(HTLATEX) $$f "xhtml,html5,mathml,charset=utf-8" " -cunihtf -utf8" -d$(HTML_DIR)/; \
	done
	@rm -f *.4ct *.4tc *.aux *.bbl *.css *.dvi *.html *.idv *.lg *.log *.tmp *.xref
	@echo "=> HTML files generated in $(HTML_DIR)/"
	@echo "May be you can try to execute 'firefox ./$(HTML_DIR)/index.html'"
endif

# print help message
help :
	@echo "=== List of available commands: ==="
	@echo " - make:  Compile LaTeX files based on $(LATEX) engine."
	@echo "          Generated files (pdf etc.) are placed in $(OUTPUT_DIR)/"
	@echo "          Trashed files are moved into $(TRASH_DIR)"
	@echo "   Equivalent of make pdf"
	@echo " - make force: Force re-compilation, even if not needed"
	@echo " - make fast:  Fast (single round) draft compilation"
	@echo " - make clean: Remove nearly all generated files  -- see CLEAN_TARGETS"
	@echo " - make distclean: Remove all generated files -- see DISTCLEAN_TARGETS"
	@echo " - make correction: IFF using exercise package, generate CORRECTION_*.pdf file"
	@echo "                    holding the corrections to the proposed exercises"
	@echo " - make html: Converting main LaTeX file to HTML using htlatex (see https://tug.org/tex4ht/)"
	@echo "              Output HTML/CSS files are placed into $(HTML_DIR)/"
	@echo " - make rtf:  Converting main LaTeX file to RTF using latex2rtf"
	@echo "              may be useful to facilitate further copy/paste in a Word document"
	@echo " - make optimized-pdf: create an optimimized PDF (in size, IEEE compliant) using Ghostscript"
	@echo
	@echo "Sub-commands involved in LaTeX compilation:"
	@echo " - make singlerun: single round of compilation."
	@echo "   make singlerun MAIN_TEX=<filename>.tex: single round of compilation for <filename>.tex"
	@echo " - make bib: Run $(BIBTEX) for bibliography management"
	@echo "   make bib MAIN_TEX=<filename>.tex: as above, for <filename>.tex"
	@echo " - make md: (preliminary) generation of LaTeX files from markdown sources using the pandoc utility"
	@echo "             This will transform *.md into *.md.tex, making it confortable for you to simply put in LaTeX:"
	@echo "             \\input{<filename>.md}"
	@echo "             to have the output file <filename>.md.tex included transparently into the LaTeX compilation workflow"
	@echo "        If the USE_BEAMER variable is set, the above command will tranform markdown sources to beamer LaTeX files"
	@echo "        (with slide level 3 i.e. '### ...' in markdown sources will be tranformed to \\frametitle{...} )"
	@echo " - make move_to_trash: move files listed in TO_MOVE pattern into $(TRASH_DIR)/"
	@echo
	@echo "Utility directives:"
	@echo " - make create_trash_dir: create the trash dir $(TRASH_DIR)"
	@echo " - make create_output_dir: create output dir \$$(OUTPUT_DIR) if different from '.'"
	@echo
	@echo "Customization:"
	@echo " This Makefile supports native inclusion of special files:"
	@echo "   $(MAKEFILE_BEFORE) or $(LOCAL_MAKEFILE) included (if the file exists) before the 'all' target"
	@echo "   $(MAKEFILE_AFTER)  included (if the file exists) after all directives and target are defined"
	@echo " Feel free to create (one of) these files to alter some of the default variables"
	@echo "  (typically in $(LOCAL_MAKEFILE)) or create new (custom) targets."
	@echo " Example of content of $(LOCAL_MAKEFILE) for changing the LaTeX engine and completing the 'make clean' behavior:"
	@echo "   LATEX = \$$(shell which lualatex)"
	@echo "   CLEAN_TARGETS += clean-topic"
	@echo "   clean-topic:"
	@echo "       @echo '=> custom clean for <topic>'"

ifneq (,$(wildcard $(MAKEFILE_AFTER)))
include $(MAKEFILE_AFTER)
endif
