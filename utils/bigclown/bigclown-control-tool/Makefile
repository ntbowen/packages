# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=bigclown-control-tool
PKG_VERSION:=1.2.1
PKG_RELEASE:=3

PYPI_NAME:=bch
PKG_HASH:=aa7261397b39ae202abcfc337b8307c7d2532a9b7ee721f7a87a6f25aa59608d

PKG_MAINTAINER:=Josef Schlehofer <pepe.schlehofer@gmail.com> 
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

include ../../../lang/python/pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../../../lang/python/python3-package.mk

define Package/bigclown-control-tool
  SECTION:=utils
  CATEGORY:=Utilities
  SUBMENU:=BigClown
  TITLE:=BigCLown control tool
  URL:=https://github.com/bigclownlabs/bch-control-tool
  DEPENDS:=\
    +python3-click \
    +python3-click-log \
    +python3-paho-mqtt \
    +python3-pyserial \
    +python3-yaml
endef

$(eval $(call Py3Package,bigclown-control-tool))
$(eval $(call BuildPackage,bigclown-control-tool))
