````markdown
# quectel-qmi-wwan 1.2.9 编译修复

> 环境：zagwrt · x86_64 · musl · Linux **6.18.21** · 2026-04-26

---

## 一、问题概述

| # | 错误信息 | 根因 |
|---|---------|------|
| 1 | `'struct usbnet' has no member named 'bh'` | Linux 6.18 移除 `tasklet_struct bh`，改为 `work_struct bh_work` |
| 2 | `implicit declaration of function 'tasklet_init'` | 同上，tasklet 相关 API 一并移除 |
| 3 | `unterminated #if` at line 114 | Python 脚本用行号定位函数结尾，被其他 patch 的行号偏移破坏 |
| 4 | `implicit declaration of function 'hrtimer_init'` | Linux 6.17 废弃 `hrtimer_init()` + `.function =`，改为 `hrtimer_setup()` |

---

## 二、Patch 文件

**路径**：`feeds/packages/kernel/quectel-qmi-wwan/patches/020-fix-usbnet-bh-api-kernel-6.x.patch`

包含 **4 个 Hunk**：

### Hunk 1 — struct 成员类型替换

```c
// 旧（Linux < 6.0）
struct tasklet_struct usbnet_bh;

// 新（Linux >= 6.0）
struct work_struct    usbnet_bh;
```

### Hunk 2 — usbnet_bh 函数签名与实现替换

```c
// 旧
static void usbnet_bh(unsigned long data) {
    sQmiWwanQmap *pQmapDev = (sQmiWwanQmap *)data;
    ...
}

// 新（Linux >= 6.0）
static void usbnet_bh(struct work_struct *work) {
    sQmiWwanQmap *pQmapDev = container_of(work, sQmiWwanQmap, usbnet_bh);
    schedule_work(&pQmapDev->mpNetDev->bh_work);
    if (!netif_queue_stopped(pQmapDev->mpNetDev->net)) {
        qmap_wake_queue(pQmapDev);
    }
}
```

### Hunk 3 — tasklet_init → INIT_WORK

```c
// 旧
pQmapDev->usbnet_bh = dev->bh;
tasklet_init(&dev->bh, usbnet_bh, (unsigned long)dev);

// 新（Linux >= 6.0）
pQmapDev->usbnet_bh = dev->bh_work;
INIT_WORK(&dev->bh_work, usbnet_bh);
```

### Hunk 4 — hrtimer_init → hrtimer_setup

```c
// 旧（Linux < 6.17）
hrtimer_init(&priv->agg_hrtimer, CLOCK_MONOTONIC, HRTIMER_MODE_REL);
priv->agg_hrtimer.function = rmnet_usb_tx_agg_timer_cb;

// 新（Linux >= 6.17）
hrtimer_setup(&priv->agg_hrtimer, rmnet_usb_tx_agg_timer_cb,
              CLOCK_MONOTONIC, HRTIMER_MODE_REL);
```

---

## 三、Patch 生成脚本

> **前提**：`/tmp/qmi_wwan_q.c.orig` 必须是**已应用 010/011 patch 之后**从 build_dir 复制的文件。

