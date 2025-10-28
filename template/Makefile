# Time-stamp: <Tue 2025-10-28 08:52 svarrette>
####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
#                     __  __       _         __ _ _
#                    |  \/  | __ _| | _____ / _(_) | ___
#                    | |\/| |/ _` | |/ / _ \ |_| | |/ _ \
#                    | |  | | (_| |   <  __/  _| | |  __/
#                    |_|  |_|\__,_|_|\_\___|_| |_|_|\___|
#
####################################################################################
# Resources: https://github.com/Falkor/Makefiles
# In particular, to collect .Makefile.venv:
#    wget https://raw.githubusercontent.com/Falkor/Makefiles/refs/heads/devel/venv/.Makefile.venv
# or to pilot git[flow] operations:
#    wget https://raw.githubusercontent.com/Falkor/Makefiles/refs/heads/devel/gitflow/.Makefile.git
#
SHELL = /bin/bash

# Check commands needed for this Makefile - complete as needed (space separated)
EXECUTABLES = curl
$(foreach exec,$(EXECUTABLES),\
  $(if $(shell command -v $(exec) 2>/dev/null),,$(error "$(exec) not available in PATH")))

ROOT_DIR:=$(shell dirname $(realpath $(firstword $(MAKEFILE_LIST))))

# XDG Base Directory Specification: https://specifications.freedesktop.org/basedir-spec/
XDG_CACHE_HOME  ?= $(HOME)/.cache
XDG_CONFIG_HOME ?= $(HOME)/.config
XDG_DATA_HOME   ?= $(HOME)/.local/share

##################### Main targets #####################
TARGETS = setup
SETUP_TARGETS = setup-xdg
CLEAN_TARGETS =
DISTCLEAN_TARGETS = clean-venv
INFO_TARGETS =

# local hook (before) for 'easy' customization. Ex:
# SETUP_TARGETS += setup-python
# all: $(TARGETS)
ifneq ("$(wildcard .Makefile.local)","")
include .Makefile.local
endif

############### Let's go ##############
.PHONY: all setup clean distclean info
all: $(TARGETS)
setup: $(SETUP_TARGETS)
clean: $(CLEAN_TARGETS)
distclean: $(DISTCLEAN_TARGETS)
info: $(INFO_TARGETS)

### Specific setup; XDG, python venv
.PHONY: setup-xdg setup-python
setup-xdg:
	mkdir -p $(XDG_CONFIG_HOME)
	mkdir -p $(XDG_DATA_HOME)
	mkdir -p $(XDG_CACHE_HOME)

ifneq ("$(wildcard .Makefile.venv)","")
include .Makefile.venv
setup-python: setup-python-deps
endif



# local hook (after) to add complementary rules
# extending *_TARGETS variables here won't have the expected effect (use .Makefile.local instead)
ifneq ("$(wildcard .Makefile.after)","")
include .Makefile.after
endif
