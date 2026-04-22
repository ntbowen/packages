#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=fuse
PKG_VERSION:=3.18.2
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/libfuse/libfuse/releases/download/$(PKG_NAME)-$(PKG_VERSION)
PKG_HASH:=f01de85717e20adf5f98aff324acd85dd73d61a5ca3834d573dcf0bd6e54a298

PKG_MAINTAINER:=
PKG_CPE_ID:=cpe:/a:fuse_project:fuse

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

define Package/fuse/Default
  TITLE:=FUSE
  URL:=https://github.com/libfuse/libfuse
endef

define Package/fuse/Default/description
 FUSE (Filesystem in UserSpacE)
endef

define Package/fuse-utils
$(call Package/fuse/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libfuse3
  TITLE+= (utilities)
  SUBMENU:=Filesystem
  LICENSE:=GPL-2.0-only
  LICENSE_FILES:=COPYING
endef

define Package/fuse-utils/description
$(call Package/fuse/Default/description)
 This package contains the FUSE utilities.
 - fusermount3
 - mount.fuse3
endef

define Package/libfuse3
$(call Package/fuse/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE+= (library)
  DEPENDS:=+kmod-fuse +libpthread
  SUBMENU:=Filesystem
  ABI_VERSION:=3
  LICENSE:=LGPL-2.1-only
  LICENSE_FILES:=COPYING.LIB
endef

define Package/libfuse3/description
$(call Package/fuse/Default/description)
 This package contains the FUSE shared library (libfuse3).
endef

MESON_ARGS += \
	-Dutils=true \
	-Dexamples=false \
	-Dtests=false \
	-Dudevrulesdir='' \
	-Dinitscriptdir='' \
	-Ddisable-libc-symbol-version=true \
	-Denable-io-uring=false \
	-Denable-usdt=false \
	-Duseroot=false

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/fuse3 $(1)/usr/include/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libfuse3.{a,so*} $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/fuse3.pc $(1)/usr/lib/pkgconfig/
endef

define Package/fuse-utils/install
	$(INSTALL_DIR) $(1)/sbin
	$(CP) $(PKG_INSTALL_DIR)/usr/sbin/mount.fuse3 $(1)/sbin/
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(PKG_INSTALL_DIR)/usr/bin/fusermount3 $(1)/usr/bin/
endef

define Package/libfuse3/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libfuse3.so.* $(1)/usr/lib/
endef

$(eval $(call BuildPackage,libfuse3))
$(eval $(call BuildPackage,fuse-utils))
