#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PECL_NAME:=raphf
PECL_LONGNAME:=Resource and persistent handles factory

PKG_VERSION:=2.0.2
PKG_RELEASE:=2
PKG_HASH:=7e782fbe7b7de2b5f1c43f49d9eb8c427649b547573564c78baaf2b8f8160ef4

PKG_NAME:=php8-pecl-raphf
PKG_SOURCE:=$(PECL_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://pecl.php.net/get/

PKG_MAINTAINER:=Michael Heimpold <mhei@heimpold.de>

PKG_LICENSE:=BSD-2-Clause
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DIR:=$(BUILD_DIR)/pecl-php8/$(PECL_NAME)-$(PKG_VERSION)
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk
include ../php8/pecl.mk

CONFIGURE_ARGS+= \
	--enable-raphf

define Build/InstallDev
	mkdir -p $(STAGING_DIR)/usr/include/php8/ext/$(PECL_NAME)
	cp $(PKG_BUILD_DIR)/php_raphf.h $(STAGING_DIR)/usr/include/php8/ext/$(PECL_NAME)/
	cp $(PKG_BUILD_DIR)/php_raphf_api.h $(STAGING_DIR)/usr/include/php8/ext/$(PECL_NAME)/
endef

$(eval $(call PHP8PECLPackage,$(PECL_NAME),$(PECL_LONGNAME)))
$(eval $(call BuildPackage,$(PKG_NAME)))
