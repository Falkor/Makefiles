##################################################################################
# Makefile - Configuration file for GNU make (http://www.gnu.org/software/make/)
# Time-stamp: <Fri 2026-07-17 16:00 svarrette>
##################################################################################
# Copyright (c) 2023-2026 Sebastien Varrette
# Latest version of this Makefile: see
#      https://github.com/Falkor/Makefiles/tree/devel/ISOs
#
# Consider https://github.com/Mexit/MultiOS-USB to easily create a minimal
# bootable USB stick featuring all your downloaded ISOs (under MultiOS-USB/ISOs/)
#    git clone https://github.com/Mexit/MultiOS-USB.git
#    cd MultiOS-USB
#    ./multios-usb.sh -l # Detect USB devices
#    sudo ./multios-usb.sh -f ext4 /dev/sdX   # install MultiOS-USB, /!\ ADAPT device sbX
#    # mount the MultiOS-USB partition
#    sudo cp <linux-distrib>.iso /path/to/MultiOS-USB/ISOs/
#    sync
#    # You may need to update the debian profile to support netinst
#    # see MultiOS-USB/config/debian* (check iso)
#    sudo umount /mnt/multiOS-USB
#

# Available Commands
# ------------------
# make           List supported OS
# make <os>      Download and check integrity and signature of <os> ISO
# make clean     Remove checksum files (keep downloaded ISO)
# make distclean Remove all downloaded files (incl. ISO)
#
############################## Variables Declarations ############################
SHELL = /bin/bash

# Check mandatory commands needed for this Makefile
EXECUTABLES = curl
$(foreach exec,$(EXECUTABLES),\
	$(if $(shell command -v $(exec) 2>/dev/null),,$(error "$(exec) not available in PATH	")))

############
# List of supported OS in this Makefile (corresponding to directive names)
SUPPORTED_OS = debian ubuntu ubuntu-server archlinux fedora fedora-silverblue rockylinux almalinux archlinux pfsense

### OS ISOs characteristics to download and check
# You normally only need to update the <OS>_VERSION variable to the latest version
DEBIAN_VERSION = 13.6.0
DEBIAN_ARCH    = amd64
DEBIAN_MIRROR  = 'https://cdimage.debian.org/debian-cd/current/$(DEBIAN_ARCH)/iso-cd/'
DEBIAN_ISO_URL = '$(DEBIAN_MIRROR)/debian-$(DEBIAN_VERSION)-$(DEBIAN_ARCH)-netinst.iso'
DEBIAN_CHECKSUMS      = '$(DEBIAN_MIRROR)/SHA256SUMS'
DEBIAN_CHECKSUMS_SIGN = '$(DEBIAN_MIRROR)/SHA256SUMS.sign'

UBUNTU_VERSION = 26.04
UBUNTU_ARCH    = $(DEBIAN_ARCH)
UBUNTU_MIRROR  = 'https://ubuntu.mirrors.ovh.net/ubuntu-releases/$(UBUNTU_VERSION)'
UBUNTU_ISO_URL = '$(UBUNTU_MIRROR)/ubuntu-$(UBUNTU_VERSION)-desktop-$(UBUNTU_ARCH).iso'
UBUNTU_SERVER_ISO_URL = '$(UBUNTU_MIRROR)/ubuntu-$(UBUNTU_VERSION)-live-server-$(UBUNTU_ARCH).iso'
UBUNTU_CHECKSUMS      = '$(UBUNTU_MIRROR)/SHA256SUMS'
UBUNTU_CHECKSUMS_SIGN = '$(UBUNTU_MIRROR)/SHA256SUMS.gpg'

