# Copyright 2010 Google Inc.
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are
# met:
#
# * Redistributions of source code must retain the above copyright
#   notice, this list of conditions and the following disclaimer.
# * Redistributions in binary form must reproduce the above copyright
#   notice, this list of conditions and the following disclaimer in the
#   documentation and/or other materials provided with the distribution.
# * Neither the name of Google Inc. nor the names of its contributors
#   may be used to endorse or promote products derived from this software
#   without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
# A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
# OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
# SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
# LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
# DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
# THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

ACLOCAL_AMFLAGS = -I m4

CHECK_BOOTSTRAP_DEPS =
CHECK_KYUA_DEPS =
CHECK_LOCAL =
CLEAN_TARGETS =
PHONY_TARGETS =
CLEANFILES =

EXTRA_DIST =
noinst_DATA =
noinst_LIBRARIES =

doc_DATA = AUTHORS COPYING NEWS README
noinst_DATA += INSTALL README
EXTRA_DIST += $(doc_DATA) INSTALL README

if WITH_ATF
tests_topdir = $(pkgtestsdir)

tests_top_DATA = Kyuafile
EXTRA_DIST += $(tests_top_DATA)
endif

include bootstrap/Makefile.am.inc
include cli/Makefile.am.inc
include doc/Makefile.am.inc
include engine/Makefile.am.inc
include examples/Makefile.am.inc
include integration/Makefile.am.inc
include misc/Makefile.am.inc
include store/Makefile.am.inc
include utils/Makefile.am.inc

bin_PROGRAMS = kyua
kyua_SOURCES = main.cpp
kyua_CXXFLAGS = $(CLI_CFLAGS) $(ENGINE_CFLAGS) $(UTILS_CFLAGS)
kyua_LDADD = $(CLI_LIBS) $(ENGINE_LIBS) $(UTILS_LIBS)

CHECK_ENVIRONMENT = KYUA_CONFDIR=/non-existent \
                    KYUA_DOCDIR=$(abs_top_srcdir) \
                    KYUA_MISCDIR=$(abs_top_srcdir)/misc \
                    KYUA_STOREDIR=$(abs_top_srcdir)/store \
                    PATH=$(abs_top_builddir):$${PATH}
CHECK_VARS = -v test_suites.kyua.examplesdir="$(abs_top_srcdir)/examples"
INSTALLCHECK_ENVIRONMENT = KYUA_CONFDIR=/non-existent \
                           PATH=$(prefix)/bin:$${PATH}
INSTALLCHECK_VARS =

if WITH_ATF
CHECK_LOCAL += check-kyua
PHONY_TARGETS += check-kyua
check-kyua: $(CHECK_KYUA_DEPS)
	$(CHECK_ENVIRONMENT) $(TESTS_ENVIRONMENT) \
	    ./kyua --config=none $(CHECK_VARS) test \
            --kyuafile='$(top_srcdir)/Kyuafile' --build-root='$(top_builddir)'

installcheck-local: installcheck-kyua
PHONY_TARGETS += installcheck-kyua
installcheck-kyua:
	cd $(pkgtestsdir) && $(INSTALLCHECK_ENVIRONMENT) $(TESTS_ENVIRONMENT) \
	    kyua --config=none $(INSTALLCHECK_VARS) test
else
dist-hook: forbid-dist
PHONY_TARGETS += forbid-dist
forbid-dist:
	@echo "Sorry; cannot make dist without atf."
	@false
endif
check-local: $(CHECK_LOCAL)

if WITH_DOXYGEN
# This should probably be html-local, but it seems better to generate the
# documentation in all cases to get warnings about missing documentation every
# time the code is modified.  (And, after all, the documentation is not
# installed so generating it unconditionally is not a big problem.)
all-local: api-docs/api-docs.tag

api-docs/api-docs.tag: $(builddir)/Doxyfile $(SOURCES)
	mkdir -p api-docs
	${DOXYGEN} $(builddir)/Doxyfile 2>&1 | tee api-docs/warnings

CLEAN_TARGETS += clean-api-docs
clean-api-docs:
	rm -rf api-docs
endif

clean-local: $(CLEAN_TARGETS)

PHONY_TARGETS += clean-all
clean-all:
	GIT="$(GIT)" $(SH) $(srcdir)/admin/clean-all.sh

.PHONY: $(PHONY_TARGETS)
