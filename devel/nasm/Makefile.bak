include $(TOPDIR)/rules.mk

PKG_NAME:=nasm
PKG_VERSION:=3.01
PKG_RELEASE:=1

PKG_SOURCE_URL:=https://www.nasm.us/pub/nasm/releasebuilds/$(PKG_VERSION)/
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_HASH:=b7324cbe86e767b65f26f467ed8b12ad80e124e3ccb89076855c98e43a9eddd4

PKG_MAINTAINER:=Oskari Rauta <oskari.rauta@gmail.com>
PKG_LICENSE:=BSD-2-Clause
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:nasm:netwide_assembler

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1
PKG_FIXUP:=autoreconf

include $(INCLUDE_DIR)/package.mk

define Package/nasm
  SECTION:=devel
  CATEGORY:=Development
  TITLE:=nasm assembler
  DEPENDS:=@TARGET_x86
endef

define Package/nasm/description
  NASM, the Netwide Assembler: an assembler targeting the
  Intel x86 series of processors, with portable source.
endef

CONFIGURE_ARGS += --disable-lto --disable-werror --disable-gdb

define Package/nasm/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/ndisasm $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nasm $(1)/usr/bin/
endef

$(eval $(call BuildPackage,nasm))
