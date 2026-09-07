include $(TOPDIR)/rules.mk

PKG_NAME:=bubblewrap
PKG_VERSION:=0.12.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://github.com/containers/$(PKG_NAME)/releases/download/v$(PKG_VERSION)
PKG_HASH:=3f66d9e3f85396f6d7d7fbd2859f277e4fd3a9303341bfe2ff325251bda65fd4

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
