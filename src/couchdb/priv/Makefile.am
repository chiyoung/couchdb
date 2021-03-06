## Licensed under the Apache License, Version 2.0 (the "License"); you may not
## use this file except in compliance with the License. You may obtain a copy of
## the License at
##
##   http://www.apache.org/licenses/LICENSE-2.0
##
## Unless required by applicable law or agreed to in writing, software
## distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
## WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
## License for the specific language governing permissions and limitations under
## the License.

couchlibdir = $(localerlanglibdir)/couch-$(version)
couchprivdir = $(couchlibdir)/priv
couchprivlibdir = $(couchlibdir)/priv/lib
erlinterfaceldflags = $(ERL_INTERFACE_DIR_LIB) -lerl_interface -lei

EXTRA_DIST = \
	stat_descriptions.cfg.in \
	couch_ejson_compare/erl_nif_compat.h \
	couch_ejson_compare/couch_ejson_compare.h

CLEANFILES = stat_descriptions.cfg
SNAPPY_SRC=../../snappy/snappy-$(SNAPPY_MAJOR).$(SNAPPY_MINOR).$(SNAPPY_PATCHLEVEL)
WIN_SNAPPY_SRC=../../$(SNAPPY_SRC)

if WINDOWS
ICU_OUTPUT_DIR = icu_driver/win32
icu_driver_msbuild = $(ICU_OUTPUT_DIR)/msbuild.bat
icu_driver_vcproj = $(ICU_OUTPUT_DIR)/couch_icu_driver.vcxproj
WIN_ICU_INCLUDE = $(ICU_OUTPUT_DIR)/win_icu_include.tmp
WIN_ICU_LIB_DIR = $(ICU_OUTPUT_DIR)/win_icu_dir.tmp
WIN_ERL_INCLUDE = $(ICU_OUTPUT_DIR)/win_erl_include.tmp
COUCH_ICU_DRIVER = $(ICU_OUTPUT_DIR)/couch_icu_driver.dll
EJSON_COMPARE_DIR = couch_ejson_compare/win32
EJSON_COMPARE_MSBUILD = $(EJSON_COMPARE_DIR)/msbuild.bat
EJSON_COMPARE_VCPROJ = $(EJSON_COMPARE_DIR)/couch_ejson_compare.vcxproj
COUCH_EJSON_COMPARE = $(EJSON_COMPARE_DIR)/couch_ejson_compare.dll

else
ICU_LOCAL_FLAGS = $(ICU_LOCAL_CFLAGS) $(ICU_LOCAL_LDFLAGS)
ICU_LOCAL_LIBS=-licuuc -licudata -licui18n

couchprivlib_LTLIBRARIES = couch_icu_driver.la
if USE_OTP_NIFS
couchprivlib_LTLIBRARIES += couch_ejson_compare.la

couch_ejson_compare_la_SOURCES = couch_ejson_compare/couch_ejson_compare.c \
	couch_ejson_compare/couch_raw_json_compare.c \
	couch_ejson_compare/couch_ejson_compare_utils.c
couch_ejson_compare_la_CFLAGS = -D_BSD_SOURCE $(ICU_LOCAL_FLAGS)
couch_ejson_compare_la_LDFLAGS = -module -avoid-version $(ICU_LOCAL_FLAGS)
couch_ejson_compare_la_LIBADD = $(ICU_LOCAL_LIBS)
if GCC
couch_ejson_compare_la_CFLAGS += -ansi -Wall -Winline
endif

endif
couch_icu_driver_la_SOURCES = icu_driver/couch_icu_driver.c
couch_icu_driver_la_LDFLAGS = -module -avoid-version $(ICU_LOCAL_FLAGS)
couch_icu_driver_la_CFLAGS = $(ICU_LOCAL_FLAGS)
couch_icu_driver_la_LIBADD = $(ICU_LOCAL_LIBS)
endif

if WINDOWS
couchpriv_DATA = stat_descriptions.cfg $(COUCH_ICU_DRIVER)
endif

locallibbin_PROGRAMS =

if !WINDOWS
couchpriv_DATA = stat_descriptions.cfg
endif
couchpriv_PROGRAMS =

%.cfg: %.cfg.in
	cp $< $@

if WINDOWS

$(COUCH_ICU_DRIVER) : icu_driver/couch_icu_driver.c $(icu_driver_msbuild) $(icu_driver_vcproj)
	$(MKDIR_P) "$(couchprivlibdir)" || true
	(cd icu_driver/win32 && cmd /c msbuild.bat)
	cp $(ICU_OUTPUT_DIR)/couch_icu_driver.dll $(couchprivlibdir)
	cp $(ICU_OUTPUT_DIR)/couch_icu_driver.lib $(couchprivlibdir)

all: $(COUCH_ICU_DRIVER)

