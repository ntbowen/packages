#
# Copyright (C) 2007-2017 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python-chardet
PKG_VERSION:=5.2.0
PKG_RELEASE:=2
PKG_LICENSE:=LGPL-2.1

PYPI_NAME:=chardet
PKG_HASH:=1b3b6ff479a8c414bc3fa2c0852995695c4a026dcd6d0633b2dd092ca39c1cf7

HOST_BUILD_DEPENDS:=python3/host python-build/host python-installer/host python-wheel/host

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/host-build.mk
include ../python3-package.mk
include ../python3-host-build.mk

define Package/python3-chardet
  SUBMENU:=Python
  SECTION:=lang
  CATEGORY:=Languages
  MAINTAINER:=Alexandru Ardelean <ardeleanalex@gmail.com>
  URL:=https://github.com/chardet/chardet
  TITLE:=Universal encoding detector
  DEPENDS:=+python3-light +python3-logging
endef

define Package/python3-chardet/description
  Universal encoding detector for Python
endef

# 添加 Build/Prepare 阶段，确保在开始前清理可能存在的冲突文件
define Build/Prepare
	$(call Build/Prepare/Default)
	# 确保目标目录存在
	mkdir -p $(PKG_INSTALL_DIR)/usr/bin/
	mkdir -p $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/
	# 删除可能存在的冲突文件
	rm -f $(PKG_INSTALL_DIR)/usr/bin/chardetect
	rm -f $(PKG_INSTALL_DIR)/usr/bin/chardetect3
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet-*.egg-info
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet-*.dist-info
endef

# 修改 Build/Compile 阶段
define Build/Compile
	# 删除可能存在的冲突文件
	rm -f $(PKG_INSTALL_DIR)/usr/bin/chardetect
	rm -f $(PKG_INSTALL_DIR)/usr/bin/chardetect3
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet-*.egg-info
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet-*.dist-info
	
	$(call Py3Build/Compile/Default)
	
	# 编译后再次删除可能存在的冲突文件
	rm -f $(PKG_INSTALL_DIR)/usr/bin/chardetect
	rm -f $(PKG_INSTALL_DIR)/usr/bin/chardetect3
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet-*.egg-info
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet-*.dist-info
endef

# 修改 Build/Install 阶段
define Build/Install
	# 安装前删除可能存在的冲突文件
	rm -f $(PKG_INSTALL_DIR)/usr/bin/chardetect
	rm -f $(PKG_INSTALL_DIR)/usr/bin/chardetect3
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet-*.egg-info
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/chardet-*.dist-info
	
	# 使用 pip 直接安装而不是通过 installer 模块
	cd "$(PKG_BUILD_DIR)" && \
	$(HOST_PYTHON3_PIP) install --root="$(PKG_INSTALL_DIR)" --prefix="/usr" --ignore-installed .
endef

define Py3Package/python3-chardet/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(PKG_INSTALL_DIR)/usr/bin/* $(1)/usr/bin/
	for bin in $(1)/usr/bin/*; do \
		mv $$$$bin $$$${bin}3 ; \
		$(LN) $$$${bin##*/}3 $$$$bin ; \
	done
endef

$(eval $(call Py3Package,python3-chardet))
$(eval $(call BuildPackage,python3-chardet))
$(eval $(call BuildPackage,python3-chardet-src))
$(eval $(call HostBuild))
