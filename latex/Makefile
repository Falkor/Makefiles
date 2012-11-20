####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
# Time-stamp: <Wed 2012-10-03 15:59 svarrette>
#     __  __       _         __ _ _            __   _         _____   __  __
#    |  \/  | __ _| | _____ / _(_) | ___      / /  | |    __ |_   _|__\ \/ /
#    | |\/| |/ _` | |/ / _ \ |_| | |/ _ \    / /   | |   / _` || |/ _ \\  / 
#    | |  | | (_| |   <  __/  _| | |  __/   / /    | |__| (_| || |  __//  \
#    |_|  |_|\__,_|_|\_\___|_| |_|_|\___|  /_/     |_____\__,_||_|\___/_/\_\
#
# Copyright (c) 2012 Sebastien Varrette <Sebastien.Varrette@uni.lu>
# .             http://varrette.gforge.uni.lu
#
####################################################################################
# Compilation of files written in LaTeX. 
# 
# Grab the lastest version of this Makefile from:
#            https://github.com/Falkor/Makefiles/tree/devel/latex
#
# --------------------------------------------------------------------------------
# This is a generic makefile in the sense that it doesn't require to be 
# modified when adding/removing new source files.
# --------------------------------------------------------------------------------
#
# This makefile search for LaTeX sources from the current directory, identifies 
# the main files (i.e the one containing the sequence '\begin{document}') and 
# launch the compilation for the generation of PDFs and optionnaly compressed 
# Postscript files. 
# Two compilation modes can be configured using the USE_PDFLATEX variable:
#    1/ Rely on pdflatex to generate directly a pdfs from the LaTeX sources. 
#       The compilation follow then the scheme: 
#
#       main.tex --[pdflatex/bibtex]--> main.pdf + main.[aux|log etc.]
#
#       Note that in that case, your figures should be in pdf format instead of eps.
#       To use this mode, just set the USE_PDFLATEX variable to 'yes'
# 
#    2/ Respect the classical scheme:                             +-[dvips]-> main.ps
#                                                                 |             |             
#                                                                 |        +-[gzip]
#       main.tex -[latex/bibtex]-> main.dvi + main.[aux|log etc.]-+        |     
#                                                                 |        +-> main.ps.gz     
#                                                                 +-[dvipdf]-> main.pdf
#       To use this mode, just set the USE_PDFLATEX variable to 'no'
# In all cases: 
#   - all the intermediate files (main.aux, main.log etc.) will be moved
#     to $(TRASH_DIR)/ (if it exists). 
#   - the gererated files (optional dvi file, pdf, compressed Postscript files)
#     will stay in the current directory.    
#
# Available Commands : see `make help`

############################## Variables Declarations ##############################
SHELL = /bin/bash

# set to 'yes' to use pdflatex for the direct generation of pdf from LaTeX sources
# set to 'no' to use the classical scheme tex -> dvi -> [ps|pdf] by dvips
USE_PDFLATEX = yes

