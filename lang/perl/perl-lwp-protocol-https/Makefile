include $(TOPDIR)/rules.mk

PKG_NAME:=perl-lwp-protocol-https
PKG_VERSION:=6.15
PKG_RELEASE:=1

METACPAN_NAME:=LWP-Protocol-https
METACPAN_AUTHOR:=OALDERS
PKG_HASH:=aa7261397b39ae202abcfc337b8307c7d2532a9b7ee721f7a87a6f25aa59608d

PKG_MAINTAINER:=Jens Wagner <jens@wagner2013.de>
PKG_LICENSE:=GPL-1.0-or-later Artistic-1.0-Perl
PKG_LICENSE_FILES:=LICENSE

include ../metacpan.mk
include $(INCLUDE_DIR)/package.mk
include ../perlmod.mk

define Package/perl-lwp-protocol-https
  SUBMENU:=Perl
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=https support for LWP::UserAgent
  URL:=https://metacpan.org/pod/LWP::Protocol::https
  DEPENDS:=perl +perlbase-essential +perl-io-socket-ssl +perl-www +perl-net-http
endef

define Package/perl-lwp-protocol-https/description
  The LWP::Protocol::https module provides support for using
  https schemed URLs with LWP::UserAgent
endef

define Build/Configure
    $(call perlmod/Configure,,)
endef

define Build/Compile
    $(call perlmod/Compile,,)
endef

define Package/perl-lwp-protocol-https/install
    $(call perlmod/Install,$(1),LWP)
endef

$(eval $(call BuildPackage,perl-lwp-protocol-https))
