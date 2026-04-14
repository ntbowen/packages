#
# Copyright (C) 2019 CZ.NIC, z. s. p. o. (https://www.nic.cz/)
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python-sentry-sdk
PKG_VERSION:=2.58.0
PKG_RELEASE:=1

PYPI_NAME:=sentry-sdk
PYPI_SOURCE_NAME:=sentry_sdk
PKG_HASH:=aa7261397b39ae202abcfc337b8307c7d2532a9b7ee721f7a87a6f25aa59608d

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
