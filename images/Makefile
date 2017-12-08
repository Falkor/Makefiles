##################################################################################
# Makefile - Configuration file for GNU make (http://www.gnu.org/software/make/)
# Time-stamp: <Fri 2017-12-08 23:46 svarrette>
#
# Copyright (c) 2016 Sebastien Varrette <Sebastien.Varrette@uni.lu>
#
#  Specialized to optimization of images and pdf
#
# Available Commands
# ------------------
# make           : do nothing ;)
# make optim     : optimize the size of the local JPEG, PNG and PDF
#
############################## Variables Declarations ############################
SHELL=/bin/bash

LOCAL = .Makefile.local

### From Jean-Guillaume's Makefile

# Magnification for fig2dev
MAG=3.0

# set this for grayscale xfigures
ifdef FIGGRAY
	FIG2DEVCOLOR = -N
endif


# Command-line tools for image optimization
JPEGOPTIM			= $(shell which jpegoptim)
OPTIPNG				= $(shell which optipng)
GS						= $(shell which gs)
FIG2DEV				= $(shell which fig2dev)
INKSCAPE			= $(shell which inkscape)
RSVG_CONVERT	= $(shell which rsvg-convert)

# Their relatives options
JPEGOPTIM_OPTS =
OPTIPNG_OPTS   = -o9 -zm1-9 -strip all
GS_OPTS        = -q -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/prepress
FIG2DEV_OPTS   = -m ${MAG}
INKSCAPE_OPTS  = -z -D -d $(shell echo ${MAG}*90. | bc -l)

# Sources
SRC_FIG = $(wildcard *.fig)
SRC_SVG = $(wildcard *.svg)
SRC_JPG = $(wildcard *.jpg)
SRC_PNG = $(wildcard *.png)
SRC_PDF = $(filter-out $(SRC_SVG:%.svg=%.pdf), $(filter-out $(SRC_FIG:%.fig=%.pdf), $(wildcard *.pdf)))

PDF     = $(SRC_PDF:.pdf=-optimized.pdf)
FIG_EPS = $(filter-out $(FIG_PSPDFTEX:%.pspdftex=%.eps), $(SRC_FIG:%.fig=%.eps)) $(FIG_PSPDFTEX:%.pspdftex=%_pspdftex.eps)
FIG_PDF = $(filter-out $(FIG_PSPDFTEX:%.pspdftex=%.pdf), $(SRC_FIG:%.fig=%.pdf)) $(FIG_PSPDFTEX:%.pspdftex=%_pspdftex.pdf)
FIG_PNG = $(SRC_FIG:%.fig=%.png)
SVG_EPS = $(SRC_SVG:%.svg=%.eps)
SVG_PDF = $(SRC_SVG:%.svg=%.pdf)
SVG_PNG = $(SRC_SVG:%.svg=%.png)

define run-pspdftex
	- ${FIG2DEVPROG} -L pstex ${FIG2DEVCOLOR} $< $*_pspdftex.eps
	- ${FIG2DEVPROG} -L pdftex ${FIG2DEVCOLOR} $< $*_pspdftex.pdf
	- ${FIG2DEVPROG} -L pdftex_t -p $*_pspdftex $< $@
endef

SRC     = $(SRC_JPG) $(SRC_PNG) $(SRC_PDF) $(SRC_FIG) $(SRC_SVG)
TARGETS = $(SRC_JPG) $(SRC_PNG) $(PDF) $(FIG_PSPDFTEX) $(FIG_PDF) $(SVG_PDF)

ifneq ($(wildcard $(LOCAL)),)
include $(LOCAL)
endif

.PHONY: all fig optim jpeg png pdf print FORCE
###########################################
all: $(TARGETS)

#	@echo "By default, this makefile does nothing ;) run 'make optim' to optimize the images"

optim:    $(TARGETS)
fig:      $(SRC_FIG)
jpg jpeg: $(SRC_JPG)
png:      $(SRC_PNG)
pdf:      $(PDF)

print:
	@echo "JPEGOPTIM      = $(JPEGOPTIM)"
	@echo "JPEGOPTIM_OPTS = $(JPEGOPTIM_OPTS)"
	@echo "OPTIPNG        = $(OPTIPNG)"
	@echo "OPTIPNG_OPTS   = $(OPTIPNG_OPTS)"
	@echo "GS             = $(GS)"
	@echo "GS_OPTS        = $(GS_OPTS)"
	@echo "FIG2DEV        = $(FIG2DEV)"
	@echo "FIG2DEV_OPTS   = $(FIG2DEV_OPTS)"
	@echo
	@echo "SRC_FIG = $(SRC_FIG)"
	@echo "SRC_SVG = $(SRC_SVG)"
	@echo "SRC_JPG = $(SRC_JPG)"
	@echo "SRC_PNG = $(SRC_PNG)"
	@echo "SRC_PDF = $(SRC_PDF)"
	@echo "PDF     = $(PDF)"
	@echo "FIG_EPS = $(FIG_EPS)"
	@echo "FIG_PDF = $(FIG_PDF)"
	@echo "SVG_EPS = $(SVG_EPS)"
	@echo "SVG_PDF = $(SVG_PDF)"
	@echo "TARGETS = $(TARGETS)"


