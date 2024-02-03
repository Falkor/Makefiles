####################################################################################
# Makefile (configuration file for GNU make - see http://www.gnu.org/software/make/)
# Time-stamp: <Fri 2023-09-01 09:29 svarrette>
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

MERMAIDFILES = $(wildcard *.mmd)
PDF = $(MERMAIDFILES:%.mmd=%.pdf)
PNG = $(MERMAIDFILES:%.mmd=%.png)

### mermaid-cli is a command-line interface (CLI) for mermaid.
# It takes a mermaid definition file as input and generates an svg/png/pdf file as output.
# See https://github.com/mermaid-js/mermaid-cli
#
# As it's a node.js program (typically installed via 'npm install -g @mermaid-js/mermaid-cli'),
# preferred way to run it is within a podman container in a rootless environment
PODMAN=$(shell which podman)
PODMAN_RUN_OPTS=--runtime crun --userns keep-id --user $(shell id -u) --rm -v $(PWD):/data:z
# Github Container Registry details
GHCR_OWNER = mermaid-js
GHCR_IMAGE = mermaid-cli

# mermaid-cli (mmdc) invocation - CLI explainations:
#  --runtime crun: switch to the alternative OCI runtime 'crun', compliant with
#                  cgroup V2 allowing to run in rootless mode
#  --userns keep-id --user <uid>:  allows the container to keep the same UID as
#                  the current user's UID in the container namespace instead of
#                  mapping to a subuid
#                  See https://docs.podman.io/en/latest/markdown/options/userns.container.html
# The container looks for input files in /data so a volume mapping (-v) is expected.
# Note: ':z' added to the end of the volume mapping to instructs podman to
# relabel the files in the volume with the SELinux label container_file_t, which
# allows processes in the container to access the files.
# see https://docs.podman.io/en/latest/markdown/podman-run.1.html#volume-v-source-volume-host-dir-container-dir-options
MMDC=mermaid-cli
# MMDC=$(shell which mmdc)

TARGETS  = $(PDF)
#################### Let's go ####################
ifeq ($(PODMAN),)
all:
	$(warning Expected to run mermaid-cli using Podman - kindly install it: 'apt install podman')
else

all: $(TARGETS)

endif

info:
	@echo "MERMAIDFILES = $(MERMAIDFILES)"
	@echo "PDF = $(PDF)"
	@echo "PNG = $(PNG)"
	@echo "TARGETS      = $(TARGETS)"

pdf: $(PDF)
png: $(PNG)

%.pdf: %.mmd
	@echo "=> generating $@ from mermaid diagram $<"
	$(PODMAN) run $(PODMAN_RUN_OPTS) ghcr.io/$(GHCR_OWNER)/$(GHCR_IMAGE)/$(MMDC) --pdfFit -i $< -o $@

# %.png: %.mmd
# 	@echo "=> generating $@ (transparent PNG) from mermaid diagram $<"
# 	$(PODMAN) run $(PODMAN_RUN_OPTS) ghcr.io/$(GHCR_OWNER)/$(GHCR_IMAGE)/$(MMDC) -i $< -o $@ -b transparent

# FIXME : use pdftoppm -png instead
# %.png: %.pdf
# 	convert -density 400 -depth 8 -quality 100 $< $@

distclean:
	rm -f $(PDF) $(PNG)

