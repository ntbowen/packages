#
# Copyright (C) 2007-2011 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=ocserv
PKG_VERSION:=1.4.2
PKG_RELEASE:=3
PKG_BUILD_FLAGS:=no-mips16

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://www.infradead.org/ocserv/download
PKG_HASH:=e35d748a5244b10be3a92ad4df95a534c8280c43680eecaf3cb1b20d2a22b1a5

PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=COPYING

PKG_BUILD_PARALLEL:=1

PKG_CONFIG_DEPENDS:= \
	CONFIG_OCSERV_PAM \
	CONFIG_OCSERV_SECCOMP \
	CONFIG_OCSERV_PROTOBUF \

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

define Package/ocserv/config
	source "$(SOURCE)/Config.in"
endef

define Package/ocserv
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=VPN
  TITLE:=OpenConnect VPN server
  URL:=http://www.infradead.org/ocserv/
  MAINTAINER:=Nikos Mavrogiannopoulos <n.mavrogiannopoulos@gmail.com>
  DEPENDS:= +USE_GLIBC:libcrypt-compat +OCSERV_RADIUS:libradcli +OCSERV_SECCOMP:libseccomp +libgnutls +certtool +libncurses +libreadline +OCSERV_PAM:libpam +OCSERV_PROTOBUF:libprotobuf-c +OCSERV_LIBOATH:liboath +libev +kmod-tun
  USERID:=ocserv=72:ocserv=72
endef

define Package/ocserv/description
 OpenConnect server (ocserv) is an SSL VPN server. Its purpose is to be
 a secure, small, fast and configurable VPN server. It implements the
 OpenConnect SSL VPN protocol, and has also (currently experimental)
 compatibility with clients using the AnyConnect SSL VPN protocol. The
 OpenConnect VPN protocol uses the standard IETF security protocols such
 as TLS 1.2, and Datagram TLS to provide the secure VPN service.
endef

MESON_ARGS += \
	-Dlibnl=disabled \
	-Dgssapi=disabled \
	-Dmaxmind=disabled \
	-Dlz4=disabled \
	-Dlocal-talloc=true \
	-Dlocal-llhttp=true \
	-Dlocal-pcl=true \
	-Dsystemd=disabled \
	-Dpager= \
	-Doidc-auth=disabled \
	-Dlibwrap=disabled \
	-Dwith-werror=false

ifeq ($(CONFIG_OCSERV_PAM),y)
MESON_ARGS += -Dpam=enabled
else
MESON_ARGS += -Dpam=disabled
endif

ifeq ($(CONFIG_OCSERV_RADIUS),y)
MESON_ARGS += -Dradius=enabled
else
MESON_ARGS += -Dradius=disabled
endif

ifeq ($(CONFIG_OCSERV_SECCOMP),y)
MESON_ARGS += -Dseccomp=enabled
else
MESON_ARGS += -Dseccomp=disabled
endif

ifeq ($(CONFIG_OCSERV_PROTOBUF),y)
MESON_ARGS += -Dlocal-protobuf=false
else
MESON_ARGS += -Dlocal-protobuf=true
endif

ifdef CONFIG_OCSERV_LIBOATH
MESON_ARGS += -Dliboath=enabled
else
MESON_ARGS += -Dliboath=disabled
endif

define Package/ocserv/conffiles
/etc/config/ocserv
endef

define Package/ocserv/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/ocserv $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/ocserv-worker $(1)/usr/sbin/
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/ocpasswd $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/occtl $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/libexec
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/libexec/ocserv-fw $(1)/usr/libexec/
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/ocserv.init $(1)/etc/init.d/ocserv
	$(INSTALL_DIR) $(1)/etc/ocserv
	$(INSTALL_CONF) ./files/ocserv.conf.template $(1)/etc/ocserv/ocserv.conf.template
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/config $(1)/etc/config/ocserv
	$(INSTALL_DIR) $(1)/lib/upgrade/keep.d
	$(INSTALL_DATA) ./files/ocserv.upgrade $(1)/lib/upgrade/keep.d/ocserv
endef

$(eval $(call BuildPackage,ocserv))
