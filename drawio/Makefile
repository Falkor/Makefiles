####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
# Time-stamp: <Fri 2024-11-08 12:39 svarrette>
#
#     __  __       _         __ _ _
#    |  \/  | __ _| | _____ / _(_) | ___
#    | |\/| |/ _` | |/ / _ \ |_| | |/ _ \
#    | |  | | (_| |   <  __/  _| | |  __/
#    |_|  |_|\__,_|_|\_\___|_| |_|_|\___|
#
#
####################################################################################
#
############################## Variables Declarations ##############################

DRAWIOFILES = $(wildcard *.drawio)
PDF = $(DRAWIOFILES:%.drawio=%.pdf)
PNG = $(DRAWIOFILES:%.drawio=%.png)

# Drawio (now diagrams.net) -- see https://www.diagrams.net/
# Desktop (electron-based) cross platform application: https://github.com/jgraph/drawio-desktop/
DRAWIO=$(shell which drawio)
DRAWIO_OPTS=--export --transparent --crop

TARGETS  = $(PDF) $(PNG)

#################### Let's go ####################
ifeq ($(DRAWIO),)
all:
	$(error Expected to have drawio Desktop application installed: see 'https://github.com/jgraph/drawio-desktop/releases/')
else

all: $(TARGETS)

endif

info:
	@echo "DRAWIOFILES = $(DRAWIOFILES)"
	@echo "PDF     = $(PDF)"
	@echo "PNG     = $(PNG)"
	@echo "TARGETS = $(TARGETS)"

pdf: $(PDF)
png: $(PNG)

%.pdf: %.drawio
	@echo "=> generating $@ from drawio diagram $<"
	$(DRAWIO) $(DRAWIO_OPTS) -f pdf $< -o $@

%.png: %.pdf
	@echo "=> generating $@ from PDF file $<"
	convert -density 600 -quality 100 $< $@


# FIXME : use pdftoppm -png instead
# %.png: %.pdf
# 	convert -density 400 -depth 8 -quality 100 $< $@

distclean:
	rm -f $(PDF) $(PNG)
