````markdown
# tinyproxy 1.11.3 编译修复

> 环境：zagwrt · x86_64 · musl · 2026-04-26

---

## 问题

```
Hunk #2 FAILED at 124.
1 out of 2 hunks FAILED -- saving rejects to file etc/tinyproxy.conf.in.rej
Patch failed! Please fix feeds/packages/net/tinyproxy/patches/020-config_and_pid-path.patch
```

## 根因

`tinyproxy-1.11.3/etc/tinyproxy.conf.in` 中 PidFile 路径变量名已更新：

| | 旧版（patch `-` 行） | 新版（实际文件） |
|---|---|---|
| PidFile | `@localstatedir@/run/tinyproxy/tinyproxy.pid` | `@runstatedir@/tinyproxy/tinyproxy.pid` |

旧 patch 的上下文行与实际文件不匹配，导致 Hunk #2 失败。

---

## 修复：重写 patch

路径：`feeds/packages/net/tinyproxy/patches/020-config_and_pid-path.patch`

```bash
python3 -c "
content = '''--- a/etc/tinyproxy.conf.in
+++ b/etc/tinyproxy.conf.in
@@ -93,7 +93,7 @@ StatFile \"@pkgdatadir@/stats.html\"
 # exclusive. If neither Syslog nor LogFile are specified, output goes
 # to stdout.
 #
-#LogFile \"@localstatedir@/log/tinyproxy/tinyproxy.log\"
+LogFile \"@localstatedir@/log/tinyproxy.log\"
 
 #
 # Syslog: Tell tinyproxy to use syslog instead of a logfile.  This
@@ -124,7 +124,7 @@ LogLevel Info
 # can be used for signalling purposes.
 # If not specified, no pidfile will be written.
 #
-#PidFile \"@runstatedir@/tinyproxy/tinyproxy.pid\"
+PidFile \"@localstatedir@/tinyproxy.pid\"
 
 #
 # XTinyproxy: Tell Tinyproxy to include the X-Tinyproxy header, which
'''
with open('feeds/packages/net/tinyproxy/patches/020-config_and_pid-path.patch', 'w') as f:
    f.write(content)
print('done')
"
```

---

## 重新编译

```bash
rm -rf build_dir/target-x86_64_musl/tinyproxy-1.11.3/
make package/feeds/packages/tinyproxy/compile V=s -j$(nproc) 2>&1 | tail -10
```

---

## 验证结果

```
time: package/feeds/packages/tinyproxy/compile#2.47#1.09#2.02
```

无 ERROR，编译成功。
````
