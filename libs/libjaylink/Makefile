include $(TOPDIR)/rules.mk

PKG_NAME:=libjaylink
PKG_VERSION:=0.5.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://gitlab.zapb.de/libjaylink/libjaylink/-/archive/$(PKG_VERSION)
PKG_HASH:=235b12d161b29d813026704f335536a6a916646ea2058302e4ca45ca2cd81325

PKG_MAINTAINER:=Paul Fertser <fercerpav@gmail.com>
PKG_LICENSE:=GPL-2.0+
PKG_LICENSE_FILES:=COPYING

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_FIXUP:=autoreconf

include $(INCLUDE_DIR)/package.mk

define Package/libjaylink
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+libusb-1.0
  TITLE:=Library to access SEGGER J-Link and compatible devices
  URL:=https://gitlab.zapb.de/libjaylink/libjaylink.git
endef

define Package/libjaylink/description
libjaylink is a shared library written in C to access SEGGER J-Link and
compatible devices.
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/libjaylink
	$(CP) $(PKG_INSTALL_DIR)/usr/include/libjaylink/*.h $(1)/usr/include/libjaylink/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libjaylink.so* $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/libjaylink.pc $(1)/usr/lib/pkgconfig/
endef

define Package/libjaylink/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libjaylink.so* $(1)/usr/lib/
endef

$(eval $(call BuildPackage,libjaylink))
