#
# Copyright (C) 2019 CZ.NIC, z. s. p. o. (https://www.nic.cz/)
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python-sentry-sdk
PKG_VERSION:=2.60.0
PKG_RELEASE:=1

PYPI_NAME:=sentry-sdk
PYPI_SOURCE_NAME:=sentry_sdk
PKG_HASH:=bb609c2613b84c272a672e8d472c960297221ee742011bae0d481f77a51e8d79

PKG_MAINTAINER:=Josef Schlehofer <pepe.schlehofer@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-sentry-sdk
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=Client for Sentry
  URL:=https://github.com/getsentry/sentry-python
  DEPENDS:= \
	+python3 \
	+python3-certifi \
	+python3-urllib3
endef

define Package/python3-sentry-sdk/description
This is the official Python SDK for Sentry.
endef

$(eval $(call Py3Package,python3-sentry-sdk))
$(eval $(call BuildPackage,python3-sentry-sdk))
$(eval $(call BuildPackage,python3-sentry-sdk-src))
