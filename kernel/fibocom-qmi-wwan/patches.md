````markdown
# fibocom-qmi-wwan 1.0.5 编译修复

> 环境：zagwrt · x86_64 · musl · Linux 6.18 · 2026-04-26

---

## 问题

```
qmi_wwan_f.c:1211:5: error: implicit declaration of function 'hrtimer_init';
did you mean 'hrtimers_init'? [-Wimplicit-function-declaration]
```

---

## 根因

Linux 6.17 内核 API 变更：`hrtimer_init()` + 手动赋值 `.function` 的两步初始化方式被废弃，合并为一步完成的 `hrtimer_setup()`。

| 版本 | 写法 |
|------|------|
| Linux < 6.17 | `hrtimer_init(&t, clock, mode);` + `t.function = cb;` |
| Linux >= 6.17 | `hrtimer_setup(&t, cb, clock, mode);` |

---

## 修复方案

**Patch 文件**：`feeds/packages/kernel/fibocom-qmi-wwan/patches/020-fix-hrtimer-api-kernel-6.17.patch`

```patch
--- a/qmi_wwan_f.c
+++ b/qmi_wwan_f.c
@@ -1208,8 +1208,13 @@ static int qmap_register_device(struct qmap_priv *priv)
     priv->agg_skb = NULL;
     priv->agg_count = 0;
+#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 17, 0)
+    hrtimer_setup(&priv->agg_hrtimer, rmnet_usb_tx_agg_timer_cb,
+                  CLOCK_MONOTONIC, HRTIMER_MODE_REL);
+#else
     hrtimer_init(&priv->agg_hrtimer, CLOCK_MONOTONIC, HRTIMER_MODE_REL);
     priv->agg_hrtimer.function = rmnet_usb_tx_agg_timer_cb;
+#endif
     INIT_WORK(&priv->agg_wq, rmnet_usb_tx_agg_work);
     ktime_get_ts64(&priv->agg_time);
     spin_lock_init(&priv->agg_lock);
```

### 创建 Patch 命令

```bash
python3 -c "
content = '''--- a/qmi_wwan_f.c
+++ b/qmi_wwan_f.c
@@ -1208,8 +1208,13 @@ static int qmap_register_device(struct qmap_priv *priv)
     priv->agg_skb = NULL;
     priv->agg_count = 0;
+#if LINUX_VERSION_CODE >= KERNEL_VERSION(6, 17, 0)
+    hrtimer_setup(&priv->agg_hrtimer, rmnet_usb_tx_agg_timer_cb,
+                  CLOCK_MONOTONIC, HRTIMER_MODE_REL);
+#else
     hrtimer_init(&priv->agg_hrtimer, CLOCK_MONOTONIC, HRTIMER_MODE_REL);
     priv->agg_hrtimer.function = rmnet_usb_tx_agg_timer_cb;
+#endif
     INIT_WORK(&priv->agg_wq, rmnet_usb_tx_agg_work);
     ktime_get_ts64(&priv->agg_time);
     spin_lock_init(&priv->agg_lock);
'''
with open('feeds/packages/kernel/fibocom-qmi-wwan/patches/020-fix-hrtimer-api-kernel-6.17.patch', 'w') as f:
    f.write(content)
print('patch written')
"
```

### 重新编译

```bash
rm -rf build_dir/target-x86_64_musl/linux-x86_64/fibocom-qmi-wwan-1.0.5/
make package/feeds/packages/fibocom-qmi-wwan/compile V=s -j$(nproc) 2>&1 | tail -15
```

---

## 验证结果

```
time: package/feeds/packages/fibocom-qmi-wwan/compile#1.23#0.47#1.68
```

无 ERROR，编译成功。✅

---

## 注意事项

- `hrtimer_setup()` 是 Linux 6.17 引入的，用 `LINUX_VERSION_CODE >= KERNEL_VERSION(6, 17, 0)` 做版本判断，保持向下兼容
- 同包中已有 `010-fix-build-for-kernel-6.6.patch`，本 patch 编号为 `020`，按序应用
- 同类内核模块（如 `qmi_wwan_q`、`qmi_wwan_s`）若也使用 `hrtimer_init`，用相同模式处理
````
