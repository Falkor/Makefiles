##################################################################################
# Makefile - Configuration file for GNU make (http://www.gnu.org/software/make/)
# Time-stamp: <Thu 2016-02-11 12:52 svarrette>
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

# Command-line tools for image optimization 
JPEGOPTIM = $(shell which jpegoptim)
OPTIPNG   = $(shell which optipng)
GS        = $(shell which gs)
# Their relatives options
JPEGOPTIM_OPTS =
OPTIPNG_OPTS   = -o9 -zm1-9 -strip all
GS_OPTS        = -q -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/prepress

# Sources
SRC_JPG = $(wildcard *.jpg)
SRC_PNG = $(wildcard *.png)
SRC_PDF = $(wildcard *.pdf)
PDF     = $(SRC_PDF:.pdf=-optimized.pdf)

SRC     = $(SRC_JPG) $(SRC_PNG) $(SRC_PDF)
TARGETS = $(SRC_JPG) $(SRC_PNG) $(PDF)

.PHONY: all optim jpeg png pdf print FORCE
###########################################
all:
	@echo "By default, this makefile does nothing ;) run 'make optim' to optimize the images"

optim:    $(TARGETS)
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
	@echo
	@echo "SRC_JPG = $(SRC_JPG)"
	@echo "SRC_PNG = $(SRC_PNG)"
	@echo "SRC_PDF = $(SRC_PDF)"
	@echo "PDF     = $(PDF)"
	@echo "TARGETS = $(TARGETS)"


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
