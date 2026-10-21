include $(TOPDIR)/rules.mk

PKG_NAME:=libxmp
PKG_VERSION:=4.7.4
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/$(PKG_NAME)/$(PKG_NAME)/releases/download/$(PKG_NAME)-$(PKG_VERSION)
PKG_HASH:=f2a5a36d60b384e00b1b06240f8ad1faa981f5952dd99a58cfe7a72df97401f8

PKG_MAINTAINER:=Daniel Golle <daniel@makrotopia.org>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=README

CMAKE_INSTALL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/libxmp
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=Extended Module Player Library
  URL:=https://github.com/libxmp/libxmp
endef

define Package/libxmp/description
  Libxmp is a library that renders module files to PCM data. It supports
  over 90 mainstream and obscure module formats including Protracker (MOD),
  Scream Tracker 3 (S3M), Fast Tracker II (XM), and Impulse Tracker (IT).
endef

define Package/libxmp/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/lib*.so* $(1)/usr/lib/
endef

$(eval $(call BuildPackage,libxmp))
