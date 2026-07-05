# SPDX-License-Identifier: GPL-3.0-only
#
# Copyright (C) 2021 ImmortalWrt.org

include $(TOPDIR)/rules.mk

PKG_NAME:=cloudreve
PKG_VERSION:=4.17.0
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/cloudreve/Cloudreve.git
PKG_SOURCE_VERSION:=$(PKG_VERSION)
PKG_MIRROR_HASH:=1c9859aab8ec19ae87d92c8be4623281cc3f35704cf92b18dfb939580e6acd3e

PKG_LICENSE:=GPL-3.0-only
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tianling Shen <cnsztl@immortalwrt.org>

PKG_BUILD_DEPENDS:=golang/host node/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/cloudreve/Cloudreve/v4
GO_PKG_EXCLUDES:=pkg/request/ssrftest
GO_PKG_LDFLAGS_X:= \
	$(GO_PKG)/application/constants.BackendVersion=$(PKG_VERSION) \
	$(GO_PKG)/application/constants.LastCommit=$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include $(TOPDIR)/feeds/packages/lang/golang/golang-package.mk

define Package/cloudreve
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Cloud Manager
  TITLE:=A project helps you build your own cloud in minutes
  URL:=https://cloudreve.org
  DEPENDS:=@(aarch64||arm||i386||i686||riscv64||x86_64) +ca-bundle
endef

define Package/cloudreve/description
  Self-deployed file management and sharing system, supports multiple
  storage providers.
endef

define Build/Compile
	( \
		pushd $(PKG_BUILD_DIR)/assets ; \
		npm install --prefer-offline --legacy-peer-deps --ignore-scripts ; \
		npm run build ; \
		cd .. ; \
		zip -r - assets/build > application/statics/assets.zip ; \
		popd ; \
		$(call GoPackage/Build/Compile) ; \
	)
endef

define Package/cloudreve/install
	$(call GoPackage/Package/Install/Bin,$(PKG_INSTALL_DIR))

	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/Cloudreve $(1)/usr/bin/cloudreve
endef

$(eval $(call GoBinPackage,cloudreve))
$(eval $(call BuildPackage,cloudreve))
