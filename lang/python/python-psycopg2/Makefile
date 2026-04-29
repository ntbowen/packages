#
# Copyright (C) 2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python-psycopg2
PKG_VERSION:=2.9.12
PKG_RELEASE:=1

PYPI_NAME:=psycopg2
PKG_HASH:=32ed63159c77e21ee19ca1b9aa3213ccf0218eb59539560b132a8e68ef0e18ea

PKG_MAINTAINER:=Daniel Danzberger <daniel@dd-wrt.com>
PKG_LICENSE:=LGPL-3.0-or-later
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DEPENDS:= \
	python-setuptools/host

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-psycopg2
  SUBMENU:=Python
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=PostgreSQL database adapter
  URL:=https://www.psycopg.org/
  DEPENDS:= \
    +python3-light \
    +python3-decimal \
    +python3-logging \
    +python3-uuid \
    +libpq
endef

define Package/python3-psycopg2/description
Psycopg is the most popular PostgreSQL adapter for the Python
programming language
endef

$(eval $(call Py3Package,python3-psycopg2))
$(eval $(call BuildPackage,python3-psycopg2))
$(eval $(call BuildPackage,python3-psycopg2-src))
