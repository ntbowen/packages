include $(TOPDIR)/rules.mk

PKG_NAME:=libxmp
PKG_VERSION:=4.7.2
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/$(PKG_NAME)/$(PKG_NAME)/releases/download/$(PKG_NAME)-$(PKG_VERSION)
PKG_HASH:=3a6ad7d154d931e21db79e1c0af3253998555c73706083ce9ab2b634180a6e99

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
