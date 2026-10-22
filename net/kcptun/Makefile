include $(TOPDIR)/rules.mk

PKG_NAME:=kcptun
PKG_VERSION:=20261018
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/smithwhere/kcptun/tar.gz/v${PKG_VERSION}?
PKG_HASH:=c3ef2508c28862cbe83ed0124c6860dad15e868750fc4094bb7144fe3513a282

PKG_MAINTAINER:=Dengfeng Liu <liudf0716@gmail.com>, Chao Liu <expiron18@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE.md

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/smithwhere/kcptun

GO_PKG_LDFLAGS_X:=main.VERSION=$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/kcptun-config
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  TITLE:=Kcptun Config Scripts
  URL:=https://github.com/smithwhere/kcptun
  DEPENDS:=$(GO_ARCH_DEPENDS)
endef

define Package/kcptun-config/conffiles
/etc/config/kcptun
endef

define Package/kcptun-config/install
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/kcptun.config $(1)/etc/config/kcptun
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/kcptun.init $(1)/etc/init.d/kcptun
endef

define Package/kcptun/Default
  define Package/kcptun-$(1)
    SECTION:=net
    CATEGORY:=Network
    SUBMENU:=Web Servers/Proxies
    TITLE:=KCP-based Secure Tunnel $(1)
    URL:=https://github.com/smithwhere/kcptun
    DEPENDS:=+kcptun-config
  endef

  define Package/kcptun-$(1)/description
    kcptun is a Stable & Secure Tunnel Based On KCP with N:M Multiplexing.
This package only contains kcptun $(1).
  endef

  define Package/kcptun-$(1)/install
		$$(call GoPackage/Package/Install/Bin,$$(PKG_INSTALL_DIR))

		$$(INSTALL_DIR) $$(1)/usr/bin
		$$(INSTALL_BIN) $$(PKG_INSTALL_DIR)/usr/bin/$(1) $$(1)/usr/bin/kcptun-$(1)
  endef
endef

$(eval $(call BuildPackage,kcptun-config))
KCPTUN_COMPONENTS:=server client
$(foreach component,$(KCPTUN_COMPONENTS), \
  $(eval $(call Package/kcptun/Default,$(component))) \
  $(eval $(call GoBinPackage,kcptun-$(component))) \
  $(eval $(call BuildPackage,kcptun-$(component))) \
)
