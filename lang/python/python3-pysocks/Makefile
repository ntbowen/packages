#
# Copyright (C) 2024 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python3-pysocks
PKG_VERSION:=1.7.1
PKG_RELEASE:=1

PYPI_NAME:=PySocks
PKG_HASH:=3f8804571ebe159c380ac6de37643bb4685970655d3bba243530d6558b799aa0

PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=OpenWrt Developers

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-pysocks
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=SOCKS proxy client for Python
  URL:=https://github.com/Anorov/PySocks
  DEPENDS:=+python3-light
endef

define Package/python3-pysocks/description
  PySocks lets you send traffic through SOCKS and HTTP proxy servers.
  It is a modern fork of SocksiPy with bug fixes and extra features.
endef

$(eval $(call Py3Package,python3-pysocks))
$(eval $(call BuildPackage,python3-pysocks))
$(eval $(call BuildPackage,python3-pysocks-src))