```bash
# Step 1: 备份已打过 010/011 patch 的源文件
cp build_dir/target-x86_64_musl/linux-x86_64/quectel-qmi-wwan-1.2.9/qmi_wwan_q.c \
   /tmp/qmi_wwan_q.c.orig

# Step 2: 运行生成脚本
cat > /tmp/make_patched2.py << 'PYEOF'
src = '/tmp/qmi_wwan_q.c.orig'
dst = '/tmp/qmi_wwan_q.c.new'

with open(src, 'r') as f:
    lines = f.readlines()

out = []
i = 0
in_old_usbnet_bh = False
usbnet_bh_brace_depth = 0

while i < len(lines):
    line = lines[i]
    stripped = line.rstrip()

    # Hunk 1: struct 成员 tasklet_struct -> work_struct
    if stripped == '\tstruct tasklet_struct usbnet_bh;':
        out.append('#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 0, 0)\n')
        out.append('\tstruct work_struct\tusbnet_bh;\n')
        out.append('#else\n')
        out.append(line)
        out.append('#endif\n')
        i += 1
        continue

    # Hunk 2: usbnet_bh 函数体替换（大括号深度计数定位结尾）
    if (stripped == '#if defined(QUECTEL_UL_DATA_AGG)' and
            i + 1 < len(lines) and
            lines[i+1].rstrip() == 'static void usbnet_bh(unsigned long data) {'):
        out.append(line)
        out.append('#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 0, 0)\n')
        out.append('static void usbnet_bh(struct work_struct *work) {\n')
        out.append('\tsQmiWwanQmap *pQmapDev = container_of(work, sQmiWwanQmap, usbnet_bh);\n')
        out.append('\tschedule_work(&pQmapDev->mpNetDev->bh_work);\n')
        out.append('\tif (!netif_queue_stopped(pQmapDev->mpNetDev->net)) {\n')
        out.append('\t\tqmap_wake_queue(pQmapDev);\n')
        out.append('\t}\n')
        out.append('}\n')
        out.append('#else\n')
        in_old_usbnet_bh = True
        usbnet_bh_brace_depth = 0
        i += 1
        continue

    # 追踪旧函数体，大括号深度归零时插入 #endif
    if in_old_usbnet_bh:
        out.append(line)
        usbnet_bh_brace_depth += line.count('{') - line.count('}')
        if usbnet_bh_brace_depth <= 0 and stripped == '}':
            out.append('#endif\n')
            in_old_usbnet_bh = False
        i += 1
        continue

    # Hunk 3: tasklet_init -> INIT_WORK
    if stripped == '\t\t\t\t\tpQmapDev->usbnet_bh = dev->bh;':
        out.append('#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 0, 0)\n')
        out.append('\t\t\t\t\tpQmapDev->usbnet_bh = dev->bh_work;\n')
        out.append('\t\t\t\t\tINIT_WORK(&dev->bh_work, usbnet_bh);\n')
        out.append('#else\n')
        out.append(line)
        i += 1
        if i < len(lines):
            out.append(lines[i])   # tasklet_init 行
            i += 1
        out.append('#endif\n')
        continue

    # Hunk 4: hrtimer_init -> hrtimer_setup (Linux 6.17+)
    if stripped == '\thrtimer_init(&priv->agg_hrtimer, CLOCK_MONOTONIC, HRTIMER_MODE_REL);':
        out.append('#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 17, 0)\n')
        out.append('\thrtimer_setup(&priv->agg_hrtimer, rmnet_usb_tx_agg_timer_cb,\n')
        out.append('\t              CLOCK_MONOTONIC, HRTIMER_MODE_REL);\n')
        out.append('#else\n')
        out.append(line)
        i += 1
        if i < len(lines):
            out.append(lines[i])   # priv->agg_hrtimer.function = ...
            i += 1
        out.append('#endif\n')
        continue

    out.append(line)
    i += 1

with open(dst, 'w') as f:
    f.writelines(out)

print(f'Done. Original: {len(lines)} lines, New: {len(out)} lines')
PYEOF
python3 /tmp/make_patched2.py

# Step 3: 生成 patch
diff -u /tmp/qmi_wwan_q.c.orig /tmp/qmi_wwan_q.c.new \
  | sed 's|^--- /tmp/qmi_wwan_q.c.orig|--- a/qmi_wwan_q.c|' \
  | sed 's|^+++ /tmp/qmi_wwan_q.c.new|+++ b/qmi_wwan_q.c|' \
  > feeds/packages/kernel/quectel-qmi-wwan/patches/020-fix-usbnet-bh-api-kernel-6.x.patch
```

---

## 四、重新编译

```bash
rm -rf build_dir/target-x86_64_musl/linux-x86_64/quectel-qmi-wwan-1.2.9/

time make package/feeds/packages/quectel-qmi-wwan/compile \
  V=s -j$(nproc) 2>&1 | grep -E 'ERROR|error:|Applying|patching|time:'
```

---

## 五、验证结果

```
time: package/feeds/packages/quectel-qmi-wwan/compile#1.25#0.45#1.69
```

无 ERROR，输出：`kmod-usb-net-qmi-wwan-quectel-6.18.21.1.2.9-r2.apk` ✅

---

## 六、经验总结

### ⚠️ 坑1：unterminated #if（行号定位失效）

Python 脚本用行号（如 `i == 911`）定位函数结尾，会因其他 patch 的行号偏移而失效。

**✅ 正确做法**：用**大括号深度计数**精确定位函数结束 `}`：

```python
usbnet_bh_brace_depth += line.count('{') - line.count('}')
if usbnet_bh_brace_depth <= 0 and stripped == '}':
    out.append('#endif\n')
```

### ⚠️ 坑2：Reversed patch detected

`dry-run` 报 "Reversed" 是因为 build_dir 残留了上次失败时部分应用的 patch。

**✅ 解决**：清理 build_dir 后重新编译：

```bash
rm -rf build_dir/target-x86_64_musl/linux-x86_64/quectel-qmi-wwan-1.2.9/
```

### ⚠️ 坑3：orig 文件的基准选择

