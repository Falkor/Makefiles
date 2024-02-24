##################################################################################
# Makefile - Configuration file for GNU make (http://www.gnu.org/software/make/)
# Time-stamp: <Fri 2024-01-05 23:55 svarrette>
##################################################################################
# Fetch and check ISOs for various Linux OS in the current directory
#
# Copyright (c) 2023-2024 Sebastien Varrette
#
# Available Commands
# ------------------
# make           List supported OS
# make <os>      Download and check integrity and signature of <os> ISO
# make clean     Remove checksum files (keep downloaded ISO)
# make distclean Remove all downloaded files (incl. ISO)
#
############################## Variables Declarations ############################

# Check mandatory commands needed for this Makefile
EXECUTABLES = curl
$(foreach exec,$(EXECUTABLES),\
	$(if $(shell command -v $(exec) 2>/dev/null),,$(error "$(exec) not available in PATH	")))

### OS ISOs characteristics to download and check
DEBIAN_VERSION = 12.4.0
DEBIAN_ARCH    = amd64
DEBIAN_MIRROR  = 'https://cdimage.debian.org/debian-cd/current/$(DEBIAN_ARCH)/iso-cd/'
DEBIAN_ISO_URL = '$(DEBIAN_MIRROR)/debian-$(DEBIAN_VERSION)-$(DEBIAN_ARCH)-netinst.iso'
DEBIAN_CHECKSUMS      = '$(DEBIAN_MIRROR)/SHA256SUMS'
DEBIAN_CHECKSUMS_SIGN = '$(DEBIAN_MIRROR)/SHA256SUMS.sign'

UBUNTU_VERSION = 22.04.3
UBUNTU_ARCH    = $(DEBIAN_ARCH)
UBUNTU_MIRROR  = 'https://www-ftp.lip6.fr/pub/linux/distributions/Ubuntu/releases/$(UBUNTU_VERSION)'
UBUNTU_ISO_URL = '$(UBUNTU_MIRROR)/ubuntu-$(UBUNTU_VERSION)-desktop-$(UBUNTU_ARCH).iso'
UBUNTU_CHECKSUMS      = '$(UBUNTU_MIRROR)/SHA256SUMS'
UBUNTU_CHECKSUMS_SIGN = '$(UBUNTU_MIRROR)/SHA256SUMS.gpg'

ROCKYLINUX_VERSION	= 8.9
ROCKYLINUX_ISO_TYPE	= minimal
ROCKYLINUX_ARCH		  = x86_64
ROCKYLINUX_VERSION_MAJOR	= $(shell echo $(ROCKYLINUX_VERSION) | sed "s/^\([0-9]*\).*/\1/")
ROCKYLINUX_MIRROR	  = 'https://download.rockylinux.org/pub/rocky/$(ROCKYLINUX_VERSION_MAJOR)/isos/$(ROCKYLINUX_ARCH)'
ROCKYLINUX_ISO_URL  = '$(ROCKYLINUX_MIRROR)/Rocky-$(ROCKYLINUX_VERSION)-$(ROCKYLINUX_ARCH)-$(ROCKYLINUX_ISO_TYPE).iso'
ROCKYLINUX_CHECKSUMS      = '$(ROCKYLINUX_MIRROR)/CHECKSUM'
ROCKYLINUX_CHECKSUMS_SIGN = '$(ROCKYLINUX_MIRROR)/CHECKSUM.sig'
ROCKYLINUX_GPG_KEY = 'https://raw.githubusercontent.com/rocky-linux/checksums/main/keys/RPM-GPG-KEY-Rocky-$(ROCKYLINUX_VERSION_MAJOR)'

ALMALINUX_VERSION		= 9.3
ALMALINUX_ISO_TYPE	= minimal
ALMALINUX_ARCH			= $(ROCKYLINUX_ARCH)
ALMALINUX_MIRROR		= 'https://repo.almalinux.org/almalinux/$(ALMALINUX_VERSION)/isos/$(ALMALINUX_ARCH)'
ALMALINUX_ISO_URL		= '$(ALMALINUX_MIRROR)/AlmaLinux-$(ALMALINUX_VERSION)-$(ALMALINUX_ARCH)-$(ALMALINUX_ISO_TYPE).iso'
ALMALINUX_CHECKSUMS = '$(ALMALINUX_MIRROR)/CHECKSUM'
ALMALINUX_CHECKSUMS_SIGN = '$(ALMALINUX_CHECKSUMS)'
ALMALINUX_VERSION_MAJOR	= $(shell echo $(ALMALINUX_VERSION) | sed "s/^\([0-9]*\).*/\1/")
ALMALINUX_GPG_KEY   = 'https://repo.almalinux.org/almalinux/RPM-GPG-KEY-AlmaLinux-$(ALMALINUX_VERSION_MAJOR)'


