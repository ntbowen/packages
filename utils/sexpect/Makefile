
include $(TOPDIR)/rules.mk

PKG_NAME:=sexpect
PKG_VERSION:=2.4.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/clarkwang/sexpect/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=8f7a989ecf67f4ff6eb167d29a8e2e54db85143ebf3e8f904ea16ee5576a4b0d

PKG_MAINTAINER:=Clark Wang <dearvoid@gmail.com>
PKG_LICENSE:=GPL-3.0-only
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/sexpect
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Expect for Shells
  URL:=https://github.com/clarkwang/sexpect
endef

define Package/sexpect/description
  Sexpect is another implementation of Expect which is specifically designed
  for shells. It's lightweight and has no dependency on other packages.
endef

define Build/Compile
	$(TARGET_CC) $(TARGET_CFLAGS) -D_GNU_SOURCE $(TARGET_LDFLAGS) -Wall \
	    $(PKG_BUILD_DIR)/*.c -o $(PKG_BUILD_DIR)/$(PKG_NAME)
endef

define Package/sexpect/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/sexpect $(1)/usr/bin/
endef

$(eval $(call BuildPackage,sexpect))