FEDORA_VERSION = 44-1.7
FEDORA_VERSION_MAJOR	= $(shell echo $(FEDORA_VERSION) | sed "s/^\([0-9]*\)-[0-9\.]*/\1/")
FEDORA_ARCH    = x86_64
FEDORA_MIRROR_BASEURL  = 'https://download.fedoraproject.org/pub/fedora/linux/releases/$(FEDORA_VERSION_MAJOR)/'
FEDORA_MIRROR  = '$(FEDORA_MIRROR_BASEURL)/Workstation/$(FEDORA_ARCH)/iso'
FEDORA_SERVER_MIRROR  = '$(FEDORA_MIRROR_BASEURL)/Server/$(FEDORA_ARCH)/iso'
FEDORA_SILVERBLUE_MIRROR = '$(FEDORA_MIRROR_BASEURL)/Silverblue/$(FEDORA_ARCH)/iso'
FEDORA_ISO_URL = '$(FEDORA_MIRROR)/Fedora-Workstation-Live-$(FEDORA_VERSION).$(FEDORA_ARCH).iso'
FEDORA_SERVER_ISO_URL = '$(FEDORA_SERVER_MIRROR)/Fedora-Server-netinst-$(FEDORA_ARCH)-$(FEDORA_VERSION).iso'
FEDORA_SILVERBLUE_ISO_URL = '$(FEDORA_SILVERBLUE_MIRROR)/Fedora-Silverblue-ostree-$(FEDORA_ARCH)-$(FEDORA_VERSION).iso'
FEDORA_CHECKSUMS  		= '$(FEDORA_MIRROR)/Fedora-Workstation-$(FEDORA_VERSION)-$(FEDORA_ARCH)-CHECKSUM'
FEDORA_CHECKSUMS_SIGN = '$(FEDORA_CHECKSUMS)'
FEDORA_SERVER_CHECKSUMS      = '$(FEDORA_SERVER_MIRROR)/Fedora-Server-$(FEDORA_VERSION)-$(FEDORA_ARCH)-CHECKSUM'
FEDORA_SERVER_CHECKSUMS_SIGN = '$(FEDORA_SERVER_CHECKSUMS)'
FEDORA_SILVERBLUE_CHECKSUMS			 = '$(FEDORA_SILVERBLUE_MIRROR)/Fedora-Silverblue-$(FEDORA_VERSION)-$(FEDORA_ARCH)-CHECKSUM'
FEDORA_SILVERBLUE_CHECKSUMS_SIGN = '$(FEDORA_SILVERBLUE_CHECKSUMS)'

RHEL_VERSION	= 10.2
RHEL_ARCH		  = x86_64
# RHEL_ISO_TYPE	= dvd
# RHEL_MIRROR	  = 'https://developers.redhat.com/content-gateway/file/rhel/'
# RHEL_ISO_URL  = '$(RHEL_MIRROR)/Red_Hat_Enterprise_Linux_$(RHEL_VERSION)/rhel-$(RHEL_VERSION)-$(RHEL_ARCH)-$(RHEL_ISO_TYPE).iso'
# RHEL_CHECKSUMS      = '$(RHEL_MIRROR)/CHECKSUM'
# RHEL_CHECKSUMS_SIGN = '$(RHEL_MIRROR)/CHECKSUM.sig'
# RHEL_GPG_KEY = 'https://raw.githubusercontent.com/rocky-linux/checksums/main/keys/RPM-GPG-KEY-Rocky-$(ROCKYLINUX_VERSION_MAJOR)'

ROCKYLINUX_VERSION  = $(RHEL_VERSION)
ROCKYLINUX_ISO_TYPE = boot
ROCKYLINUX_ARCH     = $(RHEL_ARCH)
ROCKYLINUX_VERSION_MAJOR  = $(shell echo $(ROCKYLINUX_VERSION) | sed "s/^\([0-9]*\).*/\1/")
ROCKYLINUX_MIRROR   = 'https://download.rockylinux.org/pub/rocky/$(ROCKYLINUX_VERSION_MAJOR)/isos/$(ROCKYLINUX_ARCH)'
ROCKYLINUX_ISO_URL  = '$(ROCKYLINUX_MIRROR)/Rocky-$(ROCKYLINUX_VERSION)-$(ROCKYLINUX_ARCH)-$(ROCKYLINUX_ISO_TYPE).iso'
ROCKYLINUX_CHECKSUMS      = '$(ROCKYLINUX_MIRROR)/CHECKSUM'
ROCKYLINUX_CHECKSUMS_SIGN = '$(ROCKYLINUX_CHECKSUMS).asc'
ROCKYLINUX_GPG_KEY = 'https://raw.githubusercontent.com/rocky-linux/checksums/main/keys/RPM-GPG-KEY-Rocky-$(ROCKYLINUX_VERSION_MAJOR)'

ALMALINUX_VERSION   = $(RHEL_VERSION)
ALMALINUX_ISO_TYPE  = $(ROCKYLINUX_ISO_TYPE)
ALMALINUX_ARCH      = $(RHEL_ARCH)
ALMALINUX_MIRROR    = 'https://repo.almalinux.org/almalinux/$(ALMALINUX_VERSION)/isos/$(ALMALINUX_ARCH)'
ALMALINUX_ISO_URL   = '$(ALMALINUX_MIRROR)/AlmaLinux-$(ALMALINUX_VERSION)-$(ALMALINUX_ARCH)-$(ALMALINUX_ISO_TYPE).iso'
ALMALINUX_CHECKSUMS = '$(ALMALINUX_MIRROR)/CHECKSUM'
ALMALINUX_CHECKSUMS_SIGN = '$(ALMALINUX_CHECKSUMS)'
ALMALINUX_VERSION_MAJOR = $(shell echo $(ALMALINUX_VERSION) | sed "s/^\([0-9]*\).*/\1/")
ALMALINUX_GPG_KEY   = 'https://repo.almalinux.org/almalinux/RPM-GPG-KEY-AlmaLinux-$(ALMALINUX_VERSION_MAJOR)'

