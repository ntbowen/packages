# librespeed-go 1.1.6 编译修复

> 环境：zagwrt · x86_64 · musl · 2026-05-03  
> 文件：`feeds/packages/net/librespeed-go/Makefile`

---

## 问题一：GO_PKG module 名不匹配

### 错误现象

```
go: warning: "github.com/librespeed/speedtest/..." matched no packages
no Go files in .../librespeed-go-1.1.6/.go_work/build
make[3]: *** [.../.built] Error 1
```

### 根因

| 项目 | 值 |
|------|-----|
| `go.mod` 中的 module 名 | `github.com/librespeed/speedtest-go` |
| Makefile 中的 `GO_PKG` | `github.com/librespeed/speedtest` |

Makefile 的 `GO_PKG` 缺少 `-go` 后缀，与 `go.mod` 声明的 module 名不匹配，导致 `go list` 找不到任何包。

### 修复

```bash
MAKEFILE="/home/zag/OpenWrt/zagwrt/feeds/packages/net/librespeed-go/Makefile"
sed -i 's|GO_PKG:=github.com/librespeed/speedtest$|GO_PKG:=github.com/librespeed/speedtest-go|' "$MAKEFILE"
```

---

## 问题二：install 段二进制名不匹配

### 错误现象

```
install: cannot stat '.../ipkg-install/usr/bin/speedtest': No such file or directory
make[2]: *** [.../librespeed-go-1.1.6-r5.apk] Error 1
```

### 根因

Makefile install 段写死了 `speedtest`，但 Go 工具链按 module 路径最后一段命名二进制：

```
github.com/librespeed/speedtest-go  →  speedtest-go
```

修复 `GO_PKG` 后，实际产物为 `speedtest-go`，与 install 段不符。

### 修复

```bash
MAKEFILE="/home/zag/OpenWrt/zagwrt/feeds/packages/net/librespeed-go/Makefile"
sed -i 's|$(PKG_INSTALL_DIR)/usr/bin/speedtest |$(PKG_INSTALL_DIR)/usr/bin/speedtest-go |' "$MAKEFILE"
```

---

## 完整修复流程

```bash
MAKEFILE="/home/zag/OpenWrt/zagwrt/feeds/packages/net/librespeed-go/Makefile"
cp "$MAKEFILE" "${MAKEFILE}.bak"

# 修复1：GO_PKG module 名
sed -i 's|GO_PKG:=github.com/librespeed/speedtest$|GO_PKG:=github.com/librespeed/speedtest-go|' "$MAKEFILE"

# 修复2：install 段二进制名
sed -i 's|$(PKG_INSTALL_DIR)/usr/bin/speedtest |$(PKG_INSTALL_DIR)/usr/bin/speedtest-go |' "$MAKEFILE"

# 清理重建
rm -rf /home/zag/OpenWrt/zagwrt/build_dir/target-x86_64_musl/librespeed-go-1.1.6/
make package/feeds/packages/librespeed-go/compile V=s -j$(nproc)
```

---

## 注意事项

- 上游 `feeds/packages` 的 `librespeed-go` Makefile 存在两处笔误，每次 `feeds update` 后需重新应用
- **Go 二进制命名规则**：由 module 路径最后一段决定，`speedtest-go` ≠ `speedtest`
