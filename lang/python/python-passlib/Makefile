# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.

include $(TOPDIR)/rules.mk

PKG_NAME:=python-passlib
PKG_VERSION:=1.7.4
PKG_RELEASE:=1

PYPI_NAME:=passlib
PKG_HASH:=defd50f72b65c5402ab2c573830a6978e5f202ad0d984793c8dde2c4152ebe04

PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=LICENSE

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-passlib
  SUBMENU:=Python
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Comprehensive password hashing framework
  URL:=https://passlib.readthedocs.io/
  DEPENDS:=+python3
endef

define Package/python3-passlib/description
Passlib is a password hashing library for Python 2 & 3, which provides
cross-platform implementations of over 30 password hashing algorithms,
as well as a framework for managing existing password hashes. It's
designed to be useful for a wide range of tasks, from verifying a hash
found in /etc/shadow, to providing full-strength password hashing for
multi-user applications.
endef

# 添加清理步骤以解决文件已存在的错误
define Build/Prepare
	$(call Build/Prepare/Default)
	# 确保目标目录存在
	mkdir -p $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/
	# 删除可能存在的冲突文件
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/passlib
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/passlib-*.egg-info
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/passlib-*.dist-info
endef

# 修改 Build/Compile 阶段，确保在编译前清理
define Build/Compile
	# 删除可能存在的冲突文件
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/passlib
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/passlib-*.egg-info
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/passlib-*.dist-info
	
	$(call Py3Build/Compile/Default)
endef

# 修改 Build/Install 阶段，确保在安装前清理
define Build/Install
	# 安装前删除可能存在的冲突文件
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/passlib
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/passlib-*.egg-info
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/python$(PYTHON3_VERSION)/site-packages/passlib-*.dist-info
	
	$(call Py3Build/Install/Default)
endef

$(eval $(call Py3Package,python3-passlib))
$(eval $(call BuildPackage,python3-passlib))
$(eval $(call BuildPackage,python3-passlib-src))