ARCHLINUX_VERSION = latest
ARCHLINUX_ARCH    = x86_64
ARCHLINUX_MIRROR  = 'https://mirror.cyberbits.eu/archlinux/iso/$(ARCHLINUX_VERSION)'
ARCHLINUX_ISO_URL = '$(ARCHLINUX_MIRROR)/archlinux-$(ARCHLINUX_ARCH).iso'
ARCHLINUX_ISO_SIGN = '$(ARCHLINUX_ISO_URL).sig'
ARCHLINUX_CHECKSUMS = '$(ARCHLINUX_MIRROR)/sha256sums.txt'

# https://www.pfsense.org/download/
PFSENSE_VERSION = 2.7.2
PFSENSE_ARCH		= amd64
PFSENSE_MIRROR	= 'https://atxfiles.netgate.com/mirror/downloads/'
PFSENSE_ISO_URL = '$(PFSENSE_MIRROR)/pfSense-CE-$(PFSENSE_VERSION)-RELEASE-$(PFSENSE_ARCH).iso.gz'
PFSENSE_CHECKSUMS = '$(PFSENSE_ISO_URL).sha256'
# PFSENSE_CHECKSUMS = 'https://www.pfsense.org/hashes/pfSense-CE-$(PFSENSE_VERSION)-RELEASE-$(PFSENSE_ARCH).sha256'

# Where to download the ISOs
ISO_DIR = .

# Makefile custom hooks - typically to seamlessly change the ISO_DIR ;)
MAKEFILE_CUSTOM = .Makefile.custom

#######
# Macro for fetching (make fetch-<name>) with 'curl' into <dir> the <url> file (thus into <dir>/<basename>)
# Format: $(eval $(call __FETCH,<name>,<dir>,<url>))
##
define __FETCH
.PHONY: __fetch-$1 __clean-$1
__fetch-$1:
	@if [ ! -d "$2" ]; then \
    echo "ERROR: non existing target directory '$2'"; \
		exit 1; \
	fi
	@echo "=> fetching $2/$$(shell basename $3)"
	@if [ ! -f "$2/$$(shell basename $3)" ]; then \
		curl -L -o $2/$$(shell basename $3) $3; \
	else \
		echo "   /!\\ $2/$$(shell basename $3)  has already been downloaded"; \
	fi

__clean-$1:
	-rm -f $2/$$(shell basename $3)

endef

#######
# Macro for verifying a GPG signature of a file from a detached signature
# Format: $(eval $(call __GPG_VERIFY_DETACHED_SIGN,<name>,<dir>,<fileurl>,<sigurl>))
##
define __GPG_VERIFY_DETACHED_SIGN
__gpg_verify-$1: __gpg_verify_detached_sign-$1

.PHONY: __gpg_verify_detached_sign-$1
__gpg_verify_detached_sign-$1:
	@echo "=> verify GPG signature for $$(shell basename $3) (using signature file $$(shell basename $4))"
	gpg --verify $2/$$(shell basename $4) $2/$$(shell basename $3)


endef

#######
# Macro for verifying a GPG signature of a file (embedded signature)
# Format: $(eval $(call __GPG_VERIFY_SIGN,<name>,<dir>,<fileurl>))
##
define __GPG_VERIFY_SIGN
__gpg_verify-$1: __gpg_verify_sign-$1

.PHONY: __gpg_verify_sign-$1
__gpg_verify_sign-$1:
	@echo "=> verify GPG signature for '$2/$$(shell basename $3)'"
	gpg --verify $2/$$(shell basename $3)

endef


#######
# Macro for verifying a ISO SHA256 checksum from a checksum file and create a convenient symlink
# Format: $(eval $(call __VERIFY_ISO_CHECKSUM,<name>,<dir>,<url>,<checksum_url>))
##
define __VERIFY_ISO_CHECKSUM
__check-iso-$1: __fetch-iso-$1  __fetch-iso-$1-checksum
	@echo "=> check ISO checksum: $$(shell basename $3)"
	cd $2 && sha256sum -c $$(shell basename $4) --ignore-missing
	@echo "=> cleanup checksum files"
	$(MAKE) __clean-iso-$1-checksum
	@echo "=> create convenient symlink"
	ln -sf $$(shell basename $3) $2/$1.iso
endef


