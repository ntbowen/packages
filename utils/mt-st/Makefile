#
# Copyright (C) 2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=mt-st
PKG_VERSION:=1.8
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/iustin/mt-st/releases/download/v$(PKG_VERSION)
PKG_HASH:=e0549f55185bfdc82220e6dd530a2470189dd8017ee109fe91867dc0d6ac43dd

PKG_MAINTAINER:=Giuseppe Magnotta <giuseppe.magnotta@gmail.com>
PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=COPYING

include $(INCLUDE_DIR)/package.mk

define Package/mt-st
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Magnetic tape control tools for Linux SCSI tapes
  URL:=https://github.com/iustin/mt-st
endef

define Package/mt-st/description
	Includes a mt-like program supporting additional commands using ioctls
	specific to  the Linux SCSI tape driver (up to kernel 2.6.26), and the program
	stinit to define the SCSI tape devices in system startup scripts.
endef

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		CFLAGS="$(TARGET_CFLAGS) $(TARGET_CPPFLAGS)" \
		CC="$(TARGET_CC)"
endef

define Package/mt-st/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/mt $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/stinit $(1)/usr/bin/
endef

$(eval $(call BuildPackage,mt-st))
