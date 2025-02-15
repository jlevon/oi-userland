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
# Copyright 2014-2017 Alexander Pyhalov
#

BUILD_BITS=64

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		tor
COMPONENT_VERSION=	0.4.1.6
COMPONENT_PROJECT_URL=	https://www.torproject.org
COMPONENT_SUMMARY=	Anonymizing overlay network for TCP
COMPONENT_FMRI=		network/tor
COMPONENT_CLASSIFICATION=	Applications/Internet
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.gz
COMPONENT_ARCHIVE_HASH=	\
    sha256:2a88524ce426079fb9b828bc1b789f2c8ade3ed53c130851102debc3518bed71
COMPONENT_ARCHIVE_URL=	https://www.torproject.org/dist/$(COMPONENT_ARCHIVE)
COMPONENT_SIG_URL=	$(COMPONENT_ARCHIVE_URL).asc	
COMPONENT_LICENSE_FILE=	LICENSE
COMPONENT_LICENSE=	BSD, OPEN DATA LICENSE

include $(WS_MAKE_RULES)/common.mk

CPPFLAGS += -I/usr/include/libevent2

COMPONENT_PREP_ACTION = (cd $(@D) && autoreconf -f)

CONFIGURE_OPTIONS+=	--sysconfdir=/etc
CONFIGURE_OPTIONS+=	--localstatedir=/var

CONFIGURE_ENV += CPPFLAGS="$(CPPFLAGS)"

COMPONENT_TEST_ENV+=	PATH=$(PATH.gnu)

COMPONENT_TEST_TRANSFORMS += \
	'-n ' \
	'-e "/TOTAL:/p" ' \
	'-e "/SKIP:/p" ' \
	'-e "/PASS:/p" ' \
	'-e "/FAIL:/p" ' \
	'-e "/ERROR:/p" '

# Internal test suite results
# 'util/map_anon_nofork' test failed (https://trac.torproject.org/projects/tor/ticket/31897):
#    FAIL ... tor-0.4.1.6/src/test/test_util.c:6223: assert(buf[0] OP_EQ 0xd0): -48 vs 208

# Tests require GNU diff
REQUIRED_PACKAGES += text/gnu-diffutils
REQUIRED_PACKAGES += library/libevent2
REQUIRED_PACKAGES += library/security/openssl
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += SUNWcs
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += $(GXX_RUNTIME_PKG)
REQUIRED_PACKAGES += system/library/math
# Auto-generated dependencies
REQUIRED_PACKAGES += compress/xz
REQUIRED_PACKAGES += compress/zstd
