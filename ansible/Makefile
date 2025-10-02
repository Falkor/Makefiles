####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
# Time-stamp: <Mon 2025-04-21 18:31 svarrette>
#     __  __       _         __ _ _
#    |  \/  | __ _| | _____ / _(_) | ___
#    | |\/| |/ _` | |/ / _ \ |_| | |/ _ \
#    | |  | | (_| |   <  __/  _| | |  __/
#    |_|  |_|\__,_|_|\_\___|_| |_|_|\___|
#
####################################################################################
# Generic Makefile to pilot an Ansible control repository
#
# Initialize this repository with:
#    make setup
#    source venv/bin/activate   # or use direnv and allow '.envrc'
#    make setup-ansible
#    make setup-pre-commit
#
############################## Variables Declarations ##############################
SHELL = /bin/bash

# XDG Base Directory Specification: https://specifications.freedesktop.org/basedir-spec/latest/
XDG_CACHE_HOME  ?= $(HOME)/.cache
XDG_CONFIG_HOME ?= $(HOME)/.config
XDG_DATA_HOME   ?= $(HOME)/.local/share

# Directory layout
SCRIPTS_DIR = scripts
COLLECTION_DIR = collections

VAULT_SCRIPT = $(SCRIPTS_DIR)/vault

##################### Main targets #####################
TARGETS = setup
SETUP_TARGETS = setup-xdg setup-python
CLEAN_TARGETS = clean-pre-commit clean-ansible clean-venv

############################### Now starting rules ################################
.PHONY: all
all: $(TARGETS)

### Setup directives
.PHONY: setup
setup: $(SETUP_TARGETS)

# assuming direnv based setup
setup-all: setup-xdg setup-python setup-ansible setup-pre-commit

### XDG Base Directory Specification:
# see https://specifications.freedesktop.org/basedir-spec/latest/
.PHONY: setup-xdg
setup-xdg:
	@echo "=> setup XDG Base Directories"
	mkdir -p $(XDG_CONFIG_HOME)
	mkdir -p $(XDG_DATA_HOME)
	mkdir -p $(XDG_CACHE_HOME)

### Python venv
ifneq ("$(wildcard .Makefile.venv)","")
include .Makefile.venv
setup-python: setup-python-deps
endif

### pre-commmit stuff
# see https://pre-commit.com/
.PHONY: setup-pre-commit
setup-pre-commit: setup-python
	pre-commit install

.PHONY: clean-pre-commit
clean-pre-commit:
	-pre-commit uninstall

### ansible collection
.PHONY: setup-collection setup-ansible-galaxy-collection
setup-collection setup-ansible-galaxy-collection:
	ansible-galaxy collection install --upgrade -r $(COLLECTION_DIR)/requirements.yml

collection-verify:
	ansible-galaxy collection verify -r $(COLLECTION_DIR)/requirements.yml

collection-list:
	ansible-galaxy collection list

.PHONY: clean-collection clean-ansible-galaxy-collection
clean-collection clean-ansible-galaxy-collection:
	-rm -rf ./$(COLLECTION_DIR)/ansible_collections

### ansible
.PHONY: setup-ansible clean-ansible
setup-ansible: setup-python setup-ansible-galaxy-collection

clean-ansible: clean-ansible-galaxy-collection clean-venv

### linter
.PHONY: lint lint-bash lint-ansible lint-ansible-playbooks
lint: lint-bash lint-ansible
lint-bash:
	shellcheck $(shell find $(SCRIPTS_DIR) -type f)
lint-ansible: lint-ansible-playbooks
lint-ansible-playbooks:
	ansible-lint playbook.yml

### vault
.PHONY: vault-list
vault-list:
	$(VAULT_SCRIPT) -l


### Cleanup directives
clean: $(CLEAN_TARGETS)
