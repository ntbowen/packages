以下是完整的 Markdown 文档：

````markdown
# zagwrt 编译问题修复记录

> 环境：zagwrt · OpenWrt · aarch64_generic · musl · GCC 14.3.0

---

## 1. bluez-5.86 — libical 5.x 兼容性问题

### 问题现象

编译 `obexd/plugins/phonebook-dummy.c` 时链接失败：

```
undefined reference to `writeMemVObject`
undefined reference to `isAPropertyOf`
```

### 根因分析

`libical 4.x / 5.x` 将 vCard / vCalendar 相关功能拆分至独立的 `libicalvcal` 子库，而 bluez 的构建系统仅链接了 `-lical`，导致符号缺失。

```bash
# 确认库文件拆分情况
ls staging_dir/target-aarch64_generic_musl/usr/lib/libical*
# libical.so  libicalvcal.so  libicalss.so ...
```

### 修复方案

修改 `feeds/packages/utils/bluez/Makefile`，在 `CONFIGURE_ARGS` 定义前注入额外链接参数：

```makefile
# libical 4.x/5.x splits vCard/vCalendar into libicalvcal
TARGET_LDFLAGS += -licalvcal
```

### 验证

编译日志确认 `-licalvcal` 已正确传入所有阶段：

```bash
# configure 阶段
LDFLAGS="... -licalvcal " ./configure ...

# make 阶段
LDFLAGS="... -licalvcal " make ...

# obexd 最终链接（同时使用 -lical 和 -licalvcal）
aarch64-openwrt-linux-musl-gcc ... -lical ... -licalvcal ...
```

### 编译结果

```
time: package/feeds/packages/bluez/compile#113.83#18.50#10.81
```

✅ 编译成功，生成以下 APK：

| 包名 | 说明 |
|------|------|
| `bluez-libs-5.86-r1.apk` | Bluetooth 共享库 |
| `bluez-utils-5.86-r1.apk` | 基础工具（hciconfig、hcitool 等） |
| `bluez-utils-extra-5.86-r1.apk` | 附加工具（btmgmt、gatttool 等） |
| `bluez-daemon-5.86-r1.apk` | bluetoothd + bluetoothctl + obexd |

---

## 2. luci-app-hermes — APK 版本格式错误

### 问题现象

```
ERROR: info field 'version' has invalid value
```

### 修复方案

将 `PKG_VERSION` 修改为符合 `数字.数字.数字-r数字` 的标准格式：

```makefile
# 修改前
PKG_VERSION:=1.0-beta

# 修改后
PKG_VERSION:=1.0.0-r1
```

---

## 3. librespeed-go-1.1.6 — GO_PKG 路径错误

### 问题现象

编译时找不到 Go 模块入口。

### 修复方案

修正 `GO_PKG` 路径及二进制安装路径：

```makefile
GO_PKG:=github.com/librespeed/speedtest-go
GO_PKG_INSTALL_BIN_PATH:=/usr/bin
```

---

## 4. tcl-9.0.3 — Makefile 中失效的 cp 指令

### 问题现象

安装阶段报错，找不到 `tcl8` 目录。

### 修复方案

移除 Makefile 中遗留的 `cp` 指令（引用了已不存在的 `tcl8` 路径）。

---

## 5. lxc-7.0.0 — 废弃补丁导致编译失败

### 问题现象

应用补丁 `021-remove-legacy-cgroup-support.patch` 时失败。

### 修复方案

删除该废弃补丁文件：

```bash
rm feeds/packages/utils/lxc/patches/021-remove-legacy-cgroup-support.patch
```

---

## 6. 内核 BBR v3 — tcp_bbr.c 符号未定义

### 问题现象

编译 `net/ipv4/tcp_bbr.c` 时提示符号未定义。

### 根因分析

Linux 6.18.26 内核已内置 BBRv3，原有补丁与内核代码冲突。

### 修复方案

删除 `target/linux/generic/hack-6.18/` 下所有 BBR 相关补丁：

```bash
rm target/linux/generic/hack-6.18/601-*.patch
```

---

## 通用调试命令

```bash
# 清理特定包的构建标记
rm -rf build_dir/target-aarch64_generic_musl/<package>-<version>/

# 重新编译特定包
make package/feeds/packages/<package>/compile V=s -j$(nproc)

# 重新编译内核
make target/linux/clean
make -j$(nproc)

# 确认库文件拆分情况
ls staging_dir/target-aarch64_generic_musl/usr/lib/libical*

# 快速定位补丁错误
make ... 2>&1 | grep -E "Applying|error:|FAILED"
```

---

*最后更新：2026-05-05*
````
