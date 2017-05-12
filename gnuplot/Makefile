####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
# Time-stamp: <Thu 2017-05-11 22:54 svarrette>
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

GNUPLOTFILES   = $(wildcard *.gnuplot)
GNUPLOT_DATA   = $(wildcard data/*.dat) $(wildcard *.dat)
GNUPLOT_OUTPUT = $(shell grep '^set output' *.gnuplot | awk '{ print $$3 }')
OUTPUT_EXT     = $(shell echo $(suffix $(GNUPLOT_OUTPUT)) | tr ' ' '\n' | sort | uniq)

all: plot

plot: $(GNUPLOTFILES) $(GNUPLOT_DATA)
	@for plotscript in $(GNUPLOTFILES); do \
		echo "=> processing the GNUPlot script '$$plotscript'"; \
		gnuplot $$plotscript; \
	done

print:
	@echo "GNUPLOTFILES   = $(GNUPLOTFILES)"
	@echo "GNUPLOT_DATA   = $(GNUPLOT_DATA)"
	@echo "GNUPLOT_OUTPUT = $(GNUPLOT_OUTPUT)"
	@echo "OUTPUT_EXT     = $(OUTPUT_EXT)"

%.pdf: %.eps
	epstopdf $<

clean:
	@if [ "$(GNUPLOT_OUTPUT)" != "" ]; then \
		echo "=> removing $(GNUPLOT_OUTPUT)"; \
		rm -f $(GNUPLOT_OUTPUT); \
	fi

#%:
#	$(MAKE) -C ../ $@
