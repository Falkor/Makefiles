####################################################################################
# Makefile - Configuration file for GNU make (http://www.gnu.org/software/make/)
# Time-stamp: <Sat 2024-02-24 23:52 svarrette>
#
# Copyright (c) 2016-2024 Sebastien Varrette
#
# [Recursive] Compilation of LaTeX projects, those sources are expected under
# $(SRCDIR) (_i.e._ src/ ).
#
# This makefile is meant to support the 'make release' that will perform the
# following tasks:
#   - generate a fresh new PDF from the LaTeX sources within $(SRCDIR)/
#   - optimize that PDF (using ghostscript) and place a copy in the current directory
#
# It's up to you then to commit that (optimized) PDF. It's a good workflow to separate
# the continuous work on the src/ where you **SHOULD NOT** commit any produced PDF,
# and the directory where you have this Makefile to release at some point the LaTeX
# document. It also ensure anybody who clone this repo will have a valid PDF copy of
# the document without having to recompile it.
#
############################## Variables Declarations ################################

DIRS    = $(sort $(dir $(wildcard */)))
SRCDIR  = src
SRC     = $(wildcard $(SRCDIR)/[^_]*.tex)
PDF     = $(SRC:.tex=.pdf)
RELEASED_PDF = $(notdir $(PDF))

# Ghostscript for the help of producing optimized PDF
GS      = $(shell which gs)
GS_OPTS = -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/prepress

# pdftoppm to help producing cover images
PDFTOPPM = $(shell which pdftoppm)
# Resolution of an image: dots per inch
DPI = 300

TAR_ARCHIVE_OPTS = --dereference --exclude '.git' --exclude '*~' --exclude '.root' --exclude '.texinfo' --exclude '.makefile.d' --exclude '*.graffle'

# Default targets
TARGETS = $(DIRS)

# Local configuration - Kept for compatibity reason
LOCAL_MAKEFILE = .Makefile.local
# Makefile custom hooks
MAKEFILE_BEFORE = .Makefile.before
MAKEFILE_AFTER  = .Makefile.after

############################### Now starting rules ################################
# Load local settings, if existing (to override variable eventually)
ifneq (,$(wildcard $(LOCAL_MAKEFILE)))
include $(LOCAL_MAKEFILE)
endif
ifneq (,$(wildcard $(MAKEFILE_BEFORE)))
include $(MAKEFILE_BEFORE)
endif

all: $(TARGETS)

# Test values of variables - for debug purposes
info:
	@echo "DIRS     = $(DIRS)"
	@echo "SRCDIR   = $(SRCDIR)"
	@echo "SRC      = $(SRC)"
	@echo "PDF      = $(PDF)"
	@echo "RELEASED_PDF = $(RELEASED_PDF)"
	@echo "GS       = $(GS)"
	@echo "GS_OPTS  = $(GS_OPTS)"
	@echo "PDFTOPPM = $(PDFTOPPM)"
	@echo "DPI      = $(DPI)"

# Add a specific 'release' target only if in the appropriate directory, such that
# otherwise, it is just passed to the subdirectory using the generic '%' target
ifneq ($(SRC),)
.PHONY: release cover
release: clean
	@echo "==> release a new PDF version of the LaTeX document '$(PDF)' from '`pwd`'"
	$(MAKE) -C $(SRCDIR)
	@if [ -n "$(GS)" ]; then \
		for f in $(RELEASED_PDF); do \
			echo "==> producing optimized PDF '$$f' for releasing from '`pwd`'"; \
			$(GS) $(GS_OPTS) -sOutputFile=$$f $(SRCDIR)/$$f; \
    done \
	else  \
		for f in $(PDF); do \
			echo "==> blank copy of the (non-optimized) PDF from $(SRCDIR)"; \
		  cp $$f .; \
    done \
	fi

ifeq ($(PDFTOPPM),)
cover:
	@echo "Unable to find the 'pdftoppm' utility. Consider installing it: 'apt install poppler-utils' under Debian"
else
cover:
	@for f in $(RELEASED_PDF); do \
		if [ -f "$$f" ]; then \
			echo "=> generating cover image (first page, converted to png using pdftoppm) for $$f"; \
			echo $(PDFTOPPM) -png -r $(DPI) -f 1 -l 1 $$f cover_$$(basename $$f .pdf); \
			$(PDFTOPPM) -png -r $(DPI) -f 1 -l 1 $$f cover_$$(basename $$f .pdf); \
			echo "=> renaming cover file"; \
			mv cover_$$(basename $$f .pdf)-*.png cover_$$(basename $$f .pdf).png; \
		fi; \
	done

endif

endif # ifneq ($(SRC),)


# generic rule -- pass whatever target (incl. none) to each subdirs.
%:
	@for d in $(DIRS); do \
		echo "==> processing '$$d'"; \
		$(MAKE) -C $$d $@; \
	done

archive:
	@for d in $(DIRS:/=); do \
		if [ "$$d" == 'src' ]; then \
			echo "ignoring $$d (potential LaTeX source directory) - consider running 'make archive' from top directory "; \
			continue; \
		fi ;\
		echo "==> archiving $$d with"; \
		echo "    tar cvzf src_$${d}.tgz $(TAR_ARCHIVE_OPTS) $$d"; \
		tar cvzf src_$${d}.tgz $(TAR_ARCHIVE_OPTS) $$d; \
	done

help:
	@echo "=== List of available commands: ==="
	@echo " - make release: Compile LaTeX files under $(SRCDIR) and release an [optimized] PDF"
	@echo "                 in the current directory"
	@echo " - make cover: create a cover PNG file (image of the 1st page of the released PDF) using"
	@echo "               the pdftoppm utility (from the poppler-utils package)"
	@echo
	@echo "Any other command is passed to the subdirectory(ies): $(DIRS), typically as follows:"
	@echo "   make -C <subdir> <command>"


ifneq (,$(wildcard $(MAKEFILE_AFTER)))
include $(MAKEFILE_AFTER)
endif