ARCHLINUX_VERSION = latest
ARCHLINUX_ARCH    = x86_64
ARCHLINUX_MIRROR  = 'https://mirror.cyberbits.eu/archlinux/iso/$(ARCHLINUX_VERSION)'
ARCHLINUX_ISO_URL = '$(ARCHLINUX_MIRROR)/archlinux-$(ARCHLINUX_ARCH).iso'
ARCHLINUX_CHECKSUMS = '$(ARCHLINUX_MIRROR)/sha256sums.txt'
ARCHLINUX_ISO_SIGN = '$(ARCHLINUX_ISO_URL).sig'

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


################
TARGETS = list

CLEAN_TARGETS     = clean-checksums
DISTCLEAN_TARGETS = clean-debian clean-ubuntu clean-archlinux clean-rocky-linux

ifneq (,$(wildcard $(MAKEFILE_CUSTOM)))
include $(MAKEFILE_CUSTOM)
endif

################ Let's go ################
all: $(TARGETS)

clean: $(CLEAN_TARGETS)
distclean: $(DISTCLEAN_TARGETS)

fetch: fetch-debian-iso fetch-ubuntu-iso fetch-rockylinux-iso fetch-archlinux-iso fetch-pfsense-iso
check: check-debian-iso check-ubuntu-iso check-rockylinux-iso check-archlinux-iso check-pfsense-iso

.PHONY: info list
info:
	@echo "TARGETS= $(TARGETS)"
	@echo "============="
	@echo "Use make <os> to fetch and verify an ISO for <os>, and create a convenient symlink <os>.iso if all goes well"
	@echo "Run make list to get the list of supported OS"
	$(MAKE) list

list:
	@echo "=> list of supported OS <os>:"
	@echo "      <os>:    Distrib   Version   Arch  URL "
	@echo "----------- ---------- -------- ------ ---------"
	@printf "%10s: %10s %8s %6s %s\n" "debian"     "Debian"     $(DEBIAN_VERSION) $(DEBIAN_ARCH) $(DEBIAN_ISO_URL)
	@printf "%10s: %10s %8s %6s %s\n"	"ubuntu"     "Ubuntu"     $(UBUNTU_VERSION) $(UBUNTU_ARCH) $(UBUNTU_ISO_URL)
	@printf "%10s: %10s %8s %6s %s\n"	"rockylinux" "RockyLinux" $(ROCKYLINUX_VERSION) $(ROCKYLINUX_ARCH) $(ROCKYLINUX_ISO_URL)
	@printf "%10s: %10s %8s %6s %s\n"	"archlinux"  "ArchLinux"  $(ARCHLINUX_VERSION) $(ARCHLINUX_ARCH) $(ARCHLINUX_ISO_URL)
	@printf "%10s: %10s %8s %6s %s\n"	"pfsense"    "pfSense"    $(PFSENSE_VERSION) $(PFSENSE_ARCH) $(PFSENSE_ISO_URL)
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

clean-checksums:  clean-debian-checksums  clean-ubuntu-checksums clean-rocky-linux-checksums clean-archlinux-checksums clean-pfsense-checksums

##########  Debian ###########
debian: fetch-debian-iso check-debian-iso

fetch-debian: fetch-debian-iso fetch-debian-iso-checksum fetch-debian-gpg-keys
fetch-debian-iso: __fetch-iso-debian
fetch-debian-iso-checksum: __fetch-iso-debian-checksum	__fetch-iso-debian-checksum-sign

$(eval $(call __FETCH,iso-debian,$(ISO_DIR),$(DEBIAN_ISO_URL)))
$(eval $(call __FETCH,iso-debian-checksum,$(ISO_DIR),$(DEBIAN_CHECKSUMS)))
$(eval $(call __FETCH,iso-debian-checksum-sign,$(ISO_DIR),$(DEBIAN_CHECKSUMS_SIGN)))

fetch-debian-gpg-keys:
	@echo "=> collect the Debian GPG keys listed on https://www.debian.org/CD/verify"
	gpg --keyserver keyring.debian.org --recv-keys 0x988021A964E6EA7D
	gpg --keyserver keyring.debian.org --recv-keys 0xDA87E80D6294BE9B
	gpg --keyserver keyring.debian.org --recv-keys 0x42468F4009EA8AC3

