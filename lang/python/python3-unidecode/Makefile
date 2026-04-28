#
# Copyright (C) 2024 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python3-unidecode
PKG_VERSION:=1.4.0
PKG_RELEASE:=1

PYTHON3_PKG_WHEEL_NAME:=unidecode
PYPI_NAME:=Unidecode
PKG_HASH:=ce35985008338b676573023acc382d62c264f307c8f7963733405add37ea2b23

PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=OpenWrt Developers

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-unidecode
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=ASCII transliterations of Unicode text
  URL:=https://pypi.org/project/Unidecode/
  DEPENDS:=+python3-light
endef

define Package/python3-unidecode/description
  Unidecode takes Unicode data and tries to represent it in ASCII characters,
  useful for creating ASCII slugs from Unicode strings.
endef

$(eval $(call Py3Package,python3-unidecode))
$(eval $(call BuildPackage,python3-unidecode))
$(eval $(call BuildPackage,python3-unidecode-src))
