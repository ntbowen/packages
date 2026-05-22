#
# Copyright (C) 2021 CZ.NIC, z. s. p. o. (https://www.nic.cz/)
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python-engineio
PKG_VERSION:=4.13.2
PKG_RELEASE:=1

PYPI_NAME:=python-engineio
PYPI_SOURCE_NAME:=python_engineio
PKG_HASH:=3962cad8acbab095603c8c839b448b14990fe42f4d382597f800e62d3b822121

PKG_MAINTAINER:=Alexandru Ardelean <ardeleanalex@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:python-engineio_project:python-engineio

PKG_BUILD_DEPENDS:=python-setuptools/host

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-engineio
  SUBMENU:=Python
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Engine.IO server and client
  URL:=https://github.com/miguelgrinberg/python-engineio
  DEPENDS:= \
	+python3-light \
	+python3-asyncio \
	+python3-logging \
	+python3-openssl \
	+python3-urllib \
	+python3-simple-websocket
endef

define Package/python3-engineio/description
  Python implementation of the Engine.IO realtime client and server.
endef

$(eval $(call Py3Package,python3-engineio))
$(eval $(call BuildPackage,python3-engineio))
$(eval $(call BuildPackage,python3-engineio-src))