#######
# Macro for verifying the checksum of a gzipped iso, uncompress it and create a convenient symlink
# Assumes the checksum file (and only it) is present (as for pfSense)
# Format: $(eval $(call __VERIFY_CHECKSUM_GZIPPED_ISO,<name>,<dir>,<url>,<checksum_url>))
##
define __VERIFY_CHECKSUM_GZIPPED_ISO
__check-iso-$1:  __fetch-iso-$1  __fetch-iso-$1-checksum
	@echo "=> check ISO checksum: $$(shell basename $3)"
	cd $$(ISO_DIR) && sha256sum -c $$(shell basename $4) --ignore-missing
	@echo "=> uncompress $$(shell basename $3)"
	gunzip --keep $$(ISO_DIR)/$$(shell basename $3)
	@echo "=> cleanup checksum files"
	$(MAKE) __clean-iso-$1-checksum
	@echo "=> create convenient symlink to the iso"
	ln -sf $$(shell basename $3 '.gz') $2/$1.iso

.PHONY: __clean-gunzipped-iso-$1
__clean-gunzipped-iso-$1:
	-rm -f $$(ISO_DIR)/$$(shell basename $3 .gz)

endef


##################### Main targets #####################
TARGETS = list
INFO_TARGETS  = info-iso
CLEAN_TARGETS = clean-checksums
DISTCLEAN_TARGETS = $(SUPPORTED_OS:%=clean-%)

# local hook (before) for 'easy' customization. Typical content exemple:
#    ISO_DIR = /path/to/custom/dir
#    all: $(TARGETS)
#
ifneq (,$(wildcard $(MAKEFILE_CUSTOM)))
include $(MAKEFILE_CUSTOM)
endif

################ Let's go ################
.PHONY: all all-os
all: $(TARGETS)
all-os: $(SUPPORTED_OS)

### general help: list all available make directives
.PHONY: help
help:
	@echo "=> available directives: "
	@grep -EHn '^[a-z0-9_-]+[^:$$]*:' $(MAKEFILE_LIST) | grep -v '=' | awk -F: '{print $$3}' | xargs -n1 echo | sort | uniq | awk '{printf "\033[36m   make %-30s\033[0m\n", $$1}'

### Info/Debug
.PHONY: info info-iso list
info:  $(INFO_TARGETS)

info-iso:
	@echo "TARGETS= $(TARGETS)"
	@echo "CLEAN_TARGETS = $(CLEAN_TARGETS)"
	@echo "DISTCLEAN_TARGETS = $(DISTCLEAN_TARGETS)"
	@echo "============================================================================================================"
	@echo "Use make <os> to fetch and verify an ISO for <os>, and create a convenient symlink <os>.iso if all goes well"
	@echo "where <os> in: $(SUPPORTED_OS)"
	@echo "Run 'make list' to get detailed information on supported OS"
	@echo "============================================================================================================"
	$(MAKE) list

list:
	@echo "=> list of supported OS <os>:"
	@echo "                <os>:           Distrib   Version   Arch  URL "
	@echo "--------------------- ------------------ -------- ------ ---------"
	@printf "%20s: %18s %8s %6s %s\n" "debian"		 "Debian"			$(DEBIAN_VERSION) $(DEBIAN_ARCH) $(DEBIAN_ISO_URL)
	@printf "%20s: %18s %8s %6s %s\n"	"ubuntu" "Ubuntu *"	$(UBUNTU_VERSION) $(UBUNTU_ARCH)
	@printf "%20s: %18s %8s %6s %s\n"	"ubuntu-desktop" "Ubuntu Desktop" $(UBUNTU_VERSION) $(UBUNTU_ARCH) $(UBUNTU_ISO_URL)
	@printf "%20s: %18s %8s %6s %s\n"	"ubuntu-server" "Ubuntu Server"		$(UBUNTU_VERSION) $(UBUNTU_ARCH) $(UBUNTU_SERVER_ISO_URL)
	@printf "%20s: %18s %8s %6s %s\n"	"fedora"		 "Fedora *"			$(FEDORA_VERSION) $(FEDORA_ARCH)
	@printf "%20s: %18s %8s %6s %s\n"	"fedora-workstation" "Fedora Workstation"	$(FEDORA_VERSION) $(FEDORA_ARCH) $(FEDORA_ISO_URL)
	@printf "%20s: %18s %8s %6s %s\n"	"fedora-silverblue"	 "Fedora Silverblue"	$(FEDORA_VERSION) $(FEDORA_ARCH) $(FEDORA_SILVERBLUE_ISO_URL)
	@printf "%20s: %18s %8s %6s %s\n"	"fedora-server"	 "Fedora Server"	$(FEDORA_VERSION) $(FEDORA_ARCH) $(FEDORA_SERVER_ISO_URL)
	@printf "%20s: %18s %8s %6s %s\n"	"rockylinux" "RockyLinux" $(ROCKYLINUX_VERSION) $(ROCKYLINUX_ARCH) $(ROCKYLINUX_ISO_URL)
	@printf "%20s: %18s %8s %6s %s\n"	"archlinux"	 "ArchLinux"	$(ARCHLINUX_VERSION) $(ARCHLINUX_ARCH) $(ARCHLINUX_ISO_URL)
	@printf "%20s: %18s %8s %6s %s\n"	"pfsense"		 "pfSense"		$(PFSENSE_VERSION) $(PFSENSE_ARCH) $(PFSENSE_ISO_URL)
	@echo "------------------------------------------------"
	@echo "Use make <os> to:"
	@echo "  1. fetch ISO and the associated checksum files and eventual signatures"
	@echo "  2. fetch the eventual official GPG keys required to verify the dowloaded files"
	@echo "  3. verify the digital signature of the downloaded files"
	@echo "  4. verify the ISO checksum"
	@echo "  5. create a convenient symlink <os>.iso if all of the above went well"
	@echo
	@echo " In particular, if you don't end with a symlink <os>.iso, don't waste time to prepare a USB stick of use the present ISO"
	@echo " the downloaded file is probably corrupted."

