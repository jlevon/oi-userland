#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#
# Copyright (c) 2011, 2013, Oracle and/or its affiliates. All rights reserved.
# Copyright (c) 2019, Michal Nowak
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		emacs
COMPONENT_VERSION=	26.3
COMPONENT_REVISION=	1
COMPONENT_PROJECT_URL=	https://www.gnu.org/software/emacs/
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.xz
COMPONENT_ARCHIVE_HASH=	\
	sha256:4d90e6751ad8967822c6e092db07466b9d383ef1653feb2f95c93e7de66d3485
COMPONENT_ARCHIVE_URL=	http://ftp.gnu.org/gnu/emacs/$(COMPONENT_ARCHIVE)

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/configure.mk
include $(WS_MAKE_RULES)/ips.mk

# we build three different variants of emacs for our users. As Solaris
# always has a 64-bit kernel, and 64-bit emacs can handle larger files,
# we only build and deliver 64-bit binaries.
VARIANTS =	nox x gtk

BUILD_32 = $(VARIANTS:%=$(BUILD_DIR)/$(MACH32)-%/.built)
INSTALL_32 = $(VARIANTS:%=$(BUILD_DIR)/$(MACH32)-%/.installed)

# Force use of gnutls-3 pkgconfig during 2.x->3.x transition
GNUTLS_PKG_CONFIG_PATH_32 = /usr/lib/pkgconfig/gnutls-3
GNUTLS_PKG_CONFIG_PATH_64 = /usr/lib/$(MACH64)/pkgconfig/gnutls-3
PKG_CONFIG_PATH = $(GNUTLS_PKG_CONFIG_PATH_$(BITS)):$(PKG_CONFIG_PATH.$(BITS))

LD_OPTIONS.32 += -L/usr/lib/gnutls-3 -R/usr/lib/gnutls-3
LD_OPTIONS.64 += -L/usr/lib/$(MACH64)/gnutls-3 -R/usr/lib/$(MACH64)/gnutls-3
LD_OPTIONS += $(LD_OPTIONS.$(BITS))

# build with the distribution preferred libjpeg implementation
CFLAGS   += $(JPEG_CPPFLAGS) $(JPEG_CFLAGS)
CXXFLAGS += $(JPEG_CPPFLAGS) $(JPEG_CXXFLAGS)
LDFLAGS  += $(JPEG_LDFLAGS)

CFLAGS	+= $(shell pkg-config --cflags lcms2)

# LD_OPTIONS is defined to apply desirable link-editor options to Userland
# components. Non-executable stack and data break sparc emacs.
LD_MAP_NOEXSTK.sparc=
LD_MAP_NOEXDATA.sparc=

# LDFLAGS is defined for emacs to detect ncurses
LDFLAGS += -L/usr/gnu/lib -R/usr/gnu/lib

# This code is built with gcc. The primary reason for this is that the
# configure script has problems using a non-GNU cpp. I am not aware of
# any reason Sun Studio could not be made to work, but simply made a
# cost/benefit decision not to pursue it at this time
COMPILER =	gcc

# Uncomment this for debugging only. It configures emacs to run from the
# local proto instead of from its final installed location.
#CONFIGURE_PREFIX =	$(PROTO_DIR)/usr

# The configure script runs the pkg-config command. Since we're building
# 64-bit executables, we need pkg-config to use the 64-bit metadata files
# CONFIGURE_ENV += PKG_CONFIG_PATH=/usr/lib/$(MACH64)/pkgconfig

# configure options common to all variants of emacs that we want to build.
CONFIGURE_OPTIONS +=	--infodir=$(CONFIGURE_INFODIR)
CONFIGURE_OPTIONS +=	--datarootdir=$(CONFIGURE_PREFIX)/share
CONFIGURE_OPTIONS +=	--libexecdir=$(CONFIGURE_PREFIX)/lib
CONFIGURE_OPTIONS +=	--with-gif=no
CONFIGURE_OPTIONS +=	ac_cv_sys_long_file_names=yes

# ASLR should remain disabled for emacs (the default);
# build consistently core dumps with ASLR
ASLR_MODE = $(ASLR_DISABLE)

