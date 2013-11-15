####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
# Time-stamp: <Thu 2013-11-07 16:37 svarrette>
#
#     __  __       _         __ _ _       
#    |  \/  | __ _| | _____ / _(_) | ___  
#    | |\/| |/ _` | |/ / _ \ |_| | |/ _ \
#    | |  | | (_| |   <  __/  _| | |  __/ 
#    |_|  |_|\__,_|_|\_\___|_| |_|_|\___| 
# 
# Copyright (c) 2011 Sebastien Varrette <Sebastien.Varrette@uni.lu>
# .             http://varrette.gforge.uni.lu
#
####################################################################################
#
############################## Variables Declarations ##############################

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
	@for f in `ls *.eps`; do epstopdf $$f; rm -f $$f; done

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
	test -d $(ARTICLE_IMAGEDIR) && rsync -avzu *.pdf $(ARTICLE_IMAGEDIR)/


#%:
#	$(MAKE) -C ../ $@