###############
### Cleanup ###
###############
.PHONY: clean distclean
clean: $(CLEAN_TARGETS)
	@echo "=> 'make distclean' to remove all ISOs"
distclean: $(DISTCLEAN_TARGETS)

clean-checksums: $(SUPPORTED_OS:%=clean-%-checksums)

###############################################
### General fetch and check ISOs directives ###
###############################################
.PHONY: fetch check
fetch: $(SUPPORTED_OS:%=fetch-%-iso)
check: $(SUPPORTED_OS:%=check-%-iso)

##############################
### Specific OS Management ###
##############################
# Normally you won't have to touch anything below
# To support/add a new OS, kindly respect the used structure
# To update the latest version, correcting the <OS>_VERSION variable should be sufficient

##############################
##########  Debian ###########
##############################
debian: fetch-debian-iso check-debian-iso

fetch-debian-iso: __fetch-iso-debian __fetch-iso-debian-checksum	__fetch-iso-debian-checksum-sign fetch-debian-gpg-keys
$(eval $(call __FETCH,iso-debian,$(ISO_DIR),$(DEBIAN_ISO_URL)))
$(eval $(call __FETCH,iso-debian-checksum,$(ISO_DIR),$(DEBIAN_CHECKSUMS)))
$(eval $(call __FETCH,iso-debian-checksum-sign,$(ISO_DIR),$(DEBIAN_CHECKSUMS_SIGN)))

fetch-debian-gpg-keys:
	@echo "=> collect the Debian GPG keys listed on https://www.debian.org/CD/verify"
	gpg --keyserver keyring.debian.org --recv-keys 0x988021A964E6EA7D
	gpg --keyserver keyring.debian.org --recv-keys 0xDA87E80D6294BE9B
	gpg --keyserver keyring.debian.org --recv-keys 0x42468F4009EA8AC3

check-debian-iso: fetch-debian-iso __gpg_verify_detached_sign-iso-debian-checksum __check-iso-debian clean-debian-checksums

$(eval $(call __GPG_VERIFY_DETACHED_SIGN,iso-debian-checksum,$(ISO_DIR),$(DEBIAN_CHECKSUMS),$(DEBIAN_CHECKSUMS_SIGN)))
$(eval $(call __VERIFY_ISO_CHECKSUM,debian,$(ISO_DIR),$(DEBIAN_ISO_URL),$(DEBIAN_CHECKSUMS)))

clean-debian-checksums: __clean-iso-debian-checksum __clean-iso-debian-checksum-sign
clean-debian: clean-debian-checksums __clean-iso-debian
	-rm -f $(ISO_DIR)/debian.iso

#################################################
########## Ubuntu (Desktop and Server)###########
#################################################
ubuntu: fetch-ubuntu-iso check-ubuntu-iso
ubuntu-desktop: fetch-ubuntu-desktop-iso check-ubuntu-desktop-iso  clean-ubuntu-desktop-checksums
ubuntu-server:  fetch-ubuntu-server-iso  check-ubuntu-server-iso   clean-ubuntu-server-checksums

fetch-ubuntu-iso: fetch-ubuntu-desktop-iso __fetch-iso-ubuntu-server
fetch-ubuntu-desktop-iso: __fetch-iso-ubuntu       __fetch-iso-ubuntu-checksum  __fetch-iso-ubuntu-sign fetch-ubuntu-gpg-keys
fetch-ubuntu-server-iso: __fetch-iso-ubuntu-server __fetch-iso-ubuntu-checksum  __fetch-iso-ubuntu-sign fetch-ubuntu-gpg-keys

