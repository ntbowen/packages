#
# Copyright (C) 2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=sshfs
PKG_VERSION:=3.7.6
PKG_RELEASE:=3

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://github.com/libfuse/sshfs/releases/download/$(PKG_NAME)-$(PKG_VERSION)
PKG_HASH:=6a1bcb31450a077e9cb1b7ff158c71de34db697c3c0da6cb362502131e495893

PKG_MAINTAINER:=Zoltan HERPAI <wigyori@uid0.hu>
PKG_LICENSE:=GPL-2.0-only
PKG_LICENSE_FILES:=COPYING

include $(INCLUDE_DIR)/nls.mk
include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

define Package/sshfs
  TITLE:=SSHFS
  DEPENDS:=+fuse3-utils +glib2 +libpthread
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Filesystem
  URL:=https://github.com/libfuse/sshfs
endef

define Package/sshfs/description
	Mount remote system over sftp.
endef

define Package/sshfs/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/sshfs $(1)/usr/bin/
endef

$(eval $(call BuildPackage,sshfs))