# Directory where PDF, Postcript files and other generated files will be placed
# /!\ Please ensure there is no trailing space after the values
OUTPUT_DIR  = .
TRASH_DIR   = .Trash
HTML_DIR    = $(OUTPUT_DIR)/HTML
SUPER_DIR   = $(shell basename `pwd`)
RELEASE_DIR = release
LIST_RELEASE_TO_DELETE = $(shell ls $(RELEASE_DIR)/*.pdf | grep -v $(VERSION) | xargs echo)

# Check availibility of source files
TEX_SRC    = $(wildcard *.tex)
STYLE_SRC  = $(wildcard *.sty)
BIB_SRC    = $(wildcard *.bib)
ifeq ($(TEX_SRC),)
all:
	@echo "No source files available - I can't handle the compilation"
	@echo "Please check the presence of source files (with .tex extension)"
else
# Main tex file and figures it may depend on 
MAIN_TEX   = $(shell grep -l "[\]begin{document}" $(TEX_SRC) | xargs echo)
FIGURES    = $(shell find . -name "*.eps" -o -name "*.fig" | xargs echo)
ifeq ($(MAIN_TEX),)
all:
	@echo "I can't find any .tex file with a '\begin{document}' directive "\
		"among $(TEX_SRC). Please define a main tex file!"
else
# Commands used during compilation
ifneq ("$(USE_PDFLATEX)", "yes")
LATEX        = $(shell which latex)
else
LATEX        = $(shell which pdflatex)
endif
CMDLINE_OPTS = -halt-on-error -interaction=batchmode
LATEX2HTML   = $(shell which latex2html)
LATEX2RTF    = $(shell which latex2rtf)
BIBTEX       = $(shell which bibtex)
DVIPS        = $(shell which dvips)
DVIPDF       = $(shell which dvipdf)
PS2PDF       = $(shell which ps2pdf)
GZIP         = $(shell which gzip)
GRB          = $(shell which grb)
GITFLOW      = $(shell which git-flow)
# Generated files
DVI    	     = $(MAIN_TEX:%.tex=%.dvi)
PS           = $(MAIN_TEX:%.tex=%.ps)
PS_GZ        = $(MAIN_TEX:%.tex=%.ps.gz)
PDF          = $(MAIN_TEX:%.tex=%.pdf)
RTF          = $(MAIN_TEX:%.tex=%.rtf)
TARGET_PDF   = $(PDF)   
TARGET_PS_GZ = $(PS_GZ) 
ifneq ($(OUTPUT_DIR),.)
TARGET_PDF   = $(PDF:%=$(OUTPUT_DIR)/%)
TARGET_PS_GZ = $(PS_GZ:%=$(OUTPUT_DIR)/%) 
endif
BACKUP_FILES = $(shell find . -name "*~")
# Files to move to $(TRASH_DIR) after compilation
# Never add *.tex (or any reference to source files) for this variable.
TO_MOVE      = *.aux *.log *.toc *.lof *.lot *.bbl *.blg *.out *.nav *snm *.vrb *.rel

# Git stuff management
LAST_TAG_COMMIT = $(shell git rev-list --tags --max-count=1)
LAST_TAG = $(shell git describe --tags $(LAST_TAG_COMMIT) )
TAG_PREFIX = "v"

VERSION  = $(shell [ -f VERSION ] && head VERSION || echo "0.0.1")
# OR try to guess directly from the last git tag
#VERSION    = $(shell  git describe --tags $(LAST_TAG_COMMIT) | sed "s/^$(TAG_PREFIX)//")
MAJOR      = $(shell echo $(VERSION) | sed "s/^\([0-9]*\).*/\1/")
MINOR      = $(shell echo $(VERSION) | sed "s/[0-9]*\.\([0-9]*\).*/\1/")
PATCH      = $(shell echo $(VERSION) | sed "s/[0-9]*\.[0-9]*\.\([0-9]*\).*/\1/")
# total number of commits 		
BUILD      = $(shell git log --oneline | wc -l | sed -e "s/[ \t]*//g")

#REVISION   = $(shell git rev-list $(LAST_TAG).. --count)
#ROOTDIR    = $(shell git rev-parse --show-toplevel)
NEXT_MAJOR_VERSION = $(shell expr $(MAJOR) + 1).0.0-b$(BUILD)
NEXT_MINOR_VERSION = $(MAJOR).$(shell expr $(MINOR) + 1).0-b$(BUILD)
NEXT_PATCH_VERSION = $(MAJOR).$(MINOR).$(shell expr $(PATCH) + 1)-b$(BUILD)

### Main variables
TARGETS      = $(TARGET_PDF)
TARGETS_DEPS = $(TEX_SRC) $(STYLE_SRC) $(BIB_SRC) $(FIGURES) 

.PHONY: all archive clean create_output_dir dvi force help html move_to_trash pdflatex ps release rtf setup singlerun start_bump_major start_bump_minor start_bump_patch test versioninfo 

############################### Now starting rules ################################
# Required rule : what's to be done each time 
all: $(TARGETS)

# Test values of variables - for debug purposes  
test:
	@echo "USE_PDFLATEX: '$(USE_PDFLATEX)'"
	@echo "--- Directories --- "
	@echo "OUTPUT_DIR   -> '$(OUTPUT_DIR)'"
	@echo "TRASH_DIR    -> '$(TRASH_DIR)'"
	@echo "HTML_DIR     -> '$(HTML_DIR)'"
	@echo "SUPER_DIR    -> '$(SUPER_DIR)'"
	@echo "RELEASE_DIR  -> '$(RELEASE_DIR)'"
	@echo "--- Compilation commands --- "
	@echo "LATEX        -> '$(LATEX)'"
	@echo "LATEX2HTML   -> '$(LATEX2HTML)'"
	@echo "CMDLINE_OPTS -> '$(CMDLINE_OPTS)'"
	@echo "LATEX2RTF    -> '$(LATEX2RTF)'"
	@echo "BIBTEX       -> '$(BIBTEX)'"
	@echo "DVIPS        -> '$(DVIPS)'"
	@echo "DVIPDF       -> '$(DVIPDF)'"
	@echo "GZIP         -> '$(GZIP)'"
	@echo "GRB          -> '$(GRB)'"
	@echo "GITFLOW      -> '$(GITFLOW)'"
	@echo "--- Files --- "
	@echo "TEX_SRC      -> '$(TEX_SRC)'"
	@echo "STYLE_SRC    -> '$(STYLE_SRC)'"
	@echo "BIB_SRC      -> '$(BIB_SRC)'"
	@echo "MAIN_TEX     -> '$(MAIN_TEX)'"
	@echo "FIGURES      -> '$(FIGURES)'"
	@echo "DVI          -> '$(DVI)'"
	@echo "PS           -> '$(PS)'"
	@echo "PS_GZ        -> '$(PS_GZ)'"
	@echo "PDF          -> '$(PDF)'"
	@echo "RTF          -> '$(RTF)'"
	@echo "BACKUP_FILES -> '$(BACKUP_FILES)'"
	@echo "TO_MOVE      -> '$(TO_MOVE)'"
	@echo "TARGET_PS_GZ -> '$(TARGET_PS_GZ)'"
	@echo "TARGET_PDF   -> '$(TARGET_PDF)'"
	@echo "--- Main targets --- "
	@echo "TARGETS      -> '$(TARGETS)'"
	@echo "TARGETS_DEPS -> '$(TARGETS_DEPS)'"
	@echo "Consider running 'make versioninfo' to get info on git versionning variables"

############################### Archiving ################################
archive: clean
	tar -C ../ -cvzf ../$(SUPER_DIR)-$(VERSION).tar.gz --exclude ".svn" --exclude ".git"  --exclude "*~" --exclude ".DS_Store" --exclude "'$(RELEASE_DIR)/*.pdf'" $(SUPER_DIR)/

############################### Git Bootstrapping rules ################################
ifeq ($(GRB),)
setup: 
	@echo "Unable to find the 'grb' binary. Install it to setup your git repository."
	@echo "See https://github.com/webmat/git_remote_branch/ for more details"
else 
setup:
	grb grab production
	git config gitflow.branch.master     production
	git config gitflow.branch.develop    devel
	git config gitflow.prefix.feature    feature/
	git config gitflow.prefix.release    release/
	git config gitflow.prefix.hotfix     hotfix/
	git config gitflow.prefix.support    support/
	git config gitflow.prefix.versiontag $(TAG_PREFIX)
	git submodule init
	git submodule update
endif

versioninfo:
	@echo "Current version: $(VERSION)"
	@echo "Last tag: $(LAST_TAG)"
	@echo "$(shell git rev-list $(LAST_TAG).. --count) commit(s) since last tag"
	@echo "Build: $(BUILD) (total number of commits)"
	@echo "next major version: $(NEXT_MAJOR_VERSION)"
	@echo "next minor version: $(NEXT_MINOR_VERSION)"
	@echo "next patch version: $(NEXT_PATCH_VERSION)"

# Git flow management - this should be factorized 
ifeq ($(GITFLOW),)
start_bump_patch start_bump_minor start_bump_major release: 
	@echo "Unable to find git-flow on your system. "
	@echo "See https://github.com/nvie/gitflow for installation details"
else
start_bump_patch: clean
	@echo "Start the patch release of the repository from $(VERSION) to $(NEXT_PATCH_VERSION)"
	git pull origin
	git flow release start $(NEXT_PATCH_VERSION)
	@echo $(NEXT_PATCH_VERSION) > VERSION
	git commit -s -m "Patch bump to version $(NEXT_PATCH_VERSION)" VERSION
	@echo "=> remember to update the version number in $(MAIN_TEX)"
	@echo "=> run 'make release' once you finished the bump"

start_bump_minor: clean
	@echo "Start the minor release of the repository from $(VERSION) to $(NEXT_MINOR_VERSION)"
	git pull origin
	git flow release start $(NEXT_MINOR_VERSION)
	@echo $(NEXT_MINOR_VERSION) > VERSION
	git commit -s -m "Minor bump to version $(NEXT_MINOR_VERSION)" VERSION
	@echo "=> remember to update the version number in $(MAIN_TEX)"
	@echo "=> run 'make release' once you finished the bump"

start_bump_major: clean
	@echo "Start the major release of the repository from $(VERSION) to $(NEXT_MAJOR_VERSION)"
	git pull origin
	git flow release start $(NEXT_MAJOR_VERSION)
	@echo $(NEXT_MAJOR_VERSION) > VERSION
	git commit -s -m "Major bump to version $(NEXT_MAJOR_VERSION)" VERSION
	@echo "=> remember to update the version number in $(MAIN_TEX)"
	@echo "=> run 'make release' once you finished the bump"


release: clean $(TARGET_PDF)
	@if [ ! -d $(RELEASE_DIR) ]; then \
		echo "    /!\ $(RELEASE_DIR)/ does not exist ==> Now creating ./$(RELEASE_DIR)/"; \
		mkdir -p ./$(RELEASE_DIR); \
	fi;
	@for pdf in $(TARGET_PDF); do \
		targetpdf=`basename $$pdf .pdf`-v$(VERSION).pdf ; \
		base=`basename $$pdf .pdf`; \
		cp $$pdf $(RELEASE_DIR)/$$targetpdf; \
		git add -f $(RELEASE_DIR)/$$targetpdf; \
		git commit -s -m "Add a copy of version $(VERSION) of $$pdf in $(RELEASE_DIR)/"; \
	done
	@if [ -n "$(LIST_RELEASE_TO_DELETE)" ]; then \
		git rm $(LIST_RELEASE_TO_DELETE); \
		git commit -s -m "Delete previous release copy $(LIST_RELEASE_TO_DELETE)" $(LIST_RELEASE_TO_DELETE); \
	fi
	git flow release finish -s $(VERSION)
	git push origin
	git push origin --tags
endif

############################### LaTeX Compilation rules ################################

### Single LaTeX compilation 
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
		bib=`grep "^[\]bibliography{" $$f|sed -e "s/^[\]bibliography{\(.*\)}/\1/"|tr "," " "`;\
		if [ ! -z "$$bib" ]; then \
	  		echo "==> Now running BibTeX ($$bib used in $$f)";   \
			$(BIBTEX) `basename $$f .tex`;                       \
			echo -e "\n==> Now running second pdfLaTeX pass";    \
			$(MAKE) singlerun OPTIONAL_OPT=-draftmode; \
	   	fi; \
	done

### DVI generation (only relevant if USE_PDFLATEX != yes)
dvi: $(DVI)

ifneq ("$(USE_PDFLATEX)", "yes")
$(DVI): $(TARGETS_DEPS)
	@$(MAKE) singlerun OPTIONAL_OPT=-draftmode
	@$(MAKE) bib
	@$(MAKE) singlerun
	@$(MAKE) move_to_trash
else
$(DVI): 
	@echo "Not relevant if USE_PDFLATEX = $(USE_PDFLATEX)"
endif

### Postscript generation
ps: $(TARGET_PS_GZ)

ifneq ("$(USE_PDFLATEX)", "yes")
$(TARGET_PS_GZ): $(DVI)
	@for dvi in $?; do                            \
	   	ps=`basename $$dvi .dvi`.ps;                  \
	   	echo "==> Now generating $$ps.gz from $$dvi"; \
	  	$(DVIPS) -q -o $$ps $$dvi;                    \
	   	$(GZIP) -f $$ps;                              \
	done
	@if [ "$(OUTPUT_DIR)" != "." ]; then              \
		$(MAKE) create_output_dir;                    \
		for ps in $(PS); do                           \
			echo "==> Now moving $$ps.gz to $(OUTPUT_DIR)/"; \
			mv $$ps.gz $(OUTPUT_DIR);                 \
		done;                                         \
	fi
else
$(TARGET_PS_GZ): $(TARGET_PDF)
	@for pdf in $?; do                                \
		ps=`basename $$pdf .pdf`.ps;  
	   	echo "==> Now generating $$ps.gz from $$pdf"; \
	  	$(PDF2PS) $$pdf $$ps;                     \
	   	$(GZIP) -f $$ps;                          \
	done

endif

### PDF generation
pdf: $(TARGET_PDF)

ifneq ("$(USE_PDFLATEX)", "yes")

$(TARGET_PDF): $(DVI)
	@for dvi in $?; do                                \
	   	pdf=`basename $$dvi .dvi`.pdf;                \
	   	echo "==> Now generating $$pdf from $$dvi";   \
	  	$(DVIPDF) $$dvi;                              \
	done
	@if [ "$(OUTPUT_DIR)" != "." ]; then              \
		$(MAKE) create_output_dir;                    \
		for pdf in $(PDF); do                         \
			echo "==> Now moving $$pdf to $(OUTPUT_DIR)/"; \
			mv $$pdf $(OUTPUT_DIR);                   \
		done;                                         \
	fi
	@echo "==> $@ successfully generated"

else

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
endif


TO_TRASH=$(shell ls $(TO_MOVE) 2>/dev/null | xargs echo)
move_to_trash:
	@if [ ! -z "${TO_TRASH}" -a -d $(TRASH_DIR) -a "$(TRASH_DIR)" != "." ]; then  \
                echo "==> Now moving $(TO_TRASH) to $(TRASH_DIR)/";                   \
                mv -f ${TO_TRASH} $(TRASH_DIR)/;                                      \
        elif [ ! -d $(TRASH_DIR) ]; then                             \
				echo "*****************************************************"; \
                echo "*** /!\ The trash directory $(TRASH_DIR)/ does not exist!!!";       \
                echo "***     May be you should create it to hide the files ${TO_TRASH}";\
                echo "***     Consider running 'mkdir -p $(TRASH_DIR)'"; \
				echo "*****************************************************"; \
        fi;   

create_output_dir: 
	@if [ ! -d $(OUTPUT_DIR) ]; then                                                  \
		echo "    /!\ $(OUTPUT_DIR)/ does not exist ==> Now creating ./$(OUTPUT_DIR)/"; \
		mkdir -p ./$(OUTPUT_DIR);                                                 \
	fi;  

# plot:
# 	$(MAKE) -C images/gnuplot plot
# 	$(MAKE) -C images/gnuplot pdf

# Clean option
clean:
	rm -f *.dvi $(RTF) $(TO_MOVE) $(BACKUP_FILES)
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
		echo "==> Now cleaning $$base.ps.gz and $$base.pdf"; \
		rm -rf $$base.ps.gz $$base.pdf;                	     \
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

# # force recompilation
# force :
# 	@touch $(MAIN_TEX)
# 	@$(MAKE)


# print help message
help :
	@echo '+----------------------------------------------------------------------+'
	@echo '|                        Available Commands                            |'
	@echo '+----------------------------------------------------------------------+'
	@echo '| make:         Compile LaTeX files.                                   |'
	@echo '|               Generated files (pdf etc.) are placed in $(OUTPUT_DIR)/ '
	@echo '| make force:   Force re-compilation, even if not needed               |'
	@echo '| make clean:   Remove all generated files                             |'
	@echo '| make html:    Generate HTML files from TeX in $(HTML_DIR)/           '
	@echo '| make help:    Print help message                                     |'
	@echo '| make setup:   Initiate git-flow for your local copy of the repository|'
	@echo '| make start_bump_{major,minor,patch}: start a new version release with|'
	@echo '|               git-flow at a given level (major, minor or patch bump) |'
	@echo '| make release: Finalize the release using git-flow                    |'
	@echo '| make rtf:     Generate an RTF file from LaTeX sources (useful for    |'
	@echo '|               further copy/paste in a Word document)                 |'
	@echo '+----------------------------------------------------------------------+'

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
	@$(MAKE) help
endif


# HTML pages generation using latex2html
# First check that $(LATEX2HTML) and $(HTML_DIR)/ exist
html :
ifeq ($(LATEX2HTML),)
	@echo "Please install latex2html to use this option!"
	@echo "('apt-get install latex2html' under Debian)"
else
	@if [ ! -d ./$(HTML_DIR) ]; then                                    \
	   echo "$(HTML_DIR)/ does not exist => Now creating $(HTML_DIR)/"; \
	   mkdir -p ./$(HTML_DIR);                                          \
	fi
	-cp $(TRASH_DIR)/*.aux $(TRASH_DIR)/*.bbl .
	$(LATEX2HTML) -show_section_numbers -local_icons -split +1 \
		-dir $(HTML_DIR) $(MAIN_TEX)
	@rm -f *.aux *.bbl $(HTML_DIR)/*.tex $(HTML_DIR)/*.aux $(HTML_DIR)/*.bbl
	@echo "==> HTML files generated in $(HTML_DIR)/" 
	@echo "May be you can try to execute 'mozilla ./$(HTML_DIR)/index.html'"
endif
endif
endif  # End of ifeq ($(TEX_SRC),)