check-debian-iso: fetch-debian __gpg_verify_detached_sign-iso-debian-checksum __check-iso-debian clean-debian-checksums

$(eval $(call __GPG_VERIFY_DETACHED_SIGN,iso-debian-checksum,$(ISO_DIR),$(DEBIAN_CHECKSUMS),$(DEBIAN_CHECKSUMS_SIGN)))
$(eval $(call __VERIFY_ISO_CHECKSUM,debian,$(ISO_DIR),$(DEBIAN_ISO_URL),$(DEBIAN_CHECKSUMS)))

clean-debian-checksums: __clean-iso-debian-checksum __clean-iso-debian-checksum-sign
clean-debian: clean-debian-checksums __clean-iso-debian
	-rm -f $(ISO_DIR)/debian.iso


########## Ubuntu ###########
ubuntu: fetch-ubuntu-iso check-ubuntu-iso

fetch-ubuntu: fetch-ubuntu-iso fetch-ubuntu-iso-checksum fetch-ubuntu-gpg-keys
fetch-ubuntu-iso: __fetch-iso-ubuntu
fetch-ubuntu-iso-checksum: __fetch-iso-ubuntu-checksum  __fetch-iso-ubuntu-sign

$(eval $(call __FETCH,iso-ubuntu,$(ISO_DIR),$(UBUNTU_ISO_URL)))
$(eval $(call __FETCH,iso-ubuntu-checksum,$(ISO_DIR),$(UBUNTU_CHECKSUMS)))
$(eval $(call __FETCH,iso-ubuntu-sign,$(ISO_DIR),$(UBUNTU_CHECKSUMS_SIGN)))

fetch-ubuntu-gpg-keys:
	@echo "=> collect the Ubuntu GPG keys listed on https://ubuntu.com/tutorials/how-to-verify-ubuntu"
	gpg --keyid-format long --keyserver keyserver.ubuntu.com --recv-keys 0x46181433FBB75451
	gpg --keyid-format long --keyserver keyserver.ubuntu.com --recv-keys 0xD94AA3F0EFE21092

check-ubuntu-iso: fetch-ubuntu __gpg_verify_detached_sign-iso-ubuntu-checksum __check-iso-ubuntu clean-ubuntu-checksums
$(eval $(call __GPG_VERIFY_DETACHED_SIGN,iso-ubuntu-checksum,$(ISO_DIR),$(UBUNTU_CHECKSUMS),$(UBUNTU_CHECKSUMS_SIGN)))
$(eval $(call __VERIFY_ISO_CHECKSUM,ubuntu,$(ISO_DIR),$(UBUNTU_ISO_URL),$(UBUNTU_CHECKSUMS)))

clean-ubuntu-checksums:  __clean-iso-ubuntu-checksum __clean-iso-ubuntu-sign
clean-ubuntu: clean-ubuntu-checksums __clean-iso-ubuntu
	-rm -f $(ISO_DIR)/ubuntu.iso


########## Rocky Linux ###########
rockylinux: fetch-rockylinux-iso check-rockylinux-iso

fetch-rockylinux: fetch-rockylinux-iso fetch-rockylinux-iso-checksum
fetch-rockylinux-iso: __fetch-iso-rockylinux
fetch-rockylinux-iso-checksum: __fetch-iso-rockylinux-checksum __fetch-iso-rockylinux-checksum-sign

$(eval $(call __FETCH,iso-rockylinux,$(ISO_DIR),$(ROCKYLINUX_ISO_URL)))
$(eval $(call __FETCH,iso-rockylinux-checksum,$(ISO_DIR),$(ROCKYLINUX_CHECKSUMS)))
$(eval $(call __FETCH,iso-rockylinux-checksum-sign,$(ISO_DIR),$(ROCKYLINUX_CHECKSUMS_SIGN)))
# Unfortunately, no detached .sig or CHECKSUM in 9.0, nor signed version
$(eval $(call __FETCH,rockylinux-gpg-key,$(ISO_DIR),$(ROCKYLINUX_GPG_KEY)))

fetch-rockylinux-gpg-keys: __fetch-rockylinux-gpg-key
	@echo "=> import the Rockylinux GPG keys"
	gpg --import $(ISO_DIR)/$(shell basename $(ROCKYLINUX_GPG_KEY))
	$(MAKE) __clean-rockylinux-gpg-key

check-rockylinux-iso: fetch-rockylinux fetch-rockylinux-gpg-keys __gpg_verify_detached_sign-iso-rockylinux-checksum __check-iso-rockylinux clean-rockylinux-checksums

