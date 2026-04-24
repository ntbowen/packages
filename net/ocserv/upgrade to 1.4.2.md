````markdown
# ocserv 1.4.2 升级完整修复记录

> **环境**：zagwrt · x86_64 · musl · 2026-04-24
> **升级路径**：1.3.0（autoconf）→ 1.4.2（Meson）

---

## 一、构建系统迁移：autoconf → Meson

### Makefile 关键修改

```makefile
# 旧
include $(INCLUDE_DIR)/package.mk

# 新
include $(INCLUDE_DIR)/meson.mk

# 旧（configure 阶段）
CONFIGURE_ARGS += --without-libwrap ...

# 新（meson 阶段）
MESON_ARGS += \
  -Dlibwrap=disabled \
  ...
```

---

## 二、Host 构建依赖修复（Fedora）

```bash
sudo dnf install protobuf-c-compiler ipcalc
```

| 缺失包 | 用途 |
|--------|------|
| `protobuf-c-compiler` | host 端 protobuf-c 代码生成 |
| `ipcalc` | ocserv 配置脚本依赖 |

---

## 三、musl 符号缺失修复

**现象**：链接阶段报 `libwrap` 相关符号未定义

**原因**：musl libc 不提供 `libwrap`（TCP Wrappers）

**修复**：在 `MESON_ARGS` 中禁用：

```makefile
MESON_ARGS += -Dlibwrap=disabled
```

---

## 四、install 段路径修复（核心）

Meson 构建产物路径与旧 autoconf **完全不同**，需全部修正：

| 文件 | 旧路径（autoconf） | 新路径（meson） |
|------|-------------------|----------------|
| `ocserv` | `$(PKG_BUILD_DIR)/src/ocserv` | `$(PKG_INSTALL_DIR)/usr/sbin/ocserv` |
| `ocserv-worker` | `$(PKG_BUILD_DIR)/src/ocserv-worker` | `$(PKG_INSTALL_DIR)/usr/sbin/ocserv-worker` |
| `ocpasswd` | `$(PKG_BUILD_DIR)/src/ocpasswd/ocpasswd` | `$(PKG_INSTALL_DIR)/usr/bin/ocpasswd` |
| `occtl` | `$(PKG_BUILD_DIR)/src/occtl/occtl` | `$(PKG_INSTALL_DIR)/usr/bin/occtl` |
| `ocserv-fw` | `$(PKG_BUILD_DIR)/src/ocserv-fw` → `/usr/bin/` | `$(PKG_INSTALL_DIR)/usr/libexec/ocserv-fw` → `/usr/libexec/` ⚠️ |

> **注意**：`ocserv-fw` 安装目录从 `/usr/bin/` 变为 `/usr/libexec/`，init 脚本中未引用该路径，无需额外修改。

### 最终正确的 install 段

```makefile
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
```

### 修复操作（实际执行命令）

```bash
# Step 1：sed 修正 sbin/bin 路径（注意行号对应原始 Makefile）
sed -i \
  -e '107s|$(PKG_BUILD_DIR)/src/ocserv $(1)/usr/sbin/|$(PKG_INSTALL_DIR)/usr/sbin/ocserv $(1)/usr/sbin/|' \
  -e '108s|$(PKG_BUILD_DIR)/src/ocserv-worker $(1)/usr/sbin/|$(PKG_INSTALL_DIR)/usr/sbin/ocserv-worker $(1)/usr/sbin/|' \
  -e '110s|$(PKG_BUILD_DIR)/src/ocserv-fw $(1)/usr/bin/|$(PKG_INSTALL_DIR)/usr/bin/ocpasswd $(1)/usr/bin/|' \
  -e '111s|$(PKG_BUILD_DIR)/src/ocpasswd/ocpasswd $(1)/usr/bin/|$(PKG_INSTALL_DIR)/usr/bin/occtl $(1)/usr/bin/|' \
  feeds/packages/net/ocserv/Makefile

# Step 2：Python 插入 libexec 行（sed \n 在此场景不展开）
python3 -c "
with open('feeds/packages/net/ocserv/Makefile', 'r') as f:
    lines = f.readlines()
lines[111] = '\t\$(INSTALL_DIR) \$(1)/usr/libexec\n\t\$(INSTALL_BIN) \$(PKG_INSTALL_DIR)/usr/libexec/ocserv-fw \$(1)/usr/libexec/\n'
with open('feeds/packages/net/ocserv/Makefile', 'w') as f:
    f.writelines(lines)
"

# Step 3：删除 sed 遗留的重复行
sed -i '113d' feeds/packages/net/ocserv/Makefile
```

---

## 五、验证

### meson install 实际产物

```
build_dir/target-x86_64_musl/ocserv-1.4.2/ipkg-install/
├── usr/bin/occtl
├── usr/bin/ocpasswd
├── usr/libexec/ocserv-fw
├── usr/sbin/ocserv
├── usr/sbin/ocserv-worker
└── usr/share/man/man8/
    ├── occtl.8
    ├── ocpasswd.8
    └── ocserv.8
```

### 编译命令

```bash
rm -rf build_dir/target-x86_64_musl/ocserv-1.4.2
make package/feeds/packages/ocserv/compile V=s -j$(nproc)
```

### 结果

```
✅ ocserv-1.4.2-r3.apk → bin/packages/x86_64/packages/
⏱  compile 耗时：17.09s
```

---

## 六、后续 commit

```bash
git add feeds/packages/net/ocserv/Makefile
git commit -m "net/ocserv: upgrade to 1.4.2, migrate to meson build system

- Switch build system from autoconf to meson
- Fix install paths to use PKG_INSTALL_DIR
- Move ocserv-fw from /usr/bin to /usr/libexec (meson default)
- Disable libwrap (not available in musl)
- Require protobuf-c-compiler and ipcalc on host"
```
````
