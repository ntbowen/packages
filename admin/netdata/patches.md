````markdown
# netdata v2.10.3 编译修复：libbacktrace 交叉编译工具链传递

> **环境**：zagwrt · aarch64_generic · musl · GCC 14.3.0 · 2026-05-05

---

## 一、问题现象

链接阶段报错：

```
/usr/bin/ld: libbacktrace.a: error adding symbols: file in wrong format
collect2: error: ld returned 1 exit status
```

`file` 检查发现 `libbacktrace.a` 是 `x86_64` 架构，而非 `aarch64`。

---

## 二、根因分析

`packaging/cmake/Modules/NetdataBacktrace.cmake` 使用 `ExternalProject_Add` 构建 libbacktrace，但 `CONFIGURE_COMMAND` 直接调用 `./configure`，**未传递 OpenWrt 交叉编译工具链变量**（`CC`、`AR`、`RANLIB`、`--host`），导致 autoconf 使用宿主机 native 编译器（x86_64）编译出错误架构的静态库。

```cmake
# 原始（有问题）
CONFIGURE_COMMAND "${libbacktrace_SOURCE_DIR}/configure"
    --prefix=${libbacktrace_INSTALL_DIR}
    --enable-static
```

---

## 三、修复方案

**Patch 文件**：`feeds/packages/admin/netdata/patches/001-libbacktrace-cross-compile.patch`

### 3.1 生成 Patch（必须用 diff，不能手写）

> ⚠️ 该文件含 `INSTALL_COMMAND ""` 行，手写 patch 时 `""` 会被 patch 工具误判为格式标记，导致 `malformed patch`。**必须用 `diff -U3` 生成**。

```bash
mkdir -p feeds/packages/admin/netdata/patches

# Step 1：备份原文件
cp build_dir/target-aarch64_generic_musl/netdata-v2.10.3/packaging/cmake/Modules/NetdataBacktrace.cmake \
   /tmp/NetdataBacktrace.cmake.orig

# Step 2：生成修改后文件
python3 << 'PYEOF'
with open('/tmp/NetdataBacktrace.cmake.orig', 'r') as f:
    content = f.read()

old = '                CONFIGURE_COMMAND "${libbacktrace_SOURCE_DIR}/configure" --prefix=${libbacktrace_INSTALL_DIR} --enable-static'
new = '''                CONFIGURE_COMMAND ${CMAKE_COMMAND} -E env
                    "CC=${CMAKE_C_COMPILER}"
                    "AR=${CMAKE_AR}"
                    "RANLIB=${CMAKE_RANLIB}"
                    "CFLAGS=${CMAKE_C_FLAGS}"
                    "${libbacktrace_SOURCE_DIR}/configure"
                    --host=${CMAKE_SYSTEM_PROCESSOR}-linux-musl
                    --prefix=${libbacktrace_INSTALL_DIR}
                    --enable-static
                    --disable-shared'''

assert old in content, "找不到目标行！"
content_new = content.replace(old, new, 1)

with open('/tmp/NetdataBacktrace.cmake.new', 'w') as f:
    f.write(content_new)
print('new file written OK')
PYEOF

# Step 3：用 diff 生成 patch
diff -U3 \
  /tmp/NetdataBacktrace.cmake.orig \
  /tmp/NetdataBacktrace.cmake.new \
  | sed 's|/tmp/NetdataBacktrace.cmake.orig|a/packaging/cmake/Modules/NetdataBacktrace.cmake|' \
  | sed 's|/tmp/NetdataBacktrace.cmake.new|b/packaging/cmake/Modules/NetdataBacktrace.cmake|' \
  > feeds/packages/admin/netdata/patches/001-libbacktrace-cross-compile.patch
```

### 3.2 Patch 内容

```patch
--- a/packaging/cmake/Modules/NetdataBacktrace.cmake
+++ b/packaging/cmake/Modules/NetdataBacktrace.cmake
@@ -21,7 +21,16 @@
                 GIT_REPOSITORY https://github.com/ianlancetaylor/libbacktrace.git
                 SOURCE_DIR "${libbacktrace_SOURCE_DIR}"
                 BINARY_DIR "${libbacktrace_BINARY_DIR}"
-                CONFIGURE_COMMAND "${libbacktrace_SOURCE_DIR}/configure" --prefix=${libbacktrace_INSTALL_DIR} --enable-static
+                CONFIGURE_COMMAND ${CMAKE_COMMAND} -E env
+                    "CC=${CMAKE_C_COMPILER}"
+                    "AR=${CMAKE_AR}"
+                    "RANLIB=${CMAKE_RANLIB}"
+                    "CFLAGS=${CMAKE_C_FLAGS}"
+                    "${libbacktrace_SOURCE_DIR}/configure"
+                    --host=${CMAKE_SYSTEM_PROCESSOR}-linux-musl
+                    --prefix=${libbacktrace_INSTALL_DIR}
+                    --enable-static
+                    --disable-shared
                 BUILD_COMMAND make install
                 INSTALL_COMMAND ""
                 BUILD_BYPRODUCTS "${libbacktrace_LIBRARY}"
```

### 3.3 dry-run 验证

```bash
patch --dry-run -p1 \
  -d build_dir/target-aarch64_generic_musl/netdata-v2.10.3 \
  -i $(pwd)/feeds/packages/admin/netdata/patches/001-libbacktrace-cross-compile.patch
# 期望输出：checking file packaging/cmake/Modules/NetdataBacktrace.cmake  ✅
```

---

## 四、重新编译

```bash
rm -rf build_dir/target-aarch64_generic_musl/netdata-v2.10.3/

make package/feeds/packages/netdata/compile V=s -j$(nproc) 2>&1 | \
  grep -E "error:|libbacktrace|aarch64|EM:|configure|time:" | tail -30
```

**观察要点：**

| 关键词 | 期望 |
|--------|------|
| `checking host system type` | 应出现 `aarch64` |
| `EM:` | 不应出现 `x86_64`（EM:62），应为 `aarch64`（EM:183） |
| `error:` | 无 |
| `time: package/feeds/packages/netdata/compile` | 出现即成功 ✅ |

---

## 五、经验总结

### 5.1 patch 含 `""` 空引号时必须用 diff 生成

手写 patch 时，`INSTALL_COMMAND ""` 中的 `""` 会被 patch 工具误判为格式标记：

```
malformed patch at line N:                  INSTALL_COMMAND ""
```

**解决方案**：用 `diff -U3` 对实际文件生成 patch，再用 `sed` 替换路径头。

### 5.2 CMake ExternalProject_Add 交叉编译工具链注入方式

| 变量 | 说明 |
|------|------|
| `${CMAKE_COMMAND} -E env` | CMake 跨平台环境变量注入，不依赖 shell |
| `CMAKE_C_COMPILER` | 由 OpenWrt `cmake.mk` 通过 toolchain file 传入 |
| `CMAKE_AR` / `CMAKE_RANLIB` | 同上 |
| `--host=aarch64-linux-musl` | 告知 autoconf 目标架构 |
| `--disable-shared` | 只编译静态库，避免 `.so` 安装问题 |

*最后更新：2026-05-05*
````
