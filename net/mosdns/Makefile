# SPDX-License-Identifier: GPL-3.0-only
#
# Copyright (C) 2021 ImmortalWrt.org

include $(TOPDIR)/rules.mk

PKG_NAME:=mosdns
PKG_VERSION:=5.3.4
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/IrineSistiana/mosdns/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=0302a685db2a6c3c09af7bf4ff0dffd24f1e583383a47f064564f5270033671b

PKG_LICENSE:=GPL-3.0
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Tianling Shen <cnsztl@immortalwrt.org>

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

GO_PKG:=github.com/IrineSistiana/mosdns
GO_PKG_LDFLAGS_X:=main.version=v$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/mosdns
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=IP Addresses and Names
  TITLE:=A plug-in DNS forwarder/splitter
  URL:=https://github.com/IrineSistiana/mosdns
  DEPENDS:=$(GO_ARCH_DEPENDS) +ca-bundle +v2dat +v2ray-geoip +v2ray-geosite
  PROVIDES:=mosdns-neo
endef

define Package/mosdns/conffiles
/etc/mosdns/
endef

define Package/mosdns/install
	$(call GoPackage/Package/Install/Bin,$(1))

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/mosdns.init $(1)/etc/init.d/mosdns

	$(INSTALL_DIR) $(1)/etc/mosdns
	$(INSTALL_DATA) ./files/config.yaml $(1)/etc/mosdns/config.yaml
endef

define Package/mosdns/postinst
#!/bin/sh

# check if we are on real system
if [ -z "$${IPKG_INSTROOT}" ]; then
	/etc/init.d/mosdns stop >/dev/null 2>&1
	/etc/init.d/mosdns disable >/dev/null 2>&1

	# Unpack v2ray geodata to text format for mosdns v5
	mkdir -p /etc/mosdns/data
	if [ -x /usr/bin/v2dat ] && [ -f /usr/share/v2ray/geosite.dat ]; then
		v2dat unpack geosite -o /etc/mosdns/data \
			-f cn -f "geolocation-!cn" -f category-ads-all \
			/usr/share/v2ray/geosite.dat >/dev/null 2>&1
	fi
	if [ -x /usr/bin/v2dat ] && [ -f /usr/share/v2ray/geoip.dat ]; then
		v2dat unpack geoip -o /etc/mosdns/data \
			-f cn \
			/usr/share/v2ray/geoip.dat >/dev/null 2>&1
	fi
fi
exit 0
endef

$(eval $(call GoBinPackage,mosdns))
$(eval $(call BuildPackage,mosdns))