$(eval $(call __FETCH,iso-ubuntu,$(ISO_DIR),$(UBUNTU_ISO_URL)))
$(eval $(call __FETCH,iso-ubuntu-checksum,$(ISO_DIR),$(UBUNTU_CHECKSUMS)))
$(eval $(call __FETCH,iso-ubuntu-sign,$(ISO_DIR),$(UBUNTU_CHECKSUMS_SIGN)))

$(eval $(call __FETCH,iso-ubuntu-server,$(ISO_DIR),$(UBUNTU_SERVER_ISO_URL)))
$(eval $(call __FETCH,iso-ubuntu-server-checksum,$(ISO_DIR),$(UBUNTU_CHECKSUMS)))

fetch-ubuntu-gpg-keys:
	@echo "=> collect the Ubuntu GPG keys listed on https://ubuntu.com/tutorials/how-to-verify-ubuntu"
	gpg --keyid-format long --keyserver keyserver.ubuntu.com --recv-keys 0x46181433FBB75451
	gpg --keyid-format long --keyserver keyserver.ubuntu.com --recv-keys 0xD94AA3F0EFE21092

check-ubuntu-iso: fetch-ubuntu-desktop-iso fetch-ubuntu-server-iso __gpg_verify_detached_sign-iso-ubuntu-checksum  __check-iso-ubuntu __check-iso-ubuntu-server clean-ubuntu-server-checksums
check-ubuntu-desktop-iso: fetch-ubuntu-desktop-iso __gpg_verify_detached_sign-iso-ubuntu-checksum __check-iso-ubuntu  clean-ubuntu-server-checksums
check-ubuntu-server-iso:  fetch-ubuntu-server-iso  __gpg_verify_detached_sign-iso-ubuntu-server-checksum __check-iso-ubuntu-server clean-ubuntu-checksums

$(eval $(call __GPG_VERIFY_DETACHED_SIGN,iso-ubuntu-checksum,$(ISO_DIR),$(UBUNTU_CHECKSUMS),$(UBUNTU_CHECKSUMS_SIGN)))
$(eval $(call __GPG_VERIFY_DETACHED_SIGN,iso-ubuntu-server-checksum,$(ISO_DIR),$(UBUNTU_CHECKSUMS),$(UBUNTU_CHECKSUMS_SIGN)))
$(eval $(call __VERIFY_ISO_CHECKSUM,ubuntu,$(ISO_DIR),$(UBUNTU_ISO_URL),$(UBUNTU_CHECKSUMS)))
$(eval $(call __VERIFY_ISO_CHECKSUM,ubuntu-server,$(ISO_DIR),$(UBUNTU_SERVER_ISO_URL),$(UBUNTU_CHECKSUMS)))

clean-ubuntu-checksums clean-ubuntu-desktop-checksums clean-ubuntu-server-checksums:  __clean-iso-ubuntu-checksum __clean-iso-ubuntu-sign
clean-ubuntu: clean-ubuntu-checksums __clean-iso-ubuntu __clean-iso-ubuntu-server
	-rm -f $(ISO_DIR)/ubuntu.iso
	-rm -f $(ISO_DIR)/ubuntu-server.iso

######################################################
########## Fedora (Workstation and Server) ###########
######################################################
fedora: fetch-fedora-iso check-fedora-iso

fetch-fedora-iso: fetch-fedora-workstation-iso fetch-fedora-server-iso fetch-fedora-silverblue-iso
fetch-fedora-workstation-iso: __fetch-iso-fedora           __fetch-iso-fedora-checksum            fetch-fedora-gpg-keys
fetch-fedora-server-iso: __fetch-iso-fedora-server         __fetch-iso-fedora-server-checksum     fetch-fedora-gpg-keys
fetch-fedora-silverblue-iso: __fetch-iso-fedora-silverblue __fetch-iso-fedora-silverblue-checksum fetch-fedora-gpg-keys

$(eval $(call __FETCH,iso-fedora,$(ISO_DIR),$(FEDORA_ISO_URL)))
$(eval $(call __FETCH,iso-fedora-checksum,$(ISO_DIR),$(FEDORA_CHECKSUMS)))
$(eval $(call __FETCH,iso-fedora-server,$(ISO_DIR),$(FEDORA_SERVER_ISO_URL)))
$(eval $(call __FETCH,iso-fedora-server-checksum,$(ISO_DIR),$(FEDORA_SERVER_CHECKSUMS)))
$(eval $(call __FETCH,iso-fedora-silverblue,$(ISO_DIR),$(FEDORA_SILVERBLUE_ISO_URL)))
$(eval $(call __FETCH,iso-fedora-silverblue-checksum,$(ISO_DIR),$(FEDORA_SILVERBLUE_CHECKSUMS)))