$(icu_driver_msbuild): $(icu_driver_msbuild).tpl
	sed -e "s|%msbuild_dir%|$(msbuild_dir)|" \
	    -e "s|%msbuild_name%|$(msbuild_name)|" \
	    -e "s|^/cygdrive/\([a-zA-Z]\)|\1:|" \
	< $< > $@

$(icu_driver_vcproj): $(icu_driver_vcproj).tpl $(WIN_ICU_INCLUDE) $(WIN_ICU_LIB_DIR) $(WIN_ERL_INCLUDE)
	sed -e "s|%ICU_INCLUDE%|`cat $(WIN_ICU_INCLUDE)`|" \
	    -e "s|%ERLANG_INCLUDE%|`cat $(WIN_ERL_INCLUDE)`|" \
	    -e "s|%ICU_LIB_DIR%|`cat $(WIN_ICU_LIB_DIR)`|" \
	< $< > $@

$(WIN_ICU_INCLUDE):$(ICU_INCLUDE)
	echo $< | sed -e "s|^/cygdrive/\([a-zA-Z]\)|\1:|" > $@

$(WIN_ICU_LIB_DIR):$(ICU_LIB_DIR)
	echo $< | sed -e "s|^/cygdrive/\([a-zA-Z]\)|\1:|" > $@

$(WIN_ERL_INCLUDE):$(ERLANG_INCLUDE)
	echo $< | sed -e "s|^/cygdrive/\([a-zA-Z]\)|\1:|" > $@

$(COUCH_EJSON_COMPARE) : couch_ejson_compare/couch_ejson_compare.c couch_ejson_compare/couch_raw_json_compare.c couch_ejson_compare/couch_ejson_compare_utils.c $(EJSON_COMPARE_MSBUILD) $(EJSON_COMPARE_VCPROJ)
	$(MKDIR_P) "$(couchprivlibdir)" || true
	(cd couch_ejson_compare/win32 && cmd /c msbuild.bat)
	cp $(EJSON_COMPARE_DIR)/couch_ejson_compare.dll $(couchprivlibdir)
	cp $(EJSON_COMPARE_DIR)/couch_ejson_compare.lib $(couchprivlibdir)

$(EJSON_COMPARE_MSBUILD): $(EJSON_COMPARE_MSBUILD).tpl
	sed -e "s|%msbuild_dir%|$(msbuild_dir)|" \
	    -e "s|%msbuild_name%|$(msbuild_name)|" \
	    -e "s|^/cygdrive/\([a-zA-Z]\)|\1:|" \
	< $< > $@

$(EJSON_COMPARE_VCPROJ): $(EJSON_COMPARE_VCPROJ).tpl $(WIN_ICU_INCLUDE) $(WIN_ICU_LIB_DIR) $(WIN_ERL_INCLUDE)
	sed -e "s|%ICU_INCLUDE%|`cat $(WIN_ICU_INCLUDE)`|" \
	    -e "s|%ERLANG_INCLUDE%|`cat $(WIN_ERL_INCLUDE)`|" \
	    -e "s|%ICU_LIB_DIR%|`cat $(WIN_ICU_LIB_DIR)`|" \
	< $< > $@

endif

# libtool and automake have defeated markh.  For each of our executables
# we end up with 2 copies - one directly in the 'target' folder (eg, 'priv')
# and another - the correct one - in .libs.  The former doesn't work but is 
# what gets installed for 'couchspawnkillable' - but the correct one for
# couchjs.exe *does* get copied.  *shrug*  So just clobber it with the 
# correct one as the last step. See bug COUCHDB-439
install-data-hook:
	if test -f "$(DESTDIR)$(couchprivlibdir)/couch_icu_driver"; then \
	    rm -f "$(DESTDIR)$(couchprivlibdir)/couch_icu_driver.so"; \
	    cd "$(DESTDIR)$(couchprivlibdir)" && \
	        $(LN_S) couch_icu_driver couch_icu_driver.so; \
	fi
	if test -f "$(DESTDIR)$(couchprivlibdir)/couch_ejson_compare_nif"; then \
	    rm -f "$(DESTDIR)$(couchprivlibdir)/couch_ejson_compare_nif.so"; \
	    cd "$(DESTDIR)$(couchprivlibdir)" && \
	        $(LN_S) couch_ejson_compare_nif couch_ejson_compare_nif.so; \
	fi
if WINDOWS
	$(INSTALL) $(ICU_LOCAL_BIN)/icuuc44.dll $(bindir)
	$(INSTALL) $(ICU_LOCAL_BIN)/icudt44.dll $(bindir)
	$(INSTALL) $(ICU_LOCAL_BIN)/icuin44.dll $(bindir)
endif

uninstall-local:
	if test -f "$(DESTDIR)$(couchprivlibdir)/couch_erl_driver"; then \
	    rm -f "$(DESTDIR)$(couchprivlibdir)/couch_erl_driver.so"; \
	fi
