已存入 Memo，以下是完整记录：

---

# OpenWrt 编译错误修复完整记录

> 环境：zagwrt · x86_64 · musl · 2026-04-24

---

## 一、Rust 交叉编译环境修复（全局永久方案）

### 问题
Rust 包交叉编译时，`cc` crate 无法识别 OpenWrt 工具链。

### 修复文件
`feeds/packages/lang/rust/rust-values.mk`

在文件末尾追加：

```makefile
export CC_$(subst -,_,$(RUSTC_TARGET_ARCH)):=$(TARGET_CC_NOCACHE)
export CXX_$(subst -,_,$(RUSTC_TARGET_ARCH)):=$(TARGET_CXX_NOCACHE)
export AR_$(subst -,_,$(RUSTC_TARGET_ARCH)):=$(TARGET_AR)
```

---

## 二、Bind 编译 Patch 冲突修复

### 问题
`fix-usr-allow-rndc-addzone#1.patch` 修改的代码已合入 `bind-9.20.22` 源码，patch 应用冲突。

### 修复步骤

```bash
rm feeds/packages/net/bind/patches/fix-usr-allow-rndc-addzone#1.patch
rm -rf build_dir/target-x86_64_musl/bind-9.20.22
make package/feeds/packages/bind/compile V=s -j$(nproc)
```

---

## 三、FRP 编译错误修复（Web UI embed 依赖问题）✅

### 问题
`frp-0.68.1` 编译时 `go embed` 引用了不存在的 `dist/` 目录：

```
pattern dist: no matching files found
```

### 根因

| 文件 | Build 标签 | 行为 |
|------|-----------|------|
| `web/frpc/embed.go` | `!现在eb` | 默认编译，强依赖 `dist/` Web UI 资源 |
| `web/frpc/embed_stub.go` | `noweb` | 需要 `-标签 现在eb` 才编译，仅有包声明 |

`golang-build.sh` 的 `go list` 阶段不传 build tags，无法通过 `-标签 现在eb` 绕过，且 OpenWrt 不提供 Web UI 静态资源。

### 修复方案
新建 patch 文件：`feeds/packages/net/frp/patches/001-disable-web-ui-embed.patch`

```patch
--- a/web/frpc/embed.go
+++ b/web/frpc/embed.go
@@ -1,4 +1,4 @@
-//go:build !noweb
+//go:build noweb_disabled

 package frpc
 
--- a/web/frps/embed.go
+++ b/web/frps/embed.go
@@ -1,4 +1,4 @@
-//go:build !noweb
+//go:build noweb_disabled

 package frps
 
--- a/web/frpc/embed_stub.go
+++ b/web/frpc/embed_stub.go
@@ -1,3 +1,2 @@
-//go:build noweb
 
 package frpc
--- a/web/frps/embed_stub.go
+++ b/web/frps/embed_stub.go
@@ -1,3 +1,2 @@
-//go:build noweb
 
 package frps
```

### 修改逻辑

| 文件 | 修改后 | 效果 |
|------|--------|------|
| `embed。go` | tag 改为 `noweb_disabled` | 永远不编译，彻底绕开 `dist/` |
| `embed_stub.go` | 去掉 tag，无条件编译 | 保证包内始终有可用 `.go` 文件 |

### 操作步骤

```bash
mkdir -p feeds/packages/net/frp/patches
# 写入 patch 内容（见上）
rm -rf build_dir/target-x86_64_musl/frp-0.68.1
make package/feeds/packages/frp/compile V=s -j$(nproc)
```

---

## 通用注意事项

> - 修改任何 Makefile / 构建脚本后，**必须删除** `build_dir` 下对应的包目录，否则旧缓存（`.configured_*` / `.built`）会导致修改不生效。
> - `golang-build。sh` 是公共基础设施，**不建议直接修改**，优先用 patch 解决单包问题，影响范围更小、更易维护。