fetch-fedora-gpg-keys:
	@echo "=> collect the Fedora GPG keys"
	curl -O https://fedoraproject.org/fedora.gpg
	-gpg --import fedora.gpg
	-rm -f fedora.gpg

check-fedora-iso: check-fedora-workstation-iso check-fedora-server-iso check-fedora-silverblue-iso
check-fedora-workstation-iso: fetch-fedora-workstation-iso __gpg_verify_sign-iso-fedora-checksum __check-iso-fedora clean-fedora-workstation-checksums
check-fedora-server-iso: fetch-fedora-server-iso __gpg_verify_sign-iso-fedora-server-checksum __check-iso-fedora-server clean-fedora-server-checksums
check-fedora-silverblue-iso: fetch-fedora-silverblue-iso __gpg_verify_sign-iso-fedora-silverblue-checksum __check-iso-fedora-silverblue clean-fedora-silverblue-checksums

$(eval $(call __GPG_VERIFY_SIGN,iso-fedora-checksum,$(ISO_DIR),$(FEDORA_CHECKSUMS)))
$(eval $(call __GPG_VERIFY_SIGN,iso-fedora-server-checksum,$(ISO_DIR),$(FEDORA_SERVER_CHECKSUMS)))
$(eval $(call __GPG_VERIFY_SIGN,iso-fedora-silverblue-checksum,$(ISO_DIR),$(FEDORA_SILVERBLUE_CHECKSUMS)))
$(eval $(call __VERIFY_ISO_CHECKSUM,fedora,$(ISO_DIR),$(FEDORA_ISO_URL),$(FEDORA_CHECKSUMS)))
$(eval $(call __VERIFY_ISO_CHECKSUM,fedora-server,$(ISO_DIR),$(FEDORA_SERVER_ISO_URL),$(FEDORA_SERVER_CHECKSUMS)))
$(eval $(call __VERIFY_ISO_CHECKSUM,fedora-silverblue,$(ISO_DIR),$(FEDORA_SILVERBLUE_ISO_URL),$(FEDORA_SILVERBLUE_CHECKSUMS)))

clean-fedora-checksums: clean-fedora-workstation-checksums clean-fedora-server-checksums clean-fedora-silverblue-checksums
clean-fedora-workstation-checksums:  __clean-iso-fedora-checksum
clean-fedora-server-checksums:       __clean-iso-fedora-server-checksum
clean-fedora-silverblue-checksums:   __clean-iso-fedora-silverblue-checksum

clean-fedora: clean-fedora-checksums __clean-iso-fedora __clean-iso-fedora-server __clean-iso-fedora-silverblue
	-rm -f $(ISO_DIR)/fedora.iso
	-rm -f $(ISO_DIR)/fedora-server.iso
	-rm -f $(ISO_DIR)/fedora-silverblue.iso


##################################
########## Rocky Linux ###########
##################################
rockylinux: fetch-rockylinux-iso check-rockylinux-iso

fetch-rockylinux-iso: __fetch-iso-rockylinux __fetch-iso-rockylinux-checksum __fetch-iso-rockylinux-checksum-sign

$(eval $(call __FETCH,iso-rockylinux,$(ISO_DIR),$(ROCKYLINUX_ISO_URL)))
$(eval $(call __FETCH,iso-rockylinux-checksum,$(ISO_DIR),$(ROCKYLINUX_CHECKSUMS)))
$(eval $(call __FETCH,iso-rockylinux-checksum-sign,$(ISO_DIR),$(ROCKYLINUX_CHECKSUMS_SIGN)))

fetch-rockylinux-gpg-keys:
	@echo "=> collect the RockyLinux GPG keys (Release Engineering (Rocky Linux 10))"
	gpg --keyid-format long --keyserver keyserver.ubuntu.com --recv-keys FC226859C0860BF0DDB95B085B106C736FEDFC85

check-rockylinux-iso: fetch-rockylinux-iso __gpg_verify_detached_sign-iso-rockylinux-checksum __check-iso-rockylinux clean-rockylinux-checksums
$(eval $(call __GPG_VERIFY_DETACHED_SIGN,iso-rockylinux-checksum,$(ISO_DIR),$(ROCKYLINUX_CHECKSUMS),$(ROCKYLINUX_CHECKSUMS_SIGN)))
$(eval $(call __VERIFY_ISO_CHECKSUM,rockylinux,$(ISO_DIR),$(ROCKYLINUX_ISO_URL),$(ROCKYLINUX_CHECKSUMS)))

clean-rockylinux-checksums: __clean-iso-rockylinux-checksum __clean-iso-rockylinux-checksum-sign
clean-rockylinux: clean-rockylinux-checksums __clean-iso-rockylinux
	-rm -f $(ISO_DIR)/rockylinux.iso

