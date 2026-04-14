#
# Copyright (C) 2017-2018 Thomas Guyot-Sionnest <tguyot@gmail.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=libndp
PKG_VERSION:=1.9
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=http://libndp.org/files/
PKG_HASH:=a8ab214e01dc3a9b615276905395637f391298c84d77651f0cbf0b1082dd2dd4

PKG_MAINTAINER:=Thomas Guyot-Sionnest <tguyot@gmail.com>
PKG_LICENSE:=LGPL-2.1-or-later
PKG_CPE_ID:=cpe:/a:libndp:libndp

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/libndp
  SECTION:=net
  CATEGORY:=Network
  TITLE:=NDP Library
  URL:=https://github.com/jpirko/libndp/
endef

define Package/libndp/description
    Library for Neighbor Discovery Protocol
endef

define Package/libndp/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libndp.so.* $(1)/usr/lib/
endef

define Package/ndptool
  SECTION:=net
  CATEGORY:=Network
  TITLE:=NDP Tool
  URL:=https://github.com/jpirko/libndp/
  DEPENDS:= +libndp
endef

define Package/ndptool/description
    Tool for Neighbor Discovery Protocol
endef

define Package/ndptool/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/ndptool $(1)/usr/bin/
endef

$(eval $(call BuildPackage,libndp))
$(eval $(call BuildPackage,ndptool))

