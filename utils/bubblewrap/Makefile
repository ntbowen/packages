include $(TOPDIR)/rules.mk

PKG_NAME:=bubblewrap
PKG_VERSION:=0.13.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://github.com/containers/$(PKG_NAME)/releases/download/v$(PKG_VERSION)
PKG_HASH:=caecc52894a20ad9c932af8cb2a2c1bfd251b2d085f16d6c4d0424624c6c3cf9

PKG_MAINTAINER:=Daniel Golle <daniel@makrotopia.org>
PKG_LICENSE:=LGPLv2-or-later
PKG_CPE_ID:=cpe:/a:projectatomic:bubblewrap

PKG_FORTIFY_SOURCE:=0

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

MESON_ARGS += \
	-Dbash_completion=disabled \
	-Dman=disabled \
	-Dselinux=disabled \
	-Dtests=false \
	-Dzsh_completion=disabled

define Package/bubblewrap
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Bubblewrap sandbox
  URL:=https://github.com/containers/bubblewrap
  DEPENDS:=+libcap
endef

define Package/bubblewrap/description
 Bubblewrap is a container tool for security sandboxes.
endef

define Package/bubblewrap/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(PKG_INSTALL_DIR)/usr/bin/bwrap $(1)/usr/bin
endef

$(eval $(call BuildPackage,bubblewrap))