################################
########## AlmaLinux ###########
################################
almalinux: fetch-almalinux-iso check-almalinux-iso

fetch-almalinux-iso: __fetch-iso-almalinux __fetch-iso-almalinux-checksum fetch-almalinux-gpg-keys

$(eval $(call __FETCH,iso-almalinux,$(ISO_DIR),$(ALMALINUX_ISO_URL)))
$(eval $(call __FETCH,iso-almalinux-checksum,$(ISO_DIR),$(ALMALINUX_CHECKSUMS)))
$(eval $(call __FETCH,almalinux-gpg-key,$(ISO_DIR),$(ALMALINUX_GPG_KEY)))

fetch-almalinux-gpg-keys: __fetch-almalinux-gpg-key
	@echo "=> import the Almalinux GPG keys"
	gpg --import $(ISO_DIR)/$(shell basename $(ALMALINUX_GPG_KEY))
	$(MAKE) __clean-almalinux-gpg-key

check-almalinux-iso: fetch-almalinux-iso fetch-almalinux-gpg-keys __gpg_verify_sign-iso-almalinux-checksum __check-iso-almalinux clean-almalinux-checksums
$(eval $(call __GPG_VERIFY_SIGN,iso-almalinux-checksum,$(ISO_DIR),$(ALMALINUX_CHECKSUMS)))
$(eval $(call __VERIFY_ISO_CHECKSUM,almalinux,$(ISO_DIR),$(ALMALINUX_ISO_URL),$(ALMALINUX_CHECKSUMS)))

clean-almalinux-checksums: __clean-iso-almalinux-checksum
clean-almalinux: clean-almalinux-checksums __clean-iso-almalinux
	-rm -f $(ISO_DIR)/almalinux.iso


#################################
########## Arch Linux ###########
#################################
archlinux: fetch-archlinux-iso check-archlinux-iso

fetch-archlinux-iso: __fetch-iso-archlinux __fetch-iso-archlinux-checksum	__fetch-iso-archlinux-sign

$(eval $(call __FETCH,iso-archlinux,$(ISO_DIR),$(ARCHLINUX_ISO_URL)))
$(eval $(call __FETCH,iso-archlinux-checksum,$(ISO_DIR),$(ARCHLINUX_CHECKSUMS)))
$(eval $(call __FETCH,iso-archlinux-sign,$(ISO_DIR),$(ARCHLINUX_ISO_SIGN)))

fetch-archlinux-gpg-keys:
	@echo "=> collect the Archlinux GPG keys - see https://archlinux.org/download/#checksums"
	gpg --keyid-format long --keyserver keyserver.ubuntu.com --recv-keys 0x3E80CA1A8B89F69CBA57D98A76A5EF9054449A5C

check-archlinux-iso: fetch-archlinux-iso  __gpg_verify_detached_sign-iso-archlinux __check-iso-archlinux clean-archlinux-checksums
$(eval $(call __GPG_VERIFY_DETACHED_SIGN,iso-archlinux,$(ISO_DIR),$(ARCHLINUX_ISO_URL),$(ARCHLINUX_ISO_SIGN)))
$(eval $(call __VERIFY_ISO_CHECKSUM,archlinux,$(ISO_DIR),$(ARCHLINUX_ISO_URL),$(ARCHLINUX_CHECKSUMS)))

clean-archlinux-checksums: __clean-iso-archlinux-checksum __clean-iso-archlinux-sign
clean-archlinux: clean-archlinux-checksums __clean-iso-archlinux
	-rm -f $(ISO_DIR)/archlinux.iso


########## pfSense ###########
pfsense: fetch-pfsense check-pfsense-iso

fetch-pfsense: fetch-pfsense-iso fetch-pfsense-iso-checksum
fetch-pfsense-iso: __fetch-iso-pfsense
fetch-pfsense-iso-checksum: __fetch-iso-pfsense-checksum

$(eval $(call __FETCH,iso-pfsense,$(ISO_DIR),$(PFSENSE_ISO_URL)))
$(eval $(call __FETCH,iso-pfsense-checksum,$(ISO_DIR),$(PFSENSE_CHECKSUMS)))

check-pfsense-iso: fetch-pfsense __check-iso-pfsense
$(eval $(call __VERIFY_CHECKSUM_GZIPPED_ISO,pfsense,$(ISO_DIR),$(PFSENSE_ISO_URL),$(PFSENSE_CHECKSUMS)))

clean-pfsense-checksums:  __clean-iso-pfsense-checksum
clean-pfsense: clean-pfsense-checksums __clean-iso-pfsense __clean-gunzipped-iso-pfsense
	-rm -f $(ISO_DIR)/pfsense.iso
