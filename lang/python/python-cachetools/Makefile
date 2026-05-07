#
# Copyright (C) 2019 CZ.NIC, z. s. p. o. (https://www.nic.cz/)
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python-cachetools
PKG_VERSION:=7.1.1
PKG_RELEASE:=1

PYPI_NAME:=cachetools
PKG_HASH:=32ed63159c77e21ee19ca1b9aa3213ccf0218eb59539560b132a8e68ef0e18ea

PKG_MAINTAINER:=Josef Schlehofer <pepe.schlehofer@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DEPENDS:=python-setuptools/host

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-cachetools
  SUBMENU:=Python
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Extensible memoizing collections and decorators
  URL:=https://github.com/tkem/cachetools
  DEPENDS:=+python3-light
endef

define Package/python3-cachetools/description
This module provides various memoizing collections and decorators,
including variants of the Python 3 Standard Library's @lru_cache
function decorator.
endef

$(eval $(call Py3Package,python3-cachetools))
$(eval $(call BuildPackage,python3-cachetools))
$(eval $(call BuildPackage,python3-cachetools-src))
