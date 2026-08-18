#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
# updated to work with latest source from abrasive
#

include $(TOPDIR)/rules.mk

PKG_NAME:=nqptp
PKG_VERSION:=1.2.8
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/mikebrady/nqptp/tar.gz/$(PKG_VERSION)?
PKG_HASH:=3a2882a299c21605f53bb215ce537f9cc7a1e894476f639ab28562c68fd183a9

PKG_MAINTAINER:=Ben Klang <bklang@wirehack.net>
PKG_LICENSE:=GPL-2.0-only
PKG_LICENSE_FILES:=COPYING LICENSES nqptp.c

PKG_FIXUP:=autoreconf
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/nqptp
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Not Quite Peer-to-Peer
  URL:=https://github.com/mikebrady/nqptp
endef

define Package/nqptp/description
  nqptp is a daemon that monitors timing data from PTP clocks it sees on ports
  319 and 320. It maintains records for one clock, identified by its Clock ID.

  It is a companion application to Shairport Sync and provides timing
  information for AirPlay 2 operation.
endef

define Package/nqptp/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/nqptp $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/nqptp.init $(1)/etc/init.d/nqptp
endef

$(eval $(call BuildPackage,nqptp))
