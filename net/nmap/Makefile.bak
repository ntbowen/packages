include $(TOPDIR)/rules.mk

PKG_NAME:=nmap
PKG_VERSION:=7.991
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://nmap.org/dist/
PKG_HASH:=a5d507f29437bef3bedd4771ff9aaa8fc1c2a109ddba1f5b1cf12027456929be

PKG_LICENSE:=NPSL-0.94-or-NPSL-0.95
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:nmap:nmap

PKG_BUILD_PARALLEL:=1
PKG_BUILD_DEPENDS:=python-setuptools/host
PKG_INSTALL:=1
PYTHON3_PKG_BUILD:=0
PYTHON3_PKG_FORCE_DISTUTILS_SETUP:=1

include $(INCLUDE_DIR)/package.mk
include ../../lang/python/python3-package.mk

NMAP_DEPENDS:=+libpcap +libstdcpp +zlib +libpcre2
NCAT_DEPENDS:=+libpcap
NPING_DEPENDS:=+libpcap +libpthread +libstdcpp
SSL_DEPENDS:=+libopenssl +ca-certs
SSH2_DEPENDS:=+libssh2
LUA_DEPENDS:=+liblua5.4

define Package/nmap/default
  SECTION:=net
  CATEGORY:=Network
  URL:=https://nmap.org/
endef

define Package/nmap
$(call Package/nmap/default)
  DEPENDS:=$(NMAP_DEPENDS) $(SSL_DEPENDS) $(SSH2_DEPENDS) $(LUA_DEPENDS)
  TITLE:=Network exploration utility and security/port scanner
endef

define Package/nmap-ssl
$(call Package/nmap/default)
  DEPENDS:=$(NMAP_DEPENDS) $(SSL_DEPENDS) $(SSH2_DEPENDS) $(LUA_DEPENDS)
  VARIANT:=ssl
  TITLE:=Network exploration utility and security/port scanner (with OpenSSL support)
endef

define Package/nmap-full
$(call Package/nmap/default)
  DEPENDS:=$(NMAP_DEPENDS) $(SSL_DEPENDS) $(SSH2_DEPENDS) $(LUA_DEPENDS)
  VARIANT:=full
  TITLE:=Network exploration utility and security/port scanner (with OpenSSL and Ncat)
endef

define Package/ncat
$(call Package/nmap/default)
  DEPENDS:=$(NCAT_DEPENDS)
  TITLE:=Ncat - Networking utility
endef

define Package/ncat-ssl
$(call Package/nmap/default)
  DEPENDS:=$(NCAT_DEPENDS) $(SSL_DEPENDS) $(LUA_DEPENDS)
  VARIANT:=ssl
  TITLE:=Ncat - Networking utility (with OpenSSL support)
endef

define Package/ncat-full
$(call Package/nmap/default)
  DEPENDS:=$(NCAT_DEPENDS) $(SSL_DEPENDS) $(LUA_DEPENDS)
  VARIANT:=full
  TITLE:=Ncat - Networking utility (with OpenSSL and full features)
endef

define Package/nping
$(call Package/nmap/default)
  DEPENDS:=$(NPING_DEPENDS) $(SSL_DEPENDS)
  TITLE:=Nping - Network packet generation tool
endef

define Package/nping-ssl
$(call Package/nmap/default)
  DEPENDS:=$(NPING_DEPENDS) $(SSL_DEPENDS)
  VARIANT:=ssl
  TITLE:=Nping (with OpenSSL support)
endef

CONFIGURE_ARGS += \
	--with-libdnet=included \
	--with-liblinear=included \
	--with-libpcap="$(STAGING_DIR)/usr" \
	--with-libpcre="$(STAGING_DIR)/usr" \
	--with-libz="$(STAGING_DIR)/usr" \
	--with-ncat \
	--without-localdirs \
	--without-ndiff \
	--without-zenmap

ifeq ($(BUILD_VARIANT),full)
CONFIGURE_ARGS += \
	--with-liblua="$(STAGING_DIR)/usr" \
	--with-libssh2="$(STAGING_DIR)/usr" \
	--with-openssl="$(STAGING_DIR)/usr"
endif

ifeq ($(BUILD_VARIANT),ssl)
CONFIGURE_ARGS += \
	--with-openssl="$(STAGING_DIR)/usr" \
	--with-libssh2="$(STAGING_DIR)/usr" \
	--without-nping
endif

CONFIGURE_VARS += \
	ac_cv_dnet_bsd_bpf=no

# ndiff removed in nmap 7.99 - skip Python build
# PYTHON3_PKG_SETUP_DIR:=ndiff
# PYTHON3_PKG_SETUP_ARGS:=

define Build/Compile
	$(call Build/Compile/Default,)
endef

define Package/nmap/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nmap $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/share/nmap
	$(CP) $(PKG_INSTALL_DIR)/usr/share/nmap/* $(1)/usr/share/nmap/
endef

Package/nmap-ssl/install=$(Package/nmap/install)
Package/nmap-full/install=$(Package/nmap/install)

define Package/ncat/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/ncat $(1)/usr/bin/
endef

Package/ncat-ssl/install=$(Package/ncat/install)
Package/ncat-full/install=$(Package/ncat/install)

define Package/nping/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/nping $(1)/usr/bin/
endef

Package/nping-ssl/install=$(Package/nping/install)

$(eval $(call BuildPackage,nmap))
$(eval $(call BuildPackage,nmap-ssl))
$(eval $(call BuildPackage,nmap-full))
$(eval $(call BuildPackage,ncat))
$(eval $(call BuildPackage,ncat-ssl))
$(eval $(call BuildPackage,ncat-full))
$(eval $(call BuildPackage,nping))
$(eval $(call BuildPackage,nping-ssl))
