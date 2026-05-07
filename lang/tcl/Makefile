#
# Copyright (C) 2006-2018 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=tcl
TCL_MAJOR_VERSION:=9.1
PKG_VERSION:=${TCL_MAJOR_VERSION}a1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)$(PKG_VERSION)-src.tar.gz
PKG_SOURCE_URL:=@SF/$(PKG_NAME)
PKG_HASH:=11a03605ed3535ab7c7b49610f8efc56928bdc5210aa4c4fd1f5412cdeb3f670
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)$(PKG_VERSION)
HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/$(PKG_NAME)$(PKG_VERSION)

PKG_MAINTAINER:=Joe Mistachkin <joe@mistachkin.com>
PKG_LICENSE:=TCL
PKG_LICENSE_FILES:=license.terms
PKG_CPE_ID:=cpe:/a:tcl:tcl

PKG_BUILD_DEPENDS:=HOST_OS_MACOS:fakeuname/host
PKG_FIXUP:=autoreconf
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
HOST_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk

ifeq ($(CONFIG_HOST_OS_MACOS),y)
    include ../../utils/fakeuname/fakeuname.mk
endif

define Package/tcl
  SUBMENU:=Tcl
  SECTION:=lang
  CATEGORY:=Languages
  DEPENDS:=+libpthread +zlib
  TITLE:=The Tcl language
  URL:=https://www.tcl.tk/
endef

define Package/tcl/description
  Tcl, or Tool Command Language, is a an elegant, versatile, feature-rich,
  simple-to-learn yet very powerful industrial-strength open-source
  programming language and development platform. It is renowned for its
  stability and utility, and its emphasis on providing a cross-platform
  programming API makes it an ideal choice for an enormous variety of
  programming jobs.
endef

CONFIGURE_PATH := unix

CONFIGURE_VARS += \
	$(if $(CONFIG_HOST_OS_MACOS),PATH=$(FAKEUNAME_PATH):$(PATH)) \
	tcl_cv_strtod_unbroken=ok

CONFIGURE_ARGS += \
	--enable-threads

MAKE_PATH := unix

define Build/Prepare
	$(call Build/Prepare/Default)
	rm -rf $(PKG_BUILD_DIR)/pkgs/*
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/*.h $(1)/usr/include/

	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/libtcl*.{a,so*} $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/tclConfig.sh $(1)/usr/lib/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/tclooConfig.sh $(1)/usr/lib/

	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/tcl.pc \
		$(1)/usr/lib/pkgconfig
	$(SED) 's,/usr/include,$$$${prefix}/include,g' $(1)/usr/lib/pkgconfig/tcl.pc
	$(SED) 's,/usr/lib,$$$${prefix}/lib,g' $(1)/usr/lib/pkgconfig/tcl.pc
	# 安装私有头文件到 generic/ 子目录，供 expect 等 TEA 扩展使用
	$(INSTALL_DIR) $(1)/usr/include/generic
	$(CP) $(PKG_BUILD_DIR)/generic/*.h $(1)/usr/include/generic/ 2>/dev/null || true
	$(CP) $(PKG_BUILD_DIR)/*.h $(1)/usr/include/generic/ 2>/dev/null || true
	$(CP) $(PKG_BUILD_DIR)/unix/tclUnixPort.h $(1)/usr/include/generic/ 2>/dev/null || true
	# 修正 tclConfig.sh：TCL_SRC_DIR 指向 usr/include，使 generic/tclInt.h 可被 TEA 找到
	$(SED) 's|TCL_SRC_DIR=.*|TCL_SRC_DIR=$(STAGING_DIR)/usr/include|' $(1)/usr/lib/tclConfig.sh
endef

define Package/tcl/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/*.so $(1)/usr/lib

	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) $(PKG_INSTALL_DIR)/usr/bin/* $(1)/usr/bin

	$(LN) tclsh$(TCL_MAJOR_VERSION) $(1)/usr/bin/tclsh
endef

define Host/Configure
	$(call Host/Configure/Default,$(1),$(2),$(CONFIGURE_PATH)/$(3))
endef

define Host/Compile
	+$(HOST_MAKE_VARS) \
	$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR)/$(MAKE_PATH) \
		$(HOST_MAKE_FLAGS) \
		$(1)
endef

define Host/Install
	$(call Host/Compile,install)
	(cd $(HOST_BUILD_PREFIX)/bin; test -f tclsh || ln -s tclsh$(TCL_MAJOR_VERSION) tclsh)
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,tcl))