### Xfig
ifneq ($(FIG2DEV),)

%.pspdftex : %.fig ; $(run-pspdftex)

%.eps : %.fig
	${FIG2DEV} ${FIG2DEV_OPTS} -L eps ${FIG2DEVCOLOR} $< $@

%.png : %.fig
	${FIG2DEV} ${FIG2DEV_OPTS} -L png ${FIG2DEVCOLOR} $< $@

%.pdf : %.fig
	${FIG2DEV} ${FIG2DEV_OPTS} -L pdf ${FIG2DEVCOLOR} $< $@
else
$(FIG_EPS) $(FIG_PDF) $(FIG_PNG): FORCE
	@echo "/!\ unable to process '$@': unable to find the 'fig2dev' utility. Consider installing it."
endif

### Inkscape /  svg
### ifneq ($(INKSCAPE),)
ifneq ($(RSVG_CONVERT),)

%.eps : %.svg
	${RSVG_CONVERT} -f eps $< > $@
#	${INKSCAPE} ${INKSCAPE_OPTS} ${INKSCAPEGRAY} --file=$< --export-eps=$@

%.png : %.svg
	${RSVG_CONVERT} -f png $< > $@
#	${INKSCAPE} ${INKSCAPE_OPTS} --file=$< --export-png=$@

%.pdf : %.svg
	${RSVG_CONVERT} -f pdf $< > $@
#	${INKSCAPE} ${INKSCAPE_OPTS} ${INKSCAPEGRAY} --file=./$< --export-pdf=$@

else
$(SVG_EPS) $(SVG_PDF) $(SVG_PNG): FORCE
	@echo "/!\ unable to process '$@': unable to find the 'rsvg_convert' utility. Consider installing it."
endif



### JPEG
ifneq ($(JPEGOPTIM),)
%.jpg: FORCE
	@if [ ! -L "$@" ]; then \
		echo "==> optimizing '$@'"; \
		$(JPEGOPTIM) $@; \
	else \
		echo "==> ignoring '$@' which is a symlink"; \
	fi
else
%.jpg: FORCE
	@echo "/!\ unable to optimize '$@': unable to find the 'jpegoptim' utility. Consider installing it"
endif

### PNG
ifneq ($(OPTIPNG),)
%.png: FORCE
	@if [ ! -L "$@" ]; then \
		echo "==> optimizing '$@'"; \
		$(OPTIPNG) $(OPTIPNG_OPTS) $@; \
	else \
		echo "==> ignoring '$@' which is a symlink"; \
	fi
else
%.png: FORCE
	@echo "/!\ unable to optimize '$@': unable to find the 'optipng' utility. Consider installing it"
endif

### PDF
ifneq ($(GS),)
%-optimized.pdf: %.pdf
	@if [ ! -L "$@" ]; then \
		echo "==> optimizing '$@'"; \
		$(GS) $(GS_OPTS) -sOutputFile=$@ $<; \
		opti_size=`du -k $@ | cut -f1`; \
		size=`du -k $< | cut -f1`; \
		if [[ "$$opti_size" -lt "$$size" ]]; then \
			echo "    \__ optimized PDF of smaller size ($$opti_size vs. $$size) thus overwrite $<"; \
			mv $@ $<; \
		else \
			echo "    \__ already optimized PDF thus discarded"; \
			rm -f $@; \
		fi; \
	else \
		echo "==> ignoring '$@' which is a symlink"; \
	fi
else
%.pdf: FORCE
	@echo "/!\ unable to optimize '$@': unable to find the 'gs' utility (ghostscript). Consider installing it"
endif

FORCE:


clean:
	@if [ "$(FIG_PSPDFTEX)" != "" ]; then \
        echo "=> removing generated pspdftex (from xfig) files $(FIG_PSPDFTEX)"; \
        rm -f $(FIG_PSPDFTEX); \
    fi
	@if [ "$(FIG_EPS)$(SVG_EPS)" != "" ]; then \
        echo "=> removing generated EPS files $(FIG_EPS) $(SVG_EPS)"; \
        rm -f $(FIG_EPS) $(SVG_EPS); \
    fi
	@if [ "$(FIG_PDF)$(SVG_PDF)" != "" ]; then \
        echo "=> removing generated PDF files $(FIG_PDF) $(SVG_PDF)"; \
        rm -f $(FIG_PDF) $(SVG_PDF); \
    fi
