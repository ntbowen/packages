#
# Copyright (C) 2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=sbc
PKG_VERSION:=2.2
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@KERNEL/linux/bluetooth/
PKG_HASH:=42cf484bb98c0ebc7c20dd8dbeabcd5fca0a4cc05a864cfc9d916e46028b39e0

PKG_LICENSE:=LGPL-2.1-or-later
PKG_LICENSE_FILES:=COPYING.LIB
PKG_MAINTAINER:=

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=lto

include $(INCLUDE_DIR)/package.mk

define Package/sbc
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=sbc encoding library
  DEPENDS:=+libsndfile
  URL:=http://www.bluez.org
endef

define Package/sbc/description
  Low Complexity Subband Coding for bluetooth audio profiles encoding and decoding.
  Needed for A2DP support.
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/sbc
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/include/sbc/sbc.h $(1)/usr/include/sbc/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libsbc* $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/sbc.pc $(1)/usr/lib/pkgconfig/
endef

define Package/sbc/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/sbc* $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libsbc.so* $(1)/usr/lib/
endef


$(eval $(call BuildPackage,sbc))
