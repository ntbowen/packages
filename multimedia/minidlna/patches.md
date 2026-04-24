已保存到 Memo ✅，以下是本次修复的完整记录：

---

# minidlna 1.3.3 编译修复：FFmpeg 7.x `channels` API 废弃问题

> 环境：zagwrt · x86_64 · musl · 2026-04-24

---

## 问题

`minidlna-1.3.3` 在 FFmpeg 7.x（libavutil major=60）环境下编译失败：

```
libav.h:177:36: error: 'AVCodecParameters' has no member named 'channels'
  177 | #define lav_channels(s) s->codecpar->channels
metadata.c:781:53: note: in expansion of macro 'lav_channels'
```

---

## 根因分析

FFmpeg **5.1**（`libavcodec >= 59.37.100`）废弃了 `AVCodecParameters::channels`，改用 `ch_layout.nb_channels`。

| FFmpeg 版本 | API | 状态 |
|-------------|-----|------|
| < 5.1 | `codecpar->channels` | ✅ 有效 |
| >= 5.1 | `codecpar->ch_layout.nb_channels` | ✅ 新 API |
| >= 5.1 | `codecpar->channels` | ❌ 废弃 |
| 7.x（当前） | `codecpar->channels` | ❌ 编译报错 |

---

## 修复方案

### Patch 文件

路径：`feeds/packages/multimedia/minidlna/patches/080-fix-ffmpeg7-channels-api.patch`

````patch
--- a/libav.h
+++ b/libav.h
@@ -174,7 +174,11 @@
 #define lav_codec_tag(s) s->codecpar->codec_tag
 #define lav_sample_rate(s) s->codecpar->sample_rate
 #define lav_bit_rate(s) s->codecpar->bit_rate
+#if LIBAVCODEC_VERSION_INT >= AV_VERSION_INT(59, 37, 100)
+#define lav_channels(s) s->codecpar->ch_layout.nb_channels
+#else
 #define lav_channels(s) s->codecpar->channels
+#endif
 #define lav_width(s) s->codecpar->width
 #define lav_height(s) s->codecpar->height
 #define lav_profile(s) s->codecpar->profile
````

### 创建 Patch 命令

```bash
cat > feeds/packages/multimedia/minidlna/patches/080-fix-ffmpeg7-channels-api.patch << 'EOF'
--- a/libav.h
+++ b/libav.h
@@ -174,7 +174,11 @@
 #define lav_codec_tag(s) s->codecpar->codec_tag
 #define lav_sample_rate(s) s->codecpar->sample_rate
 #define lav_bit_rate(s) s->codecpar->bit_rate
+#if LIBAVCODEC_VERSION_INT >= AV_VERSION_INT(59, 37, 100)
+#define lav_channels(s) s->codecpar->ch_layout.nb_channels
+#else
 #define lav_channels(s) s->codecpar->channels
+#endif
 #define lav_width(s) s->codecpar->width
 #define lav_height(s) s->codecpar->height
 #define lav_profile(s) s->codecpar->profile
EOF

rm -rf build_dir/target-x86_64_musl/minidlna-1.3.3
make package/feeds/packages/minidlna/compile V=s -j$(nproc)
```

---

## ⚠️ OpenWrt Patch 格式规范（踩坑记录）

OpenWrt 的 patch 系统默认 `-p1` 模式，头部路径**必须**是相对路径：

```
--- a/文件名     ✅ 正确
+++ b/文件名     ✅ 正确

--- /tmp/xxx/文件名   ❌ 错误（绝对路径，-p1 无法匹配）
```

**不要**用 `diff -u 绝对路径1 绝对路径2` 直接生成 patch，应手写标准格式或用 `git diff`。
