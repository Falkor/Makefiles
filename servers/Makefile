##################################################################################
# Makefile - Configuration file for GNU make (http://www.gnu.org/software/make/)
# Time-stamp: <Tue 2017-04-04 10:22 svarrette>
#
# Copyright (c) 2015 Sebastien Varrette <Sebastien.Varrette@uni.lu>
#
############################## Variables Declarations ############################
SHELL = /bin/bash

SRC_TO_FETCH = $(shell find etc var -type f 2>/dev/null)
SERVER       = $(shell basename `pwd`)
GIT_CRYPT    = $(shell which git-crypt)

# List of components to synchronize upon initialization
INIT_LIST = '/etc/sysconfig/network-scripts/ifcfg-[eb]*[^\.bak]' \
						'/etc/fstab' \
            '/etc/motd'

ifeq ($(SRC_TO_FETCH),)
# List of components to synchronize upon initialization
SRC_TO_FETCH = $(INIT_LIST)
endif

# template for fetch rules
define FETCH_template
.PHONY: $T
$T:
	@for f in $($T_SRC); do \
		echo "=> Fetching $$$$f"; \
		rsync -avR $(SERVER):/$$$$f .; \
	done
endef
FETCH_TARGETS = __fetch __fetch-init
__fetch_SRC       = $(SRC_TO_FETCH)
__fetch-init_SRC  = $(INIT_LIST)

.PHONY: all info unlock lock init fetch commit
all: info

info:
	@echo "SRC_TO_FETCH  = $(SRC_TO_FETCH)"
	@echo "SERVER        = $(SERVER)"

ifeq ($(GIT_CRYPT),)
unlock lock:
	@echo "Unable to find 'git-crypt' on your system. Install it following instructions"
	@echo "https://github.com/AGWA/git-crypt"
else
#_____
unlock:
	@echo "=> unlock git-crypt for this repository"
	$(GIT_CRYPT) unlock
lock:
	@echo "=> locking back git-crypt for this repository"
	$(GIT_CRYPT) lock
endif

$(foreach T,$(FETCH_TARGETS),$(eval $(FETCH_template)))

init: __fetch-init
update fetch: __fetch
commit:
	@echo "==> commit changes made for the server '$(SERVER)'"
	-git commit -q -s -m "[$(SERVER)] update system files " .


# fetch:
# 	@for f in $(SRC_TO_FETCH); do \
# 		echo "=> Fetching $$f"; \
# 		rsync -avzu --relative $(SERVER):/$$f .; \
# 	done
