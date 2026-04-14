#
# Copyright (C) 2019-2020 Yousong Zhou <yszhou4tech@gmail.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=spice-protocol
PKG_VERSION:=0.14.5
PKG_RELEASE:=1
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://www.spice-space.org/download/releases
PKG_HASH:=baf58449f6e89d19f475899ad5fb9196fdc46c03cc53233f4e39cf2978f9cff7
PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=COPYING
PKG_MAINTAINER:=Yousong Zhou <yszhou4tech@gmail.com>

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

define Package/spice-protocol
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=SPICE - headers defining protocols
  URL:=https://www.spice-space.org/index.html
  BUILDONLY:=1
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/* $(1)/usr/include
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/share/pkgconfig/* $(1)/usr/lib/pkgconfig
endef

$(eval $(call BuildPackage,spice-protocol))