# variant specific configure options
$(BUILD_DIR)/%-nox/.configured: CONFIGURE_OPTIONS +=	--without-all --without-x
$(BUILD_DIR)/%-x/.configured:    CONFIGURE_OPTIONS +=	--with-x-toolkit=lucid
$(BUILD_DIR)/%-gtk/.configured:  CONFIGURE_OPTIONS +=	--with-x-toolkit=gtk3

# we need to build all variants, but only have to install one in order
# to get the common files shared by all. We use COMPONENT_POST_INSTALL_ACTION
# to remove unwanted files, copy the emacs binaries from the other tookit
# builds into place, and install the additional files we provide.
#
# Note that this tweaking is not required, as the proto need not match
# the packaging. However, doing it this way allows us to examine the proto
# as a finished and complete product, simplifies debugging emacs, and
# facilitates the use of 'gmake sample-manifest'.
PBIN=		$(PROTO_DIR)/usr/bin
PETC=		$(PROTO_DIR)/usr/share/emacs/$(COMPONENT_VERSION)/etc
PGBIN=		$(PROTO_DIR)/usr/gnu/bin
PGSHAREMAN1=	$(PROTO_DIR)/usr/gnu/share/man/man1
PSHAREMAN1=	$(PROTO_DIR)/usr/share/man/man1
PVAR=		$(PROTO_DIR)/var

# Remove emacs existing emacs executables/script from usr/bin
COMPONENT_POST_INSTALL_ACTION += $(RM) $(PBIN)/emacs $(PBIN)/emacs-* ;

# GTK binaries
COMPONENT_POST_INSTALL_ACTION += \
	$(CP) $(BUILD_DIR)/$(MACH32)-gtk/src/emacs-$(COMPONENT_VERSION).1 \
		$(PBIN)/emacs-gtk ;
COMPONENT_POST_INSTALL_ACTION += \
	$(LN) $(PBIN)/emacs-gtk $(PBIN)/emacs-gtk-$(COMPONENT_VERSION) ;

# Non-X11 binaries
COMPONENT_POST_INSTALL_ACTION += \
	$(CP) $(BUILD_DIR)/$(MACH32)-nox/src/emacs-$(COMPONENT_VERSION).1 \
		$(PBIN)/emacs-nox ;
COMPONENT_POST_INSTALL_ACTION += \
	$(LN) $(PBIN)/emacs-nox $(PBIN)/emacs-nox-$(COMPONENT_VERSION) ;

# X11 (Athena) binaries
COMPONENT_POST_INSTALL_ACTION += \
	$(CP) $(BUILD_DIR)/$(MACH32)-x/src/emacs-$(COMPONENT_VERSION).1 \
		$(PBIN)/emacs-x ;
COMPONENT_POST_INSTALL_ACTION += \
	$(LN) $(PBIN)/emacs-x $(PBIN)/emacs-x-$(COMPONENT_VERSION) ;

# Emacs shell script that picks the right variant at runtime
COMPONENT_POST_INSTALL_ACTION += $(CP) augment/emacs $(PBIN) ;
COMPONENT_POST_INSTALL_ACTION += $(CHMOD) +x $(PBIN)/emacs ;

# ctags and etags go in /usr/gnu/bin instead of /usr/bin
COMPONENT_POST_INSTALL_ACTION += $(MKDIR) $(PGBIN) ;
COMPONENT_POST_INSTALL_ACTION += $(MV) $(PBIN)/ctags $(PBIN)/etags $(PGBIN) ;

# We do not install the files under /var/games/emacs. The reason for
# this is that we do not install update-game-score as setuid, and
# therefore the game files are not usable. This the same decision made
# by Debian, among others.
COMPONENT_POST_INSTALL_ACTION += $(RM) -r $(PVAR) ;

# The file usr/share/emacs/23.1/etc/DOC-23.1.2 gets installed instead of
# DOC-23.1.1. Remove and replace.
COMPONENT_POST_INSTALL_ACTION += $(RM) $(PETC)/DOC-$(COMPONENT_VERSION).2 ;
COMPONENT_POST_INSTALL_ACTION += \
	$(CP) $(BUILD_DIR)/$(MACH32)-x/etc/DOC-$(COMPONENT_VERSION).1 \
		$(PETC)/DOC-$(COMPONENT_VERSION).1 ;

