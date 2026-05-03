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

- 上游 `feeds/packages` 的 `tcl` Makefile 存在此遗留代码，每次 `feeds update` 后需重新应用
- tcl 9.x 不再在 `usr/lib/` 下创建 `tcl8/` 或 `tcl9/` 子目录，相关内容已整合进 `.so` 和 `.sh` 文件
````
