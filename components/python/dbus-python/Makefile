#
# This file and its contents are supplied under the terms of the
# Common Development and Distribution License ("CDDL)". You may
# only use this file in accordance with the terms of the CDDL.
#
# A full copy of the text of the CDDL should have accompanied this
# source. A copy of the CDDL is also available via the Internet at
# http://www.illumos.org/license/CDDL.
#

#
# Copyright 2017 Alexander Pyhalov
# Copyright 2019 Michal Nowak
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		dbus-python
COMPONENT_VERSION=	1.2.10
COMPONENT_PROJECT_URL=	http://www.freedesktop.org/wiki/Software/DBusBindings/
COMPONENT_SUMMARY=	Python bindings for D-Bus
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.gz
COMPONENT_ARCHIVE_HASH=	\
	sha256:d4332bbd4a0054fa4010b259c293b84d461bbd9d7a8ef528157d151c0398d886
COMPONENT_ARCHIVE_URL=	http://dbus.freedesktop.org/releases/dbus-python/$(COMPONENT_ARCHIVE)

PATH=$(PATH.gnu)

PYTHON_VERSIONS = 2.7 3.4 3.5

VARIANT_PYTHON27 = $(BUILD_DIR)/python-27
VARIANT_PYTHON34 = $(BUILD_DIR)/python-34
VARIANT_PYTHON35 = $(BUILD_DIR)/python-35

VARIANTS_32 = $(VARIANT_PYTHON27) $(VARIANT_PYTHON34)
VARIANTS_64 = $(VARIANT_PYTHON27) $(VARIANT_PYTHON34) $(VARIANT_PYTHON35)

BUILD_32 = $(VARIANTS_32:%=%/$(MACH32)/.built)
BUILD_64 = $(VARIANTS_64:%=%/$(MACH64)/.built)

INSTALL_32 = $(VARIANTS_32:%=%/$(MACH32)/.installed)
INSTALL_64 = $(VARIANTS_64:%=%/$(MACH64)/.installed)

TEST_32 = $(VARIANTS_32:%=%/$(MACH32)/.tested)
TEST_64 = $(VARIANTS_64:%=%/$(MACH64)/.tested)

$(VARIANT_PYTHON27)/$(MACH64)/.configured: BITS=64
$(VARIANT_PYTHON27)/$(MACH64)/.configured: PYTHON=$(PYTHON.2.7.64)
$(VARIANT_PYTHON27)/$(MACH64)/.configured: am_cv_python_pyexecdir=$(PYTHON.2.7.VENDOR_PACKAGES.64)
$(VARIANT_PYTHON27)/$(MACH64)/.configured: am_cv_python_pythondir=$(PYTHON.2.7.VENDOR_PACKAGES.64)

$(VARIANT_PYTHON27)/$(MACH32)/.configured: PYTHON=$(PYTHON.2.7.32)
$(VARIANT_PYTHON27)/$(MACH32)/.configured: am_cv_python_pyexecdir=$(PYTHON.2.7.VENDOR_PACKAGES.32)
$(VARIANT_PYTHON27)/$(MACH32)/.configured: am_cv_python_pythondir=$(PYTHON.2.7.VENDOR_PACKAGES.32)

$(VARIANT_PYTHON34)/$(MACH64)/.configured: BITS=64
$(VARIANT_PYTHON34)/$(MACH64)/.configured: PYTHON=$(PYTHON.3.4.64)
$(VARIANT_PYTHON34)/$(MACH64)/.configured: am_cv_python_pyexecdir=$(PYTHON.3.4.VENDOR_PACKAGES.64)
$(VARIANT_PYTHON34)/$(MACH64)/.configured: am_cv_python_pythondir=$(PYTHON.3.4.VENDOR_PACKAGES.64)

$(VARIANT_PYTHON34)/$(MACH32)/.configured: PYTHON=$(PYTHON.3.4.32)
$(VARIANT_PYTHON34)/$(MACH32)/.configured: am_cv_python_pyexecdir=$(PYTHON.3.4.VENDOR_PACKAGES.32)
$(VARIANT_PYTHON34)/$(MACH32)/.configured: am_cv_python_pythondir=$(PYTHON.3.4.VENDOR_PACKAGES.32)

$(VARIANT_PYTHON35)/$(MACH64)/.configured: BITS=64
$(VARIANT_PYTHON35)/$(MACH64)/.configured: PYTHON=$(PYTHON.3.5.64)
$(VARIANT_PYTHON35)/$(MACH64)/.configured: am_cv_python_pyexecdir=$(PYTHON.3.5.VENDOR_PACKAGES.64)
$(VARIANT_PYTHON35)/$(MACH64)/.configured: am_cv_python_pythondir=$(PYTHON.3.5.VENDOR_PACKAGES.64)

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/configure.mk
include $(WS_MAKE_RULES)/ips.mk

COMPONENT_PREP_ACTION = (cd $(@D) && autoreconf -fi)

CONFIGURE_OPTIONS+=	--sysconfdir=/etc
CONFIGURE_OPTIONS+=	--disable-static
CONFIGURE_ENV+=		PYTHON=$(PYTHON)
CONFIGURE_ENV+=		am_cv_python_pythondir="$(am_cv_python_pythondir)"
CONFIGURE_ENV+=		am_cv_python_pyexecdir="$(am_cv_python_pyexecdir)"

build:		$(BUILD_32_and_64)

install:	$(INSTALL_32_and_64)

test:		$(TEST_32_and_64)

REQUIRED_PACKAGES += runtime/python-27
REQUIRED_PACKAGES += runtime/python-34
REQUIRED_PACKAGES += runtime/python-35
# Auto-generated dependencies
REQUIRED_PACKAGES += library/glib2
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/libdbus
