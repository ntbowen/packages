#
# Copyright (C) 2008-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=netdata
PKG_VERSION:=2.10.3
PKG_RELEASE:=3

PKG_MAINTAINER:=Josef Schlehofer <pepe.schlehofer@gmail.com>, Daniel Engberg <daniel.engberg.lists@pyret.net>
PKG_LICENSE:=GPL-3.0-or-later
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:netdata:netdata

PKG_SOURCE:=$(PKG_NAME)-v$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/netdata/netdata/releases/download/v$(PKG_VERSION)
PKG_HASH:=d7f178b89dc327f4f761aeaf5bd6e76a13ba220497c64c30f587f98ddc65b42b
PKG_BUILD_DIR=$(BUILD_DIR)/$(PKG_NAME)-v$(PKG_VERSION)

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_BUILD_DEPENDS:=protobuf/host
PKG_BUILD_FLAGS:=no-mips16 gc-sections

CMAKE_INSTALL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/netdata
  SECTION:=admin
  CATEGORY:=Administration
  DEPENDS:=+zlib +libuuid +libuv +libmnl +libjson-c +libopenssl \
    +libstdcpp +libatomic +protobuf +openssl-util +curl +bash +libyaml \
    +liblz4 +libzstd
  TITLE:=Real-time performance monitoring tool
  URL:=https://www.netdata.cloud/
endef

define Package/netdata/description
  netdata is a highly optimized Linux daemon providing real-time performance
  monitoring for Linux systems, applications and SNMP devices over the web.

  If you want to use Python plugins install python3, python3-yaml and
  python3-urllib3
endef

TARGET_CFLAGS := $(filter-out -O%,$(TARGET_CFLAGS)) -O3

CMAKE_OPTIONS += \
	-DDEFAULT_FEATURE_STATE=OFF \
	-DENABLE_ML=OFF \
	-DENABLE_DBENGINE=OFF \
	-DENABLE_DASHBOARD=ON \
	-DENABLE_PLUGIN_GO=OFF \
	-DENABLE_PLUGIN_PYTHON=ON \
	-DENABLE_PLUGIN_SCRIPTS=OFF \
	-DENABLE_PLUGIN_OTEL=OFF \
	-DENABLE_EXPORTER_PROMETHEUS_REMOTE_WRITE=OFF \
	-DENABLE_EXPORTER_MONGODB=OFF \
	-DENABLE_BUNDLED_PROTOBUF=OFF \
		-DProtobuf_PROTOC_EXECUTABLE=/home/zag/OpenWrt/zagwrt/staging_dir/hostpkg/bin/protoc-35.0.0 \
	-DENABLE_BUNDLED_JSONC=OFF \
	-DENABLE_BUNDLED_YAML=OFF \
	-DENABLE_SENTRY=OFF \
	-DENABLE_WEBRTC=OFF \
	-DENABLE_MIMALLOC=OFF \
	-DENABLE_JEMALLOC=OFF \
	-DSTATIC_BUILD=OFF \
	-DUSE_MOLD=OFF \
	-DBUILD_FOR_PACKAGING=ON

define Package/netdata/conffiles
/etc/netdata/
endef

define Package/netdata/install
	$(INSTALL_DIR) $(1)/etc/netdata/custom-plugins.d
	$(CP) $(PKG_INSTALL_DIR)/usr/etc/netdata $(1)/etc
	$(CP) ./files/netdata.conf $(1)/etc/netdata
	touch $(1)/etc/netdata/.opt-out-from-anonymous-statistics
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/usr/lib/netdata $(1)/usr/lib
	$(CP) $(1)/usr/lib/netdata/conf.d/health_alarm_notify.conf $(1)/etc
	$(INSTALL_DIR) $(1)/usr/libexec
	$(CP) $(PKG_INSTALL_DIR)/usr/usr/libexec/netdata $(1)/usr/libexec
	$(CP) $(1)/usr/libexec/netdata/plugins.d/tc-qos-helper.sh $(1)/etc
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/usr/sbin/netdata $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/usr/sbin/netdatacli $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/usr/sbin/netdata-claim.sh $(1)/usr/sbin
	$(INSTALL_DIR) $(1)/usr/share/netdata
	$(CP) $(PKG_INSTALL_DIR)/usr/usr/share/netdata $(1)/usr/share
	-rm $(1)/usr/share/netdata/web/demo*html
	-rm $(1)/usr/share/netdata/web/fonts/*.svg
	-rm $(1)/usr/share/netdata/web/fonts/*.ttf
	-rm $(1)/usr/share/netdata/web/fonts/*.woff
	-rm $(1)/usr/share/netdata/web/images/*.png
	-rm $(1)/usr/share/netdata/web/images/*.gif
	-rm $(1)/usr/share/netdata/web/images/*.ico
	-rm -rf $(1)/usr/share/netdata/web/old
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/netdata.init $(1)/etc/init.d/netdata
endef

$(eval $(call BuildPackage,netdata))
