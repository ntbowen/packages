# SPDX-License-Identifier: GPL-3.0-only
#
# Copyright (C) 2021-2022 ImmortalWrt.org

include $(TOPDIR)/rules.mk

PKG_NAME:=amule
PKG_VERSION:=3.0.0
PKG_RELEASE:=5

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/amule-project/amule/tar.gz/$(PKG_VERSION)?
PKG_HASH:=850a09d1a5ec7d1b8962e4051d51b29355ac3df84a7b0b1209f2d0dd5c62eab4

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/amule
  SUBMENU:=P2P
  SECTION:=net
  CATEGORY:=Network
  TITLE:=A multi-platform eMule-like ed2k client
  URL:=http://www.amule.org/
  DEPENDS:=+antileech +libpng +libpthread +libncurses +libreadline +libwxbase \
	+libupnp +libbfd +libcryptopp +libzstd
endef

CMAKE_OPTIONS += \
	-DBUILD_MONOLITHIC=OFF \
	-DBUILD_DAEMON=ON \
	-DBUILD_AMULECMD=ON \
	-DBUILD_WEBSERVER=ON \
	-DBUILD_REMOTEGUI=OFF \
	-DBUILD_CAS=OFF \
	-DBUILD_WXCAS=OFF \
	-DENABLE_NLS=OFF \
	-DENABLE_UPNP=ON \
	-DENABLE_IP2COUNTRY=OFF

define Package/amule/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/amulecmd $(1)/usr/bin/amulecmd
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/amuled $(1)/usr/bin/amuled
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/amuleweb $(1)/usr/bin/amuleweb

	$(INSTALL_DIR) $(1)/usr/share
	$(CP) $(PKG_INSTALL_DIR)/usr/share/amule $(1)/usr/share/
endef

$(eval $(call BuildPackage,amule))
