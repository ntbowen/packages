````markdown
# spotifyd 0.4.2 编译修复

> 环境：zagwrt · x86_64 · musl · 2026-04-25

---

## 问题一：PulseAudio 链接错误

### 现象

```
error: linking with `x86_64-openwrt-linux-musl-gcc` failed
= note: /usr/lib/libpulsecommon-17.0.so: undefined reference to `pa_mainloop_new`
```

### 根因

`Cargo.toml` 中 `default` features 包含 `pulseaudio_backend`，OpenWrt 路由器环境无 PulseAudio，导致链接失败。

### 修复：Patch 文件

路径：`feeds/packages/sound/spotifyd/patches/001-disable-pulseaudio-backend.patch`

```patch
--- a/Cargo.toml
+++ b/Cargo.toml
@@ -61,7 +61,7 @@
 [features]
 alsa_backend = ["librespot-playback/alsa-backend", "dep:alsa"]
 dbus_mpris = ["dep:dbus", "dep:dbus-tokio", "dep:dbus-crossroads"]
-default = ["alsa_backend", "pulseaudio_backend", "dbus_mpris"]
+default = ["alsa_backend", "dbus_mpris"]
 portaudio_backend = ["librespot-playback/portaudio-backend"]
 pulseaudio_backend = ["librespot-playback/pulseaudio-backend"]
 rodio_backend = ["librespot-playback/rodio-backend"]
```

### 创建命令

```bash
python3 -c "
with open('feeds/packages/sound/spotifyd/patches/001-disable-pulseaudio-backend.patch', 'w') as f:
    f.write('''--- a/Cargo.toml
+++ b/Cargo.toml
@@ -61,7 +61,7 @@
 [features]
 alsa_backend = [\"librespot-playback/alsa-backend\", \"dep:alsa\"]
 dbus_mpris = [\"dep:dbus\", \"dep:dbus-tokio\", \"dep:dbus-crossroads\"]
-default = [\"alsa_backend\", \"pulseaudio_backend\", \"dbus_mpris\"]
+default = [\"alsa_backend\", \"dbus_mpris\"]
 portaudio_backend = [\"librespot-playback/portaudio-backend\"]
 pulseaudio_backend = [\"librespot-playback/pulseaudio-backend\"]
 rodio_backend = [\"librespot-playback/rodio-backend\"]
''')
print('patch written')
"
```

> ⚠️ **关键**：patch 的 `@@ -61,7 +61,7 @@` 行号必须与实际 Cargo.toml 中 `[features]` 的位置匹配。
> 若 hunk failed，先确认行号：
> ```bash
> grep -n "\[features\]\|default" build_dir/target-x86_64_musl/spotifyd-0.4.2/Cargo.toml
> ```
> 再重写 patch 的 `@@` 行。

---

## 问题二：Patch Hunk Failed

### 现象

```
Hunk #1 FAILED at 1.
1 out of 1 hunk FAILED -- saving rejects to file Cargo.toml.rej
Patch failed!
```

### 根因

旧 patch 写的是 `@@ -1,4 +1,4 @@`，意思是从第 1 行开始匹配，但 `[features]` 实际在第 61 行，上下文对不上。

### 修复

重新生成 patch，`@@` 行改为 `@@ -61,7 +61,7 @@`，并带上完整的上下文行（见上方创建命令）。

---

## 问题三：packaging 阶段缺少依赖声明

### 现象

```
Package spotifyd is missing dependencies for the following libraries:
libcrypto.so.3
libdbus-1.so.3
libssl.so.3
```

### 根因

Makefile 的 `DEPENDS` 中未声明 `+libopenssl` 和 `+libdbus`，但二进制实际链接了这两个库。

### 修复

```bash
sed -i 's/DEPENDS:=\(.*\) +alsa-lib/DEPENDS:=\1 +alsa-lib +libopenssl +libdbus/' \
    feeds/packages/sound/spotifyd/Makefile
```

修复后 DEPENDS 变为：

```makefile
DEPENDS:=$(RUST_ARCH_DEPENDS) @(!arm||TARGET_bcm53xx||HAS_FPU) @!(i386||mips||mipsel) +alsa-lib +libopenssl +libdbus
```

---

## 完整修复流程

```bash
# 1. 写入 patch（禁用 pulseaudio_backend，注意行号需与实际 Cargo.toml 匹配）
python3 -c "
with open('feeds/packages/sound/spotifyd/patches/001-disable-pulseaudio-backend.patch', 'w') as f:
    f.write('''--- a/Cargo.toml
+++ b/Cargo.toml
@@ -61,7 +61,7 @@
 [features]
 alsa_backend = [\"librespot-playback/alsa-backend\", \"dep:alsa\"]
 dbus_mpris = [\"dep:dbus\", \"dep:dbus-tokio\", \"dep:dbus-crossroads\"]
-default = [\"alsa_backend\", \"pulseaudio_backend\", \"dbus_mpris\"]
+default = [\"alsa_backend\", \"dbus_mpris\"]
 portaudio_backend = [\"librespot-playback/portaudio-backend\"]
 pulseaudio_backend = [\"librespot-playback/pulseaudio-backend\"]
 rodio_backend = [\"librespot-playback/rodio-backend\"]
''')
print('patch written')
"

# 2. 补全 Makefile 依赖声明
sed -i 's/DEPENDS:=\(.*\) +alsa-lib/DEPENDS:=\1 +alsa-lib +libopenssl +libdbus/' \
    feeds/packages/sound/spotifyd/Makefile

# 3. 清理并编译（Rust 编译约 1m08s）
rm -rf build_dir/target-x86_64_musl/spotifyd-0.4.2/
make package/feeds/packages/spotifyd/compile V=s -j$(nproc) 2>&1 | tail -20
```

---

## 验证结果

```
Finished `release` profile [optimized] target(s) in 1m 08s
time: package/feeds/packages/spotifyd/compile#262.92#29.23#69.22
```

无 ERROR，编译打包成功。

---

## 经验总结

| 错误类型 | 排查方法 |
|----------|----------|
| `Hunk FAILED` | `grep -n` 确认目标行实际行号，重写 `@@` |
| `missing dependencies` | `readelf -d` 或看报错的 `.so` 名，补到 `DEPENDS` |
| Rust 链接失败 | 检查 `Cargo.toml` 的 `default` features，用 patch 禁用不需要的 backend |
````
