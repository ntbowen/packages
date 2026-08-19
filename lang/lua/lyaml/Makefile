include $(TOPDIR)/rules.mk

PKG_NAME:=lyaml
PKG_VERSION:=6.2.9
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/gvvaughan/lyaml/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=515e81d21903c1e428b017f2fe9dffa4e29ae9881b0d49ddbe6ba625e3164fe3

PKG_MAINTAINER:=Mathew McBride <matt@traverse.com.au>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DEPENDS:=lua/host luarocks/host HOST_OS_MACOS:fakeuname/host

include $(INCLUDE_DIR)/package.mk
ifeq ($(CONFIG_HOST_OS_MACOS),y)
    include ../../utils/fakeuname/fakeuname.mk
endif

define Package/lyaml
  SUBMENU:=Lua
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Lua lib-yaml bindings
  URL:=https://github.com/gvvaughan/lyaml
  DEPENDS:= +lua +libyaml
endef

define Package/lyaml/description
  Lua bindings for libyaml
endef

TARGET_CFLAGS += \
	-I$(STAGING_DIR)/usr/include

# Note: the duplicate environment exports are not a typo!
# First is for luarocks, then the second is for the compilation
# invoked by luarocks
# (Same setup as luaposix)
define Build/Compile
	cd $(PKG_BUILD_DIR) && \
	LUA_LIBDIR=$(STAGING_DIR)/usr/lib/lua \
	LUA_PKGNAME=lua5.1 \
	CFLAGS="$(TARGET_CFLAGS) $(FPIC)" \
	LDFLAGS="$(TARGET_LDFLAGS)" \
	CC="$(TARGET_CC)" LD="$(TARGET_CC)" \
	$(if $(CONFIG_HOST_OS_MACOS),PATH=$(FAKEUNAME_PATH):$(TARGET_PATH_PKG)) \
	luarocks make --pack-binary-rock lyaml-$(PKG_VERSION)-2.rockspec \
		LUA_LIBDIR=$(STAGING_DIR)/usr/lib/lua \
		YAML_DIR=$(STAGING_DIR)/usr \
		LUA_INCDIR=$(STAGING_DIR)/usr/include \
		LUA_PKGNAME=lua5.1 \
		CFLAGS="$(TARGET_CFLAGS) $(FPIC)" \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		CC="$(TARGET_CC)" LD="$(TARGET_CC)"
endef

define Package/lyaml/install
	$(INSTALL_DIR) $(1)/usr/lib/lua/lyaml
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/linux/yaml.so $(1)/usr/lib/lua/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/lib/lyaml/*.lua $(1)/usr/lib/lua/lyaml/
endef

$(eval $(call BuildPackage,lyaml))
