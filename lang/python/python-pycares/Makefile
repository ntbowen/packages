
include $(TOPDIR)/rules.mk

PKG_NAME:=python-pycares
PKG_VERSION:=5.0.1
PKG_RELEASE:=1

PYPI_NAME:=pycares
PKG_HASH:=aa7261397b39ae202abcfc337b8307c7d2532a9b7ee721f7a87a6f25aa59608d

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Fabian Lipken <dynasticorpheus@gmail.com>

PKG_BUILD_DEPENDS:=libffi/host python-cffi/host  # cffi>=1.5.0

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-pycares
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=Python interface for c-ares
  URL:=https://github.com/saghul/pycares
  DEPENDS:=+python3-light +python3-cffi
endef

define Package/python3-pycares/description
Python interface for c-ares
endef

$(eval $(call Py3Package,python3-pycares))
$(eval $(call BuildPackage,python3-pycares))
$(eval $(call BuildPackage,python3-pycares-src))
