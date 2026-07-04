include $(TOPDIR)/rules.mk

PKG_NAME:=nexttrace
PKG_VERSION:=1.7.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/nxtrace/NTrace-core/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=ac5c3f4181b061b8fff2430e2b34eee165e7a8f41eb694a07ab0b4b219e5a4bb
PKG_BUILD_DIR:=$(BUILD_DIR)/NTrace-core-$(PKG_VERSION)

PKG_LICENSE:=GPL-3.0-only
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Bradford Zhang <zyc@zyc.name>

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/nxtrace/NTrace-core
GO_PKG_LDFLAGS:=-checklinkname=0
GO_PKG_LDFLAGS_X:=$(GO_PKG)/config.Version=v$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/nexttrace
  SECTION:=net
  CATEGORY:=Network
  TITLE:=An open source visual route tracking CLI tool
  URL:=https://github.com/nxtrace/NTrace-core
  DEPENDS:=$(GO_ARCH_DEPENDS) +ca-bundle
endef

define Package/nexttrace/description
  An open source visual route tracking CLI tool
endef

define Package/nexttrace/install
	$(call GoPackage/Package/Install/Bin,$(PKG_INSTALL_DIR))
	$(INSTALL_DIR) $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/NTrace-core $(1)/usr/bin/nexttrace
endef

$(eval $(call GoBinPackage,nexttrace))
$(eval $(call BuildPackage,nexttrace))
