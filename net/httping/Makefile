#
# Copyright (C) 2006-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=httping
PKG_VERSION:=4.4.0
PKG_RELEASE:=3
PKG_LICENSE:=GPL-3.0-or-later
PKG_LICENSE_FILES:=LICENSE

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/folkertvanheusden/HTTPing
PKG_SOURCE_VERSION:=v$(PKG_VERSION)
PKG_MIRROR_HASH:=b547b91bd61fe0d7e174e586851e47c897114c997d8a01ea39ccaf0db6415438

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk
include $(INCLUDE_DIR)/nls.mk

define Package/httping/Default
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Httping is like 'ping' but for http-requests
  URL:=https://www.vanheusden.com/httping/
  DEPENDS:=$(INTL_DEPENDS)
  MAINTAINER:=Marcin Jurkowski <marcin1j@gmail.com>
endef

define Package/httping/Default/description
	Give it an url, and it'll show you how long it takes to connect, send a
	request and retrieve the reply (only the headers). Be aware that the
	transmission across the network also takes time!
endef

define Package/httping
  $(call Package/httping/Default)
  DEPENDS+= +libopenssl
  TITLE+= (with SSL support)
  VARIANT:=ssl
endef

define Package/httping/description
	$(call Package/httping/Default/description)
	This package is built with SSL support.
endef

define Package/httping-nossl
  $(call Package/httping/Default)
  TITLE+= (without SSL support)
  VARIANT:=nossl
endef

define Package/httping-nossl/description
	$(call Package/httping/Default/description)
	This package is built without SSL support.
endef

CMAKE_OPTIONS += \
	-DENABLE_SSL=ON \
	-DENABLE_NCURSES=OFF \
	-DENABLE_FFTW=OFF

ifeq ($(BUILD_VARIANT),nossl)
	CMAKE_OPTIONS += -DENABLE_SSL=OFF
endif

define Build/Install
endef

define Package/httping/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/httping $(1)/usr/bin/
endef

Package/httping-nossl/install = $(Package/httping/install)

$(eval $(call BuildPackage,httping))
$(eval $(call BuildPackage,httping-nossl))
