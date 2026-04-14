include $(TOPDIR)/rules.mk

PKG_NAME:=perl-net-http
PKG_VERSION:=6.24
PKG_RELEASE:=1

METACPAN_NAME:=Net-HTTP
METACPAN_AUTHOR:=OALDERS
PKG_HASH:=aa7261397b39ae202abcfc337b8307c7d2532a9b7ee721f7a87a6f25aa59608d

PKG_MAINTAINER:=Jens Wagner <jens@wagner2013.de>
PKG_LICENSE:=GPL-1.0-or-later Artistic-1.0-Perl
PKG_LICENSE_FILES:=LICENSE

include ../metacpan.mk
include $(INCLUDE_DIR)/package.mk
include ../perlmod.mk

define Package/perl-net-http
  SUBMENU:=Perl
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Low-level HTTP connection (client)
  URL:=https://metacpan.org/pod/Net::HTTP
  DEPENDS:=perl +perlbase-compress +perlbase-essential +perlbase-io +perl-uri
endef

define Package/perl-net-http/description
  The Net::HTTP class is a low-level HTTP client.
endef

define Build/Configure
    $(call perlmod/Configure,,)
endef

define Build/Compile
    $(call perlmod/Compile,,)
endef

define Package/perl-net-http/install
    $(call perlmod/Install,$(1),Net)
endef

$(eval $(call BuildPackage,perl-net-http))
