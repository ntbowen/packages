include $(TOPDIR)/rules.mk

PKG_NAME:=libslirp
PKG_VERSION:=4.9.1
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-v$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://gitlab.freedesktop.org/slirp/$(PKG_NAME)/-/archive/v$(PKG_VERSION)
PKG_HASH:=3970542143b7c11e6a09a4d2b50f30a133473c41f15ed0bdcc3b7a1c450d9a5c
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-v$(PKG_VERSION)

PKG_MAINTAINER:=Oskari Rauta <oskari.rauta@gmailcom>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:libslirp_project:libslirp

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk
include $(INCLUDE_DIR)/nls.mk

define Package/libslirp
  SECTION:=libs
  CATEGORY:=Libraries
  SUBMENU:=Networking
  TITLE:=user-mode networking library for virtual environments
  DEPENDS:=+glib2
  URL:=https://gitlab.freedesktop.org/slirp/libslirp
endef

define Package/libslirp/description
  libslirp is a user-mode networking library used by virtual machines,
  containers or various tools.
endef

define Package/libslirp/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libslirp.so $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libslirp.so.* $(1)/usr/lib/
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/lib $(1)/usr/include/slirp $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libslirp.so $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libslirp.so.* $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/include/slirp/** $(1)/usr/include/slirp/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/** $(1)/usr/lib/pkgconfig/
endef

$(eval $(call BuildPackage,libslirp))
