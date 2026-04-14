#
# Copyright (C) 2019 Yousong Zhou <yszhou4tech@gmail.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=spice
PKG_VERSION:=0.16.0
PKG_RELEASE:=3

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://www.spice-space.org/download/releases/spice-server
PKG_HASH:=0a6ec9528f05371261bbb2d46ff35e7b5c45ff89bb975a99af95a5f20ff4717d

PKG_MAINTAINER:=Yousong Zhou <yszhou4tech@gmail.com>
PKG_LICENSE:=LGPL-2.1-only
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:spice_project:spice

PKG_BUILD_DEPENDS:=spice-protocol

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk
include $(INCLUDE_DIR)/meson.mk

define Package/libspice-server
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=SPICE server library
  URL:=https://www.spice-space.org/index.html
  DEPENDS:=+glib2 +libjpeg +libopenssl +pixman +zlib +libstdcpp
endef

# audio codec
MESON_ARGS += \
	-Dopus=disabled

MESON_ARGS += \
	-Dgstreamer=no \
	-Dlz4=false \
	-Dsasl=false \
	-Dsmartcard=disabled \
	-Dalignment-checks=false \
	-Dextra-checks=false \
	-Dstatistics=false \
	-Dmanual=false \
	-Dinstrumentation=no \
	-Dtests=false

MESON_ARGS += \
	-Dspice-common:alignment-checks=false \
	-Dspice-common:extra-checks=false \
	-Dspice-common:opus=disabled \
	-Dspice-common:instrumentation=no \
	-Dspice-common:smartcard=disabled \
	-Dspice-common:python-checks=false \
	-Dspice-common:manual=false \
	-Dspice-common:generate-code=none \
	-Dspice-common:tests=false \

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/spice-server
	$(CP) $(PKG_INSTALL_DIR)/usr/include/spice-server/*.h $(1)/usr/include/spice-server
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libspice-server.* $(1)/usr/lib
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/spice-server.pc $(1)/usr/lib/pkgconfig
endef

define Package/libspice-server/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libspice-server.so* $(1)/usr/lib
endef

$(eval $(call BuildPackage,libspice-server))
