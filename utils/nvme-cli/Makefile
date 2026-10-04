# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2023 Luca Barbato

include $(TOPDIR)/rules.mk

PKG_NAME:=nvme-cli
PKG_VERSION:=3.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/linux-nvme/nvme-cli/archive/refs/tags/v$(PKG_VERSION)
PKG_HASH:=8a6879f1f2af53743c03a8f77f247261306300d3f3b0edfd242c5f646bdfdccb

PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Luca Barbato <lu_zero@luminem.org>

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

define Package/nvme-cli
  SECTION:=utils
  CATEGORY:=Utilities
  SUBMENU:=Disc
  TITLE:=NVMe management command line interface
  URL:=https://nvmexpress.org/
  DEPENDS:=+libnvme 
endef

define Package/nvme-cli/description
  NVM-Express user space tooling for Linux.
endef

define Package/nvme-cli/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(CP) $(PKG_INSTALL_DIR)/usr/sbin/nvme $(1)/usr/sbin/
endef

$(eval $(call BuildPackage,nvme-cli))
