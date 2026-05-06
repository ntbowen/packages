已存档到 Memo ✅，Markdown 如下：

````markdown
# tcl 9.0.3 编译修复：install 段遗留 tcl8 目录引用

> 环境：zagwrt · x86_64 · musl · feeds/packages/lang/tcl · 2026-05-03

---

## 错误现象

```
cp -fpR -a .../ipkg-install/usr/lib/tcl8 .../tcl/usr/lib/
cp: cannot stat '.../ipkg-install/usr/lib/tcl8': No such file or directory
make[3]: *** [Makefile:125: .../tcl-9.0.3-r1.apk] Error 1
ERROR: package/feeds/packages/tcl failed to build.
```

---

## 根因

Makefile install 段存在两行遗留代码，在 tcl 8.x 时代写入，升级到 9.0.3 后目录结构已变化：

| 行号 | 内容 | 问题 |
|------|------|------|
| 96 | `$(CP) -a $(PKG_INSTALL_DIR)/usr/lib/tcl8 $(1)/usr/lib/` | 硬编码 `tcl8`，tcl 9.x 不存在此目录 |
| 97 | `$(CP) -a $(PKG_INSTALL_DIR)/usr/lib/tcl$(TCL_MAJOR_VERSION) $(1)/usr/lib/` | `tcl9/` 子目录同样不存在 |

实际 tcl 9.0.3 的 `usr/lib/` 产物：

```
usr/lib/libtcl9.0.so
usr/lib/libtclstub.a
usr/lib/pkgconfig/
usr/lib/tclConfig.sh
usr/lib/tclooConfig.sh
```

**没有任何 `tcl8/` 或 `tcl9/` 子目录**，两行 CP 均为无效遗留代码。

---

## 修复

```bash
MAKEFILE="/home/zag/OpenWrt/zagwrt/feeds/packages/lang/tcl/Makefile"
cp "$MAKEFILE" "${MAKEFILE}.bak"

# 删除硬编码 tcl8 目录的行
sed -i '/$(CP) -a $(PKG_INSTALL_DIR)\/usr\/lib\/tcl8/d' "$MAKEFILE"

# 删除 tcl$(TCL_MAJOR_VERSION) 目录的行
sed -i '/$(CP) -a $(PKG_INSTALL_DIR)\/usr\/lib\/tcl$(TCL_MAJOR_VERSION)/d' "$MAKEFILE"

# 验证
grep -n "tcl8\|tcl9\|usr/lib" "$MAKEFILE" | head -20
```

---

## 重新编译

```bash
rm -f /home/zag/OpenWrt/zagwrt/build_dir/target-x86_64_musl/tcl9.0.3/.built
make package/feeds/packages/tcl/compile V=s -j$(nproc)
```

---

## 注意事项

# tcl

OpenWrt 平台的 Tcl（Tool Command Language）9.0.3，在上游基础上额外暴露了私有头文件，供 expect 等 TEA 扩展使用。

## 包信息

| 字段 | 值 |
| --- | --- |
| 名称 | tcl |
| 版本 | 9.0.3 |
| 源码 | [SourceForge](https://sourceforge.net/projects/tcl/)（`tcl9.0.3-src.tar.gz`） |
| PKG_HASH | `2537ba0c86112c8c953f7c09d33f134dd45c0fb3a71f2d7f7691fd301d2c33a6` |
| 许可证 | TCL |
| 维护者 | Joe Mistachkin `<joe@mistachkin.com>` |
| 依赖 | `libpthread`、`zlib` |

## 相对上游的修改

### `Build/InstallDev` — 安装私有头文件

Tcl 9 不再将内部头文件安装到标准 include 路径。expect 等 TEA 扩展需要以下文件：

- `generic/tclInt.h` — Tcl 内部数据结构
- `generic/tclUnixPort.h` — Unix 平台类型定义（来自源码树 `unix/tclUnixPort.h`）

在 `Build/InstallDev` 中新增：

```makefile
$(INSTALL_DIR) $(1)/usr/include/generic
$(CP) $(PKG_BUILD_DIR)/generic/*.h        $(1)/usr/include/generic/
$(CP) $(PKG_BUILD_DIR)/*.h                $(1)/usr/include/generic/
$(CP) $(PKG_BUILD_DIR)/unix/tclUnixPort.h $(1)/usr/include/generic/
$(SED) 's|TCL_SRC_DIR=.*|TCL_SRC_DIR=$(STAGING_DIR)/usr/include|' \
    $(1)/usr/lib/tclConfig.sh
```

同时将 `tclConfig.sh` 中的 `TCL_SRC_DIR` 改写为 staging_dir 路径，确保 TEA `configure` 脚本能正确找到 `generic/tclInt.h`。

### 为何需要此修改

Tcl 9 安装的 `tclPort.h` 包含：

```c
#include "tclUnixPort.h"
```

若 `tclUnixPort.h` 不在同一目录，任何间接包含 `tclPort.h`（经由 `tclInt.h`）的包都会报错：

```text
fatal error: tclUnixPort.h: No such file or directory
```

## 安装文件（target）

```text
/usr/bin/tclsh9.0
/usr/bin/tclsh  ->  tclsh9.0
/usr/lib/libtcl9.0.so
/usr/lib/tcl9.0/
/usr/lib/tcl8/
```

## 安装文件（staging_dir，开发用）

```text
/usr/include/*.h                (Tcl 公共头文件)
/usr/include/generic/*.h        (内部头文件：tclInt.h、tclUnixPort.h 等)
/usr/lib/libtcl9.0.{a,so}
/usr/lib/tclConfig.sh           (TCL_SRC_DIR 已改写为 staging_dir)
/usr/lib/tclooConfig.sh
/usr/lib/pkgconfig/tcl.pc
```


- 上游 `feeds/packages` 的 `tcl` Makefile 存在此遗留代码，每次 `feeds update` 后需重新应用
- tcl 9.x 不再在 `usr/lib/` 下创建 `tcl8/` 或 `tcl9/` 子目录，相关内容已整合进 `.so` 和 `.sh` 文件
````
