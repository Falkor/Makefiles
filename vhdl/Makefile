# Time-stamp: <Wed 2026-07-22 19:59 svarrette>
####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
#                     __  __       _         __ _ _
#                    |  \/  | __ _| | _____ / _(_) | ___
#                    | |\/| |/ _` | |/ / _ \ |_| | |/ _ \
#                    | |  | | (_| |   <  __/  _| | |  __/
#                    |_|  |_|\__,_|_|\_\___|_| |_|_|\___|
#
####################################################################################
# A generic and modular Makefile piloting a VHDL projects.
# See https://github.com/Falkor/Makefiles for more examples
#
# GHDL Tools installation:
#      sudo apt install ghdl-llvm ghdl-tools gtkwave
#
# **Default action upon 'make'**: see TARGETS
# Usage:
#     make help  # display all make directives
# - GHDL management:
#     make setup-project   setup a VHDL project according to
# - GHDL building options:
#     make lint
#     make {analyze,elaborate,run,view}
# - cleanup operation
#     make clean
####################################################################################
SHELL = /bin/bash

# Check commands needed for this Makefile - complete as needed (space separated)
EXECUTABLES = ghdl-llvm gtkwave
$(foreach exec,$(EXECUTABLES),\
  $(if $(shell command -v $(exec) 2>/dev/null),,$(error "$(exec) not available in PATH")))

##########################################################
# Main project name / entity name. /!\ ADAPT accordingly
PROJECT = uartloopb
# Top level entity name - MUST be defined within your sources !
TOP_LEVEL_ENTITY = $(PROJECT)_tb

##############################################
# Project layout: source and work directories
SRCDIR  = ../hdl
WORKDIR = work
# HDL source code
SRC = $(wildcard $(SRCDIR)/*.vhd) $(wildcard *.vhd)
OBJ = $(patsubst %.vhd,$(WORKDIR)/%.o,$(notdir $(SRC)))

# Compiler - https://ghdl-rad.readthedocs.io
GHDL = ghdl-llvm
GHDL_FLAGS = --std=08 -fsynopsys  --warn-no-hide --workdir=$(WORKDIR)
GHDL_SIM_OPTS = --ieee-asserts=disable --assert-level=error --stop-time=500ns

# Main targets for this makefile
TARGETS = setup-workdir $(OBJ) $(TOP_LEVEL_ENTITY)

####################################################
# local hook (before) for 'easy' customization. Ex:
# TARGETS += newdirective
# all: $(TARGETS)
ifneq ("$(wildcard .Makefile.local)","")
include .Makefile.local
endif
# As above, but generally meant to be git ignored...
ifneq ("$(wildcard .Makefile.custom)","")
include .Makefile.custom
endif

######### Let's go ##########
.PHONY: all
all: $(TARGETS)

###########
## Setup ##
###########
.PHONY: setup-workdir setup-project
setup-workdir:
	@if [ ! -d "$(WORKDIR)" ]; then \
		echo "=> creating work dir '$(WORKDIR)'"; \
		mkdir -p $(WORKDIR); \
	fi

setup-project:
	mkdir -p {constraints,hdl,sim,vivado}
	curl -L -o .gitignore https://github.com/github/gitignore/raw/refs/heads/main/community/Xilinx.gitignore
	cp $(firstword $(MAKEFILE_LIST)) sim/Makefile
	(echo -n -e '*.o\n*.vcd\n$(TOP_LEVEL_ENTITY)\n' > sim/.gitignore)

#############
## Cleanup ##
#############
.PHONY: clean
clean:
	rm -f $(OBJ) $(WORKDIR)/*.cf *.cf *.o *.vcd $(TOP_LEVEL_ENTITY)

###################################
## GHDL Design building commands ##
###################################
# https://ghdl-rad.readthedocs.io/en/latest/using/InvokingGHDL.html
.PHONY: lint analyze elaborate run view
lint:
		$(GHDL) -s $(GHDL_FLAGS) $(SRC)

analyze:   $(OBJ) # analyzes/compiles and creates object file - also a linter
elaborate: $(TOP_LEVEL_ENTITY) # linking
run:  $(TOP_LEVEL_ENTITY).vcd

view: run
	gtkwave $(TOP_LEVEL_ENTITY).vcd

##############################################################
# Dependencies of each object (%.o) as regard to its own %.vhd
##############################################################
$(WORKDIR)/%.o: %.vhd
	$(GHDL) -a $(GHDL_FLAGS) $<

$(WORKDIR)/%.o:: $(SRCDIR)/%.vhd
	$(GHDL) -a $(GHDL_FLAGS) $<

##############################################################
# Top level entity management
##############################################################
$(TOP_LEVEL_ENTITY): $(OBJ)
	$(GHDL) -e $(GHDL_FLAGS) $@

# Associated Waveform generation
%.vcd: $(TOP_LEVEL_ENTITY)
	$(GHDL) -r $(GHDL_FLAGS) $< --vcd=$@ $(GHDL_SIM_OPTS)

##################
##     Debug    ##
##################
.PHONY: help info
help:
	@echo "=> available directives: "
	@grep -EHn '^[a-z0-9_-]+[^:$$]*:' $(MAKEFILE_LIST) | grep -v '=' | awk -F: '{print $$3}' | xargs -n1 echo | sort | uniq | awk '{printf "\033[36m   make %-30s\033[0m\n", $$1}'

info:
	@echo "PROJECT = $(PROJECT)"
	@echo "TOP_LEVEL_ENTITY = $(TOP_LEVEL_ENTITY)"
	@echo "SRC  = $(SRC)"
	@echo "OBJ  = $(OBJ)"
	@echo "GHDL = $(GHDL)"
	@echo "GHDL_FLAGS = $(GHDL_FLAGS)"
	@echo "GHDL_SIM_OPTS = $(GHDL_SIM_OPTS)"
