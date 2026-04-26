# mDNSResponder 2881.80.4.0.1 编译修复完整记录

> 环境：zagwrt · x86_64 · musl · 2026-04-26

---

## 一、补丁清理（失效 patch 删除）

| patch 文件 | 删除原因 |
|---|---|
| `0005-Use-list-for-changed-interfaces.patch` | 上游重写实现逻辑 |
| `0007-Mark-deleted-interfaces-as-being-changed.patch` | 上游原生合并 RTM_DELLINK 处理 |
| `0008-Handle-errors-from-socket-calls.patch` | 上游用 `recv()+MSG_TRUNC` 重写读取逻辑 |
| `0015-Add-missing-limits.h.patch` | `#include <limits.h>` 已在源码第 35 行存在 |
| `120-reproducible-builds.patch` | `__DATE__`/`__TIME__` 已全部从上游源码移除 |

---

## 二、编译错误修复：AWDL 私有 API

### 问题

```
../mDNSShared/uds_daemon.c:3629:46: error: 'request_state' has no member named 'resolve_awdl'
../mDNSShared/uds_daemon.c:3629:89: error: 'AWDLInterfaceID' undeclared
```

### 根因

上游引入了 Apple AWDL 私有接口，未用 `#ifdef` 保护，Linux 构建无此成员和符号：

```c
// 问题行
const mDNSBool is_split_awdl_query = (req->resolve_awdl && question->InterfaceID == AWDLInterfaceID);
```

该变量还在 3720、3727 行的 `LogRedact` 中作为日志参数使用。

### 修复

**Patch**：`101-linux-fix-awdl-compile.patch`

Linux 下直接定义为 `mDNSfalse`（语义正确：Linux 无 AWDL），编译器优化掉 dead code：

````patch
--- a/mDNSShared/uds_daemon.c
+++ b/mDNSShared/uds_daemon.c
@@ -3627,7 +3627,11 @@
     const mDNSu32 name_hash = mDNS_DomainNameFNV1aHash(&question->qname);
     const mDNSBool isMDNSQuestion = mDNSOpaque16IsZero(question->TargetQID);
+#if defined(TARGET_OS_LINUX) && TARGET_OS_LINUX
+    const mDNSBool is_split_awdl_query = mDNSfalse;
+#else
     const mDNSBool is_split_awdl_query = (req->resolve_awdl && question->InterfaceID == AWDLInterfaceID);
+#endif
     UDS_LOG_ANSWER_EVENT(isMDNSQuestion ? MDNS_LOG_CATEGORY_MDNS : MDNS_LOG_CATEGORY_DEFAULT, MDNS_LOG_DEFAULT,
````

---

## 三、Patch Hunk FAILED 排查经验

**根因**：`@@` 行的函数签名过长，patch 工具截断后无法匹配。

**解决**：不用函数签名行作锚点，改用函数体内的**短变量声明行**作为起始行：

```
# ❌ 错误：超长函数签名作锚点
@@ -3626,7 +3626,11 @@ mDNSlocal void resolve_result_callback(mDNS *const m, ...

# ✅ 正确：函数体内短行作锚点
@@ -3627,7 +3627,11 @@
     const mDNSu32 name_hash = ...
```

---

## 四、最终补丁列表

```
patches/
├── 0001-Create-subroutine-for-cleaning-recent-interfaces.patch   ✅ 保留
├── 0002-Create-subroutine-for-tearing-down-an-interface.patch    ✅ 保留
├── 0002-make-Set-libdns_sd.so-soname-correctly.patch             ✅ 保留
├── 0003-Track-interface-socket-family.patch                      ✅ 保留
├── 0004-Indicate-loopback-interface-to-mDNS-core.patch           ✅ 保留
├── 0005-mDNSCore-Fix-broken-debug-parameter.patch                ✅ 重写
├── 100-linux_fixes.patch                                        ✅ 保留
└── 101-linux-fix-awdl-compile.patch                            🆕 新增
```

---

✅ **编译成功，无 ERROR。** 已存入 Memo。
