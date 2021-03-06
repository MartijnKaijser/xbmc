include ../../Makefile.include
PREFIX=$(NATIVEPREFIX)
PLATFORM=$(NATIVEPLATFORM)
DEPS=../../Makefile.include Makefile

# lib name, version
LIBNAME=flatbuffers
VERSION=1.9.0
SOURCE=$(LIBNAME)-$(VERSION)
ARCHIVE=$(SOURCE).tar.gz
BASE_URL=http://mirrors.kodi.tv/build-deps/sources

APP=$(PLATFORM)/flatc

# Only this package uses CMake for build on native at the moment,
# so there is no separate toolchain file. Still we have to unset
# the CMAKE_TOOLCHAIN_FILE, which is part of $(CMAKE) and set to
# the target toolchain file.
CMAKE_OPTIONS := -DCMAKE_TOOLCHAIN_FILE= \
                 -DCMAKE_BUILD_TYPE=Release \
                 -DFLATBUFFERS_CODE_COVERAGE=OFF \
                 -DFLATBUFFERS_BUILD_TESTS=OFF \
                 -DFLATBUFFERS_INSTALL=ON \
                 -DFLATBUFFERS_BUILD_FLATLIB=OFF \
                 -DFLATBUFFERS_BUILD_FLATC=ON \
                 -DFLATBUFFERS_BUILD_FLATHASH=OFF \
                 -DFLATBUFFERS_BUILD_GRPCTEST=OFF \
                 -DFLATBUFFERS_BUILD_SHAREDLIB=OFF \
                 -DCMAKE_C_COMPILER="$(CC_BINARY_FOR_BUILD)" \
                 -DCMAKE_CXX_COMPILER="$(CXX_BINARY_FOR_BUILD)" \
                 -DCMAKE_C_FLAGS="$(NATIVE_CFLAGS)" \
                 -DCMAKE_CXX_FLAGS="$(NATIVE_CXXFLAGS)" \
                 -DCMAKE_EXE_LINKER_FLAGS="$(NATIVE_LDFLAGS)" \
                 $(CMAKE_OPTIONS)
BUILDDIR = $(PLATFORM)/build-cmake # 'build' conflicts with file BUILD on case-insensitive FS

all: .installed-$(PLATFORM)

$(TARBALLS_LOCATION)/$(ARCHIVE):
	cd $(TARBALLS_LOCATION); $(RETRIEVE_TOOL) $(RETRIEVE_TOOL_FLAGS) $(BASE_URL)/$(ARCHIVE)

$(PLATFORM): $(TARBALLS_LOCATION)/$(ARCHIVE) $(DEPS)
	rm -rf $(PLATFORM)/*; mkdir -p $(PLATFORM)
	cd $(PLATFORM); $(ARCHIVE_TOOL) $(ARCHIVE_TOOL_FLAGS) $(TARBALLS_LOCATION)/$(ARCHIVE)
	cd $(PLATFORM); patch -p1 < ../0001-Fix-compiler-warning.patch
	mkdir -p $(BUILDDIR)
	cd $(BUILDDIR); $(CMAKE) $(CMAKE_OPTIONS) ..

$(APP): $(PLATFORM)
	$(MAKE) -C $(BUILDDIR)

.installed-$(PLATFORM): $(APP)
	$(MAKE) -C $(BUILDDIR) install
	touch $@

clean:
	$(MAKE) -C $(BUILDDIR) clean
	rm -f .installed-$(PLATFORM)

distclean:
	rm -rf $(PLATFORM) .installed-$(PLATFORM)