$(eval $(call __GPG_VERIFY_DETACHED_SIGN,iso-rockylinux-checksum,$(ISO_DIR),$(ALMALINUX_CHECKSUMS),$(ROCKYLINUX_CHECKSUMS_SIGN)))
$(eval $(call __VERIFY_ISO_CHECKSUM,rockylinux,$(ISO_DIR),$(ROCKYLINUX_ISO_URL),$(ROCKYLINUX_CHECKSUMS)))

clean-rockylinux-checksums: __clean-iso-rockylinux-checksum __clean-iso-rockylinux-checksum-sign
clean-rockylinux: clean-rockylinux-checksums __clean-iso-rockylinux
	-rm -f $(ISO_DIR)/rockylinux.iso


########## AlmaLinux ###########
almalinux: fetch-almalinux-iso check-almalinux-iso

fetch-almalinux: fetch-almalinux-iso fetch-almalinux-iso-checksum fetch-almalinux-gpg-keys
fetch-almalinux-iso: __fetch-iso-almalinux
fetch-almalinux-iso-checksum: __fetch-iso-almalinux-checksum
# __fetch-iso-almalinux-sign

$(eval $(call __FETCH,iso-almalinux,$(ISO_DIR),$(ALMALINUX_ISO_URL)))
$(eval $(call __FETCH,iso-almalinux-checksum,$(ISO_DIR),$(ALMALINUX_CHECKSUMS)))
$(eval $(call __FETCH,almalinux-gpg-key,$(ISO_DIR),$(ALMALINUX_GPG_KEY)))

fetch-almalinux-gpg-keys: __fetch-almalinux-gpg-key
	@echo "=> import the Almalinux GPG keys"
	gpg --import $(ISO_DIR)/$(shell basename $(ALMALINUX_GPG_KEY))
	$(MAKE) __clean-almalinux-gpg-key

check-almalinux-iso: fetch-almalinux fetch-almalinux-gpg-keys __gpg_verify_sign-iso-almalinux-checksum __check-iso-almalinux clean-almalinux-checksums
$(eval $(call __GPG_VERIFY_SIGN,iso-almalinux-checksum,$(ISO_DIR),$(ALMALINUX_CHECKSUMS)))
$(eval $(call __VERIFY_ISO_CHECKSUM,almalinux,$(ISO_DIR),$(ALMALINUX_ISO_URL),$(ALMALINUX_CHECKSUMS)))

clean-almalinux-checksums: __clean-iso-almalinux-checksum
clean-almalinux: clean-almalinux-checksums __clean-iso-almalinux
	-rm -f $(ISO_DIR)/almalinux.iso



########## Arch Linux ###########
archlinux: fetch-archlinux-iso check-archlinux-iso

fetch-archlinux: fetch-archlinux-iso fetch-archlinux-iso-checksum fetch-archlinux-gpg-keys
fetch-archlinux-iso: __fetch-iso-archlinux
fetch-archlinux-iso-checksum: __fetch-iso-archlinux-checksum	__fetch-iso-archlinux-sign

$(eval $(call __FETCH,iso-archlinux,$(ISO_DIR),$(ARCHLINUX_ISO_URL)))
$(eval $(call __FETCH,iso-archlinux-checksum,$(ISO_DIR),$(ARCHLINUX_CHECKSUMS)))
$(eval $(call __FETCH,iso-archlinux-sign,$(ISO_DIR),$(ARCHLINUX_ISO_SIGN)))

fetch-archlinux-gpg-keys:
	@echo "=> collect the Archlinux GPG keys - see https://archlinux.org/download/#checksums"
	gpg --keyid-format long --keyserver keyserver.ubuntu.com --recv-keys 0x54449A5C


check-archlinux-iso: fetch-archlinux  __gpg_verify_detached_sign-iso-archlinux __check-iso-archlinux clean-archlinux-checksums
$(eval $(call __GPG_VERIFY_DETACHED_SIGN,iso-archlinux,$(ISO_DIR),$(ARCHLINUX_ISO_URL),$(ARCHLINUX_ISO_SIGN)))

$(eval $(call __VERIFY_ISO_CHECKSUM,archlinux,$(ISO_DIR),$(ARCHLINUX_ISO_URL),$(ARCHLINUX_CHECKSUMS)))



# check-archlinux-iso: __check-iso-archlinux
# $(eval $(call __VERIFY_CHECKSUM2,archlinux,$(ISO_DIR),$(ARCHLINUX_ISO_URL),$(ARCHLINUX_CHECKSUMS)))

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
