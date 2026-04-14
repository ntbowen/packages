include $(TOPDIR)/rules.mk

PKG_NAME:=python-zipp
PKG_VERSION:=3.23.1
PKG_RELEASE:=1

PYPI_NAME:=zipp
PKG_HASH:=aa7261397b39ae202abcfc337b8307c7d2532a9b7ee721f7a87a6f25aa59608d

PKG_MAINTAINER:=Alexandru Ardelean <ardeleanalex@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DEPENDS:=python-setuptools/host python-setuptools-scm/host

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-zipp
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=Zipfile object wrapper
  URL:=https://github.com/jaraco/zipp
  DEPENDS:=+python3-light +python3-urllib
endef

define Package/python3-zipp/description
  Backport of pathlib-compatible object wrapper for zip files
endef

$(eval $(call Py3Package,python3-zipp))
$(eval $(call BuildPackage,python3-zipp))
$(eval $(call BuildPackage,python3-zipp-src))
