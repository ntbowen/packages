include $(TOPDIR)/rules.mk

PKG_NAME:=python-botocore
PKG_VERSION:=1.43.7
PKG_RELEASE:=1

PYPI_NAME:=botocore
PKG_HASH:=900a1673d7491fcc0e87b0f33e19990d9ae18c19368e0224ef302da323fc9b6d

PKG_MAINTAINER:=Daniel Danzberger <daniel@dd-wrt.com>
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE.txt

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-botocore
  SUBMENU:=Python
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Low-level, data-driven core of boto 3
  URL:=https://github.com/boto/botocore
  DEPENDS:=+python3 \
	+python3-dateutil \
	+python3-jmespath \
	+python3-urllib3
endef

define Package/python3-botocore/description
A low-level interface to a growing number of Amazon Web Services. The
botocore package is the foundation for the AWS CLI as well as boto3.
endef

$(eval $(call Py3Package,python3-botocore))
$(eval $(call BuildPackage,python3-botocore))
$(eval $(call BuildPackage,python3-botocore-src))
