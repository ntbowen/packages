include $(TOPDIR)/rules.mk

PKG_NAME:=yt-dlp
PKG_VERSION:=2025.4.30
PKG_RELEASE:=1

PYPI_NAME:=yt-dlp
PKG_HASH:=d01367d0c3ae94e35cb1e2eccb7a7c70e181c4ca448f4ee2374f26489d263603
PYPI_SOURCE_NAME:=yt_dlp

PKG_MAINTAINER:=George Sapkin <george@sapk.in>
PKG_LICENSE:=Unlicense
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:yt-dlp_project:yt-dlp

PKG_BUILD_DEPENDS:=python-hatchling/host

include ../../lang/python/pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../../lang/python/python3-package.mk

define Package/yt-dlp
  SECTION:=multimedia
  CATEGORY:=Multimedia
  TITLE:=A feature-rich command-line audio/video downloader
  URL:=https://github.com/yt-dlp/yt-dlp
  PROVIDES:=youtube-dl
  DEPENDS:= \
    +ca-certificates \
    +python3-asyncio \
    +python3-codecs \
    +python3-ctypes \
    +python3-email \
    +python3-logging \
    +python3-openssl \
    +python3-sqlite3 \
    +python3-urllib \
    +python3-uuid \
    +python3-xml
endef

define Package/yt-dlp/description
  yt-dlp is a feature-rich command-line audio/video downloader with support for
  thousands of sites.
endef

# 添加 Build/Prepare 阶段，确保在开始前清理可能存在的冲突文件
define Build/Prepare
	$(call Build/Prepare/Default)
	# 确保目标目录存在
	mkdir -p $(PKG_INSTALL_DIR)/usr/bin/
	# 删除可能存在的冲突文件
	rm -f $(PKG_INSTALL_DIR)/usr/bin/yt-dlp
endef

# 修改 Build/Compile 阶段
define Build/Compile
	# 删除可能存在的冲突文件
	rm -f $(PKG_INSTALL_DIR)/usr/bin/yt-dlp
	$(call Py3Build/Compile/Default)
	# 编译后再次删除可能存在的冲突文件
	rm -f $(PKG_INSTALL_DIR)/usr/bin/yt-dlp
endef

# 修改 Build/Install 阶段
define Build/Install
	# 安装前删除可能存在的冲突文件
	rm -f $(PKG_INSTALL_DIR)/usr/bin/yt-dlp
	
	# 使用 pip 直接安装而不是通过 installer 模块
	cd "$(PKG_BUILD_DIR)" && \
		$(HOST_PYTHON3_PIP) install --root="$(PKG_INSTALL_DIR)" --prefix="/usr" --ignore-installed .
	
	# 安装后再次确保冲突文件被正确设置
	chmod +x $(PKG_INSTALL_DIR)/usr/bin/yt-dlp || true
endef

$(eval $(call Py3Package,yt-dlp))
$(eval $(call BuildPackage,yt-dlp))
$(eval $(call BuildPackage,yt-dlp-src))
