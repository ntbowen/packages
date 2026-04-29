#
# Copyright (C) 2016 Amnon Paz <pazamnon@gmail.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=lua-openssl
PKG_SOURCE_VERSION:=0.11.1
PKG_VERSION:=$(subst -,.,$(PKG_SOURCE_VERSION))
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/zhaozg/lua-openssl.git
PKG_MIRROR_HASH:=4e0aeaf5f9928682d5f4ef4be1611949831d778880fc0ce50c618b69ce9855d2

PKG_MAINTAINER:=Amnon Paz <pazamnon@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/lua-openssl
  SUBMENU:=Lua
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Lua openSSL binding
  URL:=http://github.com/zhaozg/lua-openssl
  DEPENDS:=+lua +libopenssl +librt
endef

define Package/lua-openssl/description
    A free, MIT-licensed OpenSSL binding for Lua.
endef

define Package/lua-openssl/install
	$(INSTALL_DIR) $(1)/usr/lib/lua
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/lib/lua/5.1/openssl.so $(1)/usr/lib/lua/
endef

$(eval $(call BuildPackage,lua-openssl))
