include $(TOPDIR)/rules.mk

PKG_NAME:=exfatprogs
PKG_VERSION:=1.4.3
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://github.com/exfatprogs/exfatprogs/releases/download/$(PKG_VERSION)
PKG_HASH:=d745a0e7f97e1c36fad97148062ba4c496724e212d6075ff3129355d9054e218

PKG_MAINTAINER:=Daniel Golle <daniel@makrotopia.org>
PKG_LICENSE:=GPL-2.0-only
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:namjaejeon:exfatprogs

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/exfatprogs/Default
  SECTION:=utils
  CATEGORY:=Utilities
  SUBMENU:=Filesystem
  URL:=https://github.com/exfatprogs/exfatprogs
endef

define Package/exfat-mkfs
  $(Package/exfatprogs/Default)
  TITLE:=Utility for creating an exFAT File System
  DEPENDS:=+libblkid +musl-fts
endef

define Package/exfat-fsck
  $(Package/exfatprogs/Default)
  TITLE:=Utility for checking/repairing an exFAT File System
  DEPENDS:=+musl-fts
endef

CONFIGURE_ARGS += \
	--enable-shared \
	--disable-static

define Package/exfat-mkfs/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/mkfs.exfat $(1)/usr/sbin
endef

define Package/exfat-fsck/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/fsck.exfat $(1)/usr/sbin
endef

$(eval $(call BuildPackage,exfat-mkfs))
$(eval $(call BuildPackage,exfat-fsck))