COMPONENT_POST_INSTALL_ACTION += \
	$(CP) augment/man/man1/emacs-gtk.1 $(PSHAREMAN1)/emacs-gtk.1 ;
COMPONENT_POST_INSTALL_ACTION += \
	$(CP) augment/man/man1/emacs-nox.1 $(PSHAREMAN1)/emacs-nox.1 ;
COMPONENT_POST_INSTALL_ACTION += \
	$(CP) augment/man/man1/emacs-x.1 $(PSHAREMAN1)/emacs-x.1 ;

COMPONENT_POST_INSTALL_ACTION += $(MKDIR) $(PGSHAREMAN1) ;
COMPONENT_POST_INSTALL_ACTION += \
	$(CP) $(COMPONENT_SRC)/doc/man/ctags.1 $(PGSHAREMAN1)/ctags.1 ;
COMPONENT_POST_INSTALL_ACTION += \
	$(CP) $(COMPONENT_SRC)/doc/man/etags.1 $(PGSHAREMAN1)/etags.1 ;

# By default, emacs is configured to compress lisp files, manpages, and info
# files. The option to disable that (--without-compress-install) has no
# granularity --- it's all or nothing. For Solaris, we want to compress lisp
# sources, but not the others. It's simplest to let everything be compressed.
# and then explicitly decompress the manpages and info files.
COMPONENT_POST_INSTALL_ACTION += $(GUNZIP) \
        $(PROTO_DIR)/usr/share/info/*.gz \
        $(PROTO_DIR)/usr/share/man/man1/*.gz ;

# Throw away usr/share/info/dir. This file is the topmost node of the Info
# hierarchy. Emacs builds it, as presumably do all GNU packages, but on
# Solaris, it is delivered by system/prerequisite/gnu.
COMPONENT_POST_INSTALL_ACTION += $(RM) $(PROTO_DIR)/usr/share/info/dir ;

build:          $(BUILD_32)

install:	$(BUILD_32) $(BUILD_DIR)/$(MACH32)-x/.installed

test:		$(NO_TESTS)

# Auto-generated dependencies
REQUIRED_PACKAGES += file/gnu-findutils
REQUIRED_PACKAGES += image/imagemagick
REQUIRED_PACKAGES += image/library/libjpeg8-turbo
REQUIRED_PACKAGES += image/library/libpng16
REQUIRED_PACKAGES += image/library/librsvg
REQUIRED_PACKAGES += image/library/libtiff
REQUIRED_PACKAGES += library/desktop/cairo
REQUIRED_PACKAGES += library/desktop/gdk-pixbuf
REQUIRED_PACKAGES += library/desktop/gtk3
REQUIRED_PACKAGES += library/desktop/pango
REQUIRED_PACKAGES += library/glib2
REQUIRED_PACKAGES += library/gnutls-3
REQUIRED_PACKAGES += library/lcms2
REQUIRED_PACKAGES += library/libxml2
REQUIRED_PACKAGES += library/ncurses
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += SUNWcs
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/fontconfig
REQUIRED_PACKAGES += system/library/freetype-2
REQUIRED_PACKAGES += system/library/libdbus
REQUIRED_PACKAGES += system/library/math
REQUIRED_PACKAGES += x11/library/libice
REQUIRED_PACKAGES += x11/library/libsm
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxcb
REQUIRED_PACKAGES += x11/library/libxext
REQUIRED_PACKAGES += x11/library/libxfixes
REQUIRED_PACKAGES += x11/library/libxft
REQUIRED_PACKAGES += x11/library/libxinerama
REQUIRED_PACKAGES += x11/library/libxmu
REQUIRED_PACKAGES += x11/library/libxpm
REQUIRED_PACKAGES += x11/library/libxrandr
REQUIRED_PACKAGES += x11/library/libxrender
REQUIRED_PACKAGES += x11/library/toolkit/libxaw7
REQUIRED_PACKAGES += x11/library/toolkit/libxt
