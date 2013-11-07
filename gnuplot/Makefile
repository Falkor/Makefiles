####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
#
# --------------------------------------------------------------------------------
# This is a generic makefile in the sense that it doesn't require to be 
# modified when adding/removing new source files.
# --------------------------------------------------------------------------------
#
# Author: Sebastien Varrette <Sebastien.Varrette@uni.lu>
#          Web page : http://www-id.imag.fr/~svarrett/
# Version : 1.2
# Creation date : 2012-01-11
#
# Please refer to ../Makefile for more informations on compilation options
# Alternatively, you may try 'make help'
# 
GNUPLOTFILES       = $(wildcard *.gnuplot)
GNUPLOT_DATA       = $(wildcard *.dat)
GNUPLOT_OUTPUT     = $(wildcard *.pdf)
GNUPLOT_OUTPUT_EPS = $(wildcard *.eps)
# Do not work
REAL_GNUPLOT_OUTPUT = $(shell grep \"^set output\" *.gnuplot | awk '{ print $3 }' | sed \"s/\\\"//g\" | xargs echo )

ARTICLE_IMAGEDIR = ../../../articles/2013/ee-lsds2013/images

all: plot

plot: $(GNUPLOTFILES) $(GNUPLOT_DATA)
	@for plotscript in $(GNUPLOTFILES); do \
		echo "=> processing the GNUPlot script $$plotscript"; \
		gnuplot $$plotscript; \
	done
	@for f in `ls *.eps`; do epstopdf $$f; done

test:
	@echo "GNUPLOTFILES   = $(GNUPLOTFILES)"
	@echo "GNUPLOT_DATA   = $(GNUPLOT_DATA)"
	@echo "GNUPLOT_OUTPUT = $(GNUPLOT_OUTPUT)"
	@echo "GNUPLOT_OUTPUT_EPS  = $(GNUPLOT_OUTPUT_EPS)"
	@echo "REAL_GNUPLOT_OUTPUT = $(REAL_GNUPLOT_OUTPUT)"


%.pdf: %.eps
	epstopdf $<

clean: 
	@if [ "$(GNUPLOT_OUTPUT)" != "" ]; then \
        echo "=> removing $(GNUPLOT_OUTPUT)"; \
        rm -f $(GNUPLOT_OUTPUT); \
    fi
	@if [ "$(GNUPLOT_OUTPUT_EPS)" != "" ]; then \
        echo "=> removing $(GNUPLOT_OUTPUT_EPS)"; \
        rm -f $(GNUPLOT_OUTPUT_EPS); \
    fi

sync: plot
	rsync -avzu *.pdf $(ARTICLE_IMAGEDIR)/


#%:
#	$(MAKE) -C ../ $@

