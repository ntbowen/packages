include $(TOPDIR)/rules.mk

PKG_NAME:=python-cached-property
PKG_VERSION:=2.0.1
PKG_RELEASE:=2

PYPI_NAME:=cached-property
PKG_HASH:=aa7261397b39ae202abcfc337b8307c7d2532a9b7ee721f7a87a6f25aa59608d

PKG_MAINTAINER:=Javier Marcet <javier@marcet.info>
PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=LICENSE

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-cached-property
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=A decorator for caching properties in classes
  URL:=https://github.com/pydanny/cached-property
  DEPENDS:=+python3-light
endef

define Package/python3-cached-property/description
  A decorator for caching properties in classes.
endef

$(eval $(call Py3Package,python3-cached-property))
$(eval $(call BuildPackage,python3-cached-property))
$(eval $(call BuildPackage,python3-cached-property-src))
