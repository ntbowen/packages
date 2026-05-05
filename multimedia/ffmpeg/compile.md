````markdown
# ffmpeg-8.1 编译修复记录

> **环境**：zagwrt · aarch64_generic · musl · GCC 14.3.0 · 2026-05-05  
> **文件**：`feeds/packages/multimedia/ffmpeg/Makefile`  
> **状态**：✅ 编译成功

---

## 问题一：libpostproc `$(CP)` 行导致打包失败

### 现象

```
cp: cannot stat '.../ipkg-install/usr/lib/libpostproc.so.*': No such file or directory
make[2]: *** [...] Error 1
```

### 根因

FFmpeg 8.x 已彻底移除 `libpostproc` 库，但 Makefile 的 `install` 段仍保留了相关 `$(CP)` 指令，打包时找不到文件报错。

### 修复

删除所有含 `postproc` 的 `$(CP)` 行：

```bash
python3 << 'EOF'
path = "feeds/packages/multimedia/ffmpeg/Makefile"
with open(path, 'r') as f:
    lines = f.readlines()

out = []
for line in lines:
    if '$(CP)' in line and 'postproc' in line.lower():
        print(f"REMOVED: {line.rstrip()}")
    else:
        out.append(line)

with open(path, 'w') as f:
    f.writelines(out)

print(f"Done: {len(lines)} -> {len(out)} lines")
EOF
```

> 删除后剩余的 `postproc` 引用均为 `--disable-postproc` 配置参数，正常。

---

## 问题二：`libffmpeg-full` 缺少 `libdrm` 依赖声明

### 现象

```
Package libffmpeg-full is missing dependencies for the following libraries:
libdrm.so.2
make[2]: *** [Makefile:780: .../libffmpeg-full-8.1-r2.apk] Error 1
```

### 根因

ffmpeg-8.1 编译时会**自动检测** staging_dir 中的 `libdrm` 并启用 DRM 硬件加速，导致 `.so` 实际链接了 `libdrm.so.2`，但 `libffmpeg-full` 的 `DEPENDS` 中未声明该依赖。

### 修复

在 `libffmpeg-full` 的 `DEPENDS` 末尾追加 `+libdrm`：

```bash
python3 << 'EOF'
path = "feeds/packages/multimedia/ffmpeg/Makefile"
with open(path, 'r') as f:
    content = f.read()

old = '+!PACKAGE_libx264:fdk-aac'
new = '+!PACKAGE_libx264:fdk-aac \\\n    +libdrm'

if old in content:
    content = content.replace(old, new, 1)
    with open(path, 'w') as f:
        f.write(content)
    print("Done: +libdrm added to libffmpeg-full DEPENDS")
else:
    print("ERROR: target string not found!")
EOF
```

修复后 `libffmpeg-full` 的 `DEPENDS` 结构：

```makefile
define Package/libffmpeg-full
$(call Package/libffmpeg/Default)
 TITLE+= (full)
 DEPENDS+= +alsa-lib +libatomic +libopenssl +libopus \
    +PACKAGE_libv4l:libv4l \
    +SOFT_FLOAT:shine \
    +!SOFT_FLOAT:lame-lib \
    +PACKAGE_libx264:libx264 \
    +!PACKAGE_libx264:fdk-aac \
    +libdrm          # ← 新增
 VARIANT:=full
 PROVIDES+=libffmpeg-mini libffmpeg-audio-dec
endef
```

### 验证

```bash
grep -n -A10 "define Package/libffmpeg-full$" feeds/packages/multimedia/ffmpeg/Makefile \
  | grep -E "DEPENDS|drm"
# 期望输出：
# 351- DEPENDS+= +alsa-lib +libatomic +libopenssl +libopus \
# 357-    +libdrm
```

---

## 重新编译

```bash
# 问题一修复后（需完整重编，约 624 秒）
rm -rf build_dir/target-aarch64_generic_musl/ffmpeg-full/
make package/feeds/packages/ffmpeg/compile V=s -j$(nproc)

# 问题二修复后（仅重新打包，约 30 秒内完成）
rm -rf build_dir/target-aarch64_generic_musl/ffmpeg-full/ffmpeg-8.1/ipkg-aarch64_generic/
make package/feeds/packages/ffmpeg/compile V=s -j$(nproc)
```

---

## 注意事项

| 要点 | 说明 |
|------|------|
| `libpostproc` 彻底移除 | FFmpeg 8.x 不再提供此库，所有引用均需清除 |
| `libdrm` 自动检测 | staging_dir 中存在 libdrm 时 ffmpeg 会自动链接，必须在 DEPENDS 中声明 |
| 问题二无需重编 | `.built` 文件存在，删除 `ipkg-aarch64_generic/` 后只走 install/package 阶段 |
| 编译耗时参考 | full 变体完整编译约 624 秒（aarch64），重新打包约 30 秒 |
````
