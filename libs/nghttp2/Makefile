include $(TOPDIR)/rules.mk

PKG_NAME:=nghttp2
PKG_VERSION:=1.70.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://github.com/nghttp2/nghttp2/releases/download/v$(PKG_VERSION)
PKG_HASH:=d38d641fb3877b4847c774ad88b5f3ab70daf1ae0e616079bbeec4691186fc6a

PKG_MAINTAINER:=Hans Dedecker <dedeckeh@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:nghttp2:nghttp2

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/libnghttp2
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=Library implementing the framing layer of HTTP/2
  ABI_VERSION:=14
endef

define Package/libnghttp2/description
HTTP/2 C Library
endef

CMAKE_OPTIONS += \
        -DENABLE_LIB_ONLY=ON

define Build/InstallDev
	$(call Build/InstallDev/cmake,$(1))
	$(SED) 's,/usr/include,$$$${prefix}/include,g' $(1)/usr/lib/pkgconfig/libnghttp2.pc
	$(SED) 's,/usr/lib,$$$${prefix}/lib,g' $(1)/usr/lib/pkgconfig/libnghttp2.pc
endef

define Package/libnghttp2/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libnghttp2.so.* $(1)/usr/lib
endef

$(eval $(call BuildPackage,libnghttp2))
