include ../../Makefile.include
DEPS= ../../Makefile.include 0001-dontbuildswig.patch 0002-missing-libs.patch \
      0003-no-so-version.patch Makefile

# lib name, version
LIBNAME=libplist
VERSION=1.6
SOURCE=$(LIBNAME)-$(VERSION)
FILENAME=v$(VERSION)
ARCHIVE=$(SOURCE).tar.gz
LIBDYLIB=$(PLATFORM)/build/src/libplist++.so
ICONV=$(shell echo $(LINK_ICONV) | sed -e 's/\-l//')
export CFLAGS+=-fvisibility=default
all: .installed-$(PLATFORM)

$(TARBALLS_LOCATION)/$(ARCHIVE):
	cd $(TARBALLS_LOCATION); $(RETRIEVE_TOOL) $(RETRIEVE_TOOL_FLAGS) $(BASE_URL)/$(ARCHIVE)

$(PLATFORM): $(TARBALLS_LOCATION)/$(ARCHIVE) $(DEPS)
	echo $(ICONV)
	rm -rf $(PLATFORM); mkdir -p $(PLATFORM)
	cd $(PLATFORM); $(ARCHIVE_TOOL) $(ARCHIVE_TOOL_FLAGS) $(TARBALLS_LOCATION)/$(ARCHIVE)
	cd $(PLATFORM); patch -p1 < ../0001-dontbuildswig.patch
	cd $(PLATFORM); sed -ie 's/TARGET_LINK_LIBRARIES( plist /TARGET_LINK_LIBRARIES( plist m z $(ICONV) /' src/CMakeLists.txt
	cd $(PLATFORM); rm -rf build; mkdir -p build
	cd $(PLATFORM)/build; $(CMAKE) VERBOSE=1 ..

$(LIBDYLIB): $(PLATFORM)
	$(MAKE) -j 1 -C $(PLATFORM)/build VERBOSE=1

.installed-$(PLATFORM): $(LIBDYLIB)
	$(MAKE) -C $(PLATFORM)/build install
ifeq ($(OS),android)
	rm -f $(PREFIX)/lib/libplist.la
	-$(READELF) --dynamic $(PREFIX)/lib/libplist.so | grep ibrary

	rm -f $(PREFIX)/lib/libplist++.la
	-$(READELF) --dynamic $(PREFIX)/lib/libplist++.so | grep ibrary
endif
	touch $@

clean:
	rm -rf $(PLATFORM) .installed-$(PLATFORM)

distclean::
	rm -rf $(PLATFORM) .installed-$(PLATFORM)

