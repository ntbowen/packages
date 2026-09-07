#
# Copyright (C) 2017 Philip Prindeville <philipp@redfish-solutions.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=perl-authen-sasl
PKG_VERSION:=2.2100
PKG_RELEASE:=2

PKG_SOURCE_NAME:=Authen-SASL
PKG_SOURCE_URL:=https://www.cpan.org/authors/id/G/GB/GBARR/
PKG_SOURCE:=$(PKG_SOURCE_NAME)-$(PKG_VERSION).tar.gz
PKG_HASH:=4f0f100aeed34b52977a94b7df973e7f0b8f92d167b09f2b249bab81e8590e57

PKG_LICENSE:=GPL-1.0-or-later Artistic-1.0-Perl
PKG_MAINTAINER:=Philip Prindeville <philipp@redfish-solutions.com>

PKG_BUILD_DIR:=$(BUILD_DIR)/perl/$(PKG_SOURCE_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include ../perlmod.mk

define Package/perl-authen-sasl
  SUBMENU:=Perl
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Generic network authentication protocol framework.
  URL:=http://search.cpan.org/dist/$(PKG_SOURCE_NAME)/
  DEPENDS:=perl +perlbase-digest
endef

define Build/Configure
	$(call perlmod/Configure,,)
endef

define Build/Compile
	$(call perlmod/Compile,,)
endef

define Package/perl-authen-sasl/install
	$(call perlmod/Install,$(1),Authen)
endef


$(eval $(call BuildPackage,perl-authen-sasl))