`/tmp/qmi_wwan_q.c.orig` 必须是**已应用 010/011 patch 之后**的文件（从 build_dir 复制），
而不是 tarball 解压的原始文件，否则 diff 生成的 patch 会与 OpenWrt patch 应用顺序冲突。

---

## 七、Linux 内核 API 变更速查

| API | 变更版本 | 旧写法 | 新写法 |


已保存到 Memo。以下是完整 Markdown：

````markdown
# quectel-qmi-wwan 1.2.9 编译修复：`bh_work` 版本阈值错误

> 环境：zagwrt · aarch64_generic · musl · Linux 6.12.85 · 2026-05-06

---

## 问题现象

```
error: 'struct usbnet' has no member named 'bh_work'
  901 |  schedule_work(&pQmapDev->mpNetDev->bh_work);
 2289 |  pQmapDev->usbnet_bh = dev->bh_work;
 2290 |  INIT_WORK(&dev->bh_work, usbnet_bh);
```

---

## 根因

现有 `020` patch 的版本判断阈值**写错**：

```c
// 错误写法（原 patch）：
#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 0, 0)
    dev->bh_work   // ← Linux 6.12 没有此成员！
#else
    dev->bh        // ← Linux 6.12 实际使用的是这个
#endif
```

`bh_work` 是 **Linux 6.18** 才引入的，不是 6.0。

| 内核版本 | `struct usbnet` 成员 | 类型 |
|---|---|---|
| < 6.18 | `bh` | `tasklet_struct` |
| ≥ 6.18 | `bh_work` | `work_struct` |

验证：
```bash
grep -n 'bh\b\|bh_work\|tasklet' \
  build_dir/target-aarch64_generic_musl/linux-rockchip_armv8/linux-6.12.85/include/linux/usb/usbnet.h
# 61:	struct tasklet_struct	bh;
# → 确认 Linux 6.12 只有 bh，无 bh_work
```

---

## 修复方案

**Patch 文件**：`feeds/packages/kernel/quectel-qmi-wwan/patches/020-fix-usbnet-bh-api-kernel-6.x.patch`

将 3 处 `KERNEL_VERSION(6, 0, 0)` 改为 `KERNEL_VERSION(6, 18, 0)`，`hrtimer` 相关的 `KERNEL_VERSION(6, 17, 0)` 不动。

```bash
python3 << 'PYEOF'
path = 'feeds/packages/kernel/quectel-qmi-wwan/patches/020-fix-usbnet-bh-api-kernel-6.x.patch'
with open(path, 'r') as f:
    content = f.read()

old = 'KERNEL_VERSION(6, 0, 0)'
new = 'KERNEL_VERSION(6, 18, 0)'

count = content.count(old)
print(f"Found {count} occurrences → replacing all with {new}")
content_new = content.replace(old, new)

with open(path, 'w') as f:
    f.write(content_new)
print("Done")
PYEOF
```

验证结果（3x `6,18,0` + 1x `6,17,0`）：
```
7:+#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 18, 0)   ← struct 成员
20:+#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 18, 0)  ← 函数体
44:+#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 17, 0)  ← hrtimer（不变）
58:+#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 18, 0)  ← bind 处
```

---

## 重新编译

```bash
rm -rf build_dir/target-aarch64_generic_musl/linux-rockchip_armv8/quectel-qmi-wwan-1.2.9/

make package/feeds/packages/quectel-qmi-wwan/compile V=s -j$(nproc) 2>&1 | \
  grep -E 'error:|time:|ERROR' | tail -10
```

---

## 兼容性矩阵

| 内核版本 | `bh_work` 分支 | `bh` 分支 | hrtimer |
|---|---|---|---|
| Linux 6.12 | ❌ 不走 | ✅ 走 `#else` | `hrtimer_init` ✅ |
| Linux 6.17 | ❌ 不走 | ✅ 走 `#else` | `hrtimer_setup` ✅ |
| Linux 6.18+ | ✅ 走 `#if` | ❌ 不走 | `hrtimer_setup` ✅ |

---

## 注意事项

- 原 patch 在 x86_64 + Linux 6.18 上验证通过，但阈值 `6, 0, 0` 在 6.0~6.17 之间的内核上会引入不存在的 `bh_work`，导致编译失败
- 修复后 patch 同时兼容 Linux 6.12（aarch64 rockchip）和 Linux 6.18+（x86_64）

*最后更新：2026-05-06*
````
|-----|---------|--------|--------|
| `usbnet.bh` | Linux 6.18 | `tasklet_struct bh` + `tasklet_init` | `work_struct bh_work` + `INIT_WORK` |
| `hrtimer` 初始化 | Linux 6.17 | `hrtimer_init()` + `.function =` | `hrtimer_setup()` |
````
