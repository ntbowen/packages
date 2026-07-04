#
# Copyright (C) 2007-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=protobuf
PKG_VERSION:=35.1
PKG_RELEASE:=3

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/protocolbuffers/protobuf/releases/download/v$(PKG_VERSION)
PKG_HASH:=22775f9376938295efa2d59a59bde4cd075a42df5a9b4d27aa9b99fa6a413bd2

PKG_MAINTAINER:=
PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:google:protobuf


include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/libutf8-range
  $(call Package/protobuf/Default)
  TITLE:=UTF-8 range validation libraries (utf8_range + utf8_validity)
endef

define Package/libutf8-range/description
This package provides libutf8_range and libutf8_validity shared libraries,
split out from protobuf 35.0+.
endef

define Package/libutf8-range/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libutf8_range.so* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libutf8_validity.so* $(1)/usr/lib/
endef

define Package/protobuf/Default
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=A structured data encoding library
  URL:=https://github.com/google/protobuf
  DEPENDS:=+zlib +libpthread +libatomic +libstdcpp +abseil-cpp
endef

define Package/protobuf
  $(call Package/protobuf/Default)
  DEPENDS+=+protobuf-lite
endef

define Package/protobuf-lite
  $(call Package/protobuf/Default)
  DEPENDS+=+libutf8-range
endef

define Package/protobuf/description/Default
Protocol Buffers are a way of encoding structured data in an efficient
yet extensible format. Google uses Protocol Buffers for almost all
of its internal RPC protocols and file formats.
endef

define Package/protobuf/description
$(call Package/protobuf/description/Default)

This package provides the libprotoc, libprotobuf, and libprotobuf-lite
libraries.  For a much smaller protobuf package, see "protobuf-lite".

endef

define Package/protobuf-lite/description
$(call Package/protobuf/description/Default)

This package provides the libprotobuf-lite library.

endef

CMAKE_HOST_OPTIONS += \
	-Dprotobuf_BUILD_PROTOC_BINARIES=ON \
	-Dprotobuf_BUILD_TESTS=OFF \
	-DBUILD_SHARED_LIBS=OFF \
	-DCMAKE_INSTALL_LIBDIR=lib \
	-Dprotobuf_ABSL_PROVIDER=package \
	-DCMAKE_PREFIX_PATH=$(STAGING_DIR_HOST)/usr

CMAKE_OPTIONS += \
	-Dprotobuf_BUILD_PROTOC_BINARIES=ON \
	-Dprotobuf_BUILD_TESTS=OFF \
	-Dprotobuf_WITH_ZLIB=ON \
	-DBUILD_SHARED_LIBS=ON \
	-Dprotobuf_ABSL_PROVIDER=package \
	-DCMAKE_PREFIX_PATH=$(STAGING_DIR)/usr

TARGET_LDFLAGS += -latomic

define Build/InstallDev
	$(call Build/InstallDev/cmake,$(1))
	$(SED) 's,/usr/include,$$$${prefix}/include,g' $(1)/usr/lib/pkgconfig/protobuf.pc
	$(SED) 's,/usr/lib,$$$${exec_prefix}/lib,g' $(1)/usr/lib/pkgconfig/protobuf.pc
	$(SED) 's,/usr/include,$$$${prefix}/include,g' $(1)/usr/lib/pkgconfig/protobuf-lite.pc
	$(SED) 's,/usr/lib,$$$${exec_prefix}/lib,g' $(1)/usr/lib/pkgconfig/protobuf-lite.pc
endef

define Package/protobuf-lite/install
	$(INSTALL_DIR) \
		$(1)/usr/lib

	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/libprotobuf-lite.so* \
		$(PKG_INSTALL_DIR)/usr/lib/libutf8_range.so* \
		$(PKG_INSTALL_DIR)/usr/lib/libutf8_validity.so* \
		$(1)/usr/lib/
endef

define Package/protobuf/install
	$(INSTALL_DIR) \
		$(1)/usr/lib

	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/libprotoc.so*  \
		$(1)/usr/lib/

	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/libprotobuf.so* \
		$(1)/usr/lib/
endef

$(eval $(call BuildPackage,protobuf))
$(eval $(call BuildPackage,libutf8-range))
$(eval $(call BuildPackage,protobuf-lite))
$(eval $(call HostBuild))
