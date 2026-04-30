# shairport-sync 5.0.4 编译修复记录

> 环境：zagwrt · x86_64 · musl · feeds/packages/sound/shairport-sync · 2026-04-30

---

## 错误现象

```
configure: error: the Apple ALAC Decoder (deprecated) can not be used in AirPlay 2.
make[3]: *** [Makefile:141: .../.configured_...] Error 1
ERROR: package/feeds/packages/shairport-sync failed to build (build variant: openssl).
```

## 根因分析

`shairport-sync 5.0.4` 的 `configure` 明确禁止同时使用以下两个选项：

| 选项 | 说明 |
|------|------|
| `--with-apple-alac` | 已废弃的旧版 Apple ALAC 解码器 |
| `--with-airplay-2` | AirPlay 2 模式（openssl variant 启用） |

5.x 版本的 AirPlay 2 模式已通过 `libavcodec` 内置完整的 ALAC 解码支持，
不再需要也**不允许**同时使用旧的 `--with-apple-alac` 选项。

configure 输出中可以看到以下库均已正常检测通过，ALAC 能力完整：

```
checking for libavutil... yes
checking for libavcodec... yes
checking for libavformat... yes
checking for libswresample... yes
```

## Makefile 问题位置

```makefile
# feeds/packages/sound/shairport-sync/Makefile

CONFIGURE_ARGS += \
    --with-alsa \
    --with-apple-alac \    ← 第 91 行，需删除
    --with-libdaemon \
    --with-pipe \
    --with-mqtt-client \
    --with-metadata

ifeq ($(BUILD_VARIANT),openssl)
  CONFIGURE_ARGS+= --with-ssl=openssl --with-airplay-2   ← 第 97 行，与上面冲突
endif
```

## 修复方法

删除第 91 行的 `--with-apple-alac`：

```bash
sed -i '/--with-apple-alac/d' \
  /home/zag/OpenWrt/zagwrt/feeds/packages/sound/shairport-sync/Makefile
```

验证：

```bash
grep -n "apple-alac\|airplay" \
  /home/zag/OpenWrt/zagwrt/feeds/packages/sound/shairport-sync/Makefile
# 期望输出：只剩 --with-airplay-2，无 --with-apple-alac
```

## 重新编译

```bash
rm -rf /home/zag/OpenWrt/zagwrt/build_dir/target-x86_64_musl/shairport-sync-openssl/
make package/feeds/packages/shairport-sync/compile V=s -j$(nproc) 2>&1 | tail -5
```

## 注意事项

- 此修复对所有 build variant（openssl / mbedtls / mbedtls-tinysvcmdns）均有效
- `--with-apple-alac` 在 shairport-sync 4.x → 5.x 升级时已被废弃，升级版本时需同步移除
- ALAC 解码功能由 `libavcodec` 承担，功能无损失
