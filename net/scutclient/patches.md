Memo 里已有完整记录，直接输出 markdown：

````markdown
# scutclient 3.1.3 编译修复：CMake 最低版本过旧

> 环境：zagwrt · x86_64 · musl · 2026-04-25

---

## 问题

`scutclient-3.1.3` 编译失败：

```
CMake Error at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 has been removed from CMake.
  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.
  Or, add -DCMAKE_POLICY_VERSION_MINIMUM=3.5 to try configuring anyway.
```

## 根因

`CMakeLists.txt` 第一行：

```cmake
cmake_minimum_required(VERSION 2.4)
```

CMake >= 4.0 已移除对 `< 3.5` 的向后兼容支持，直接报错退出。

## 修复方案

**Patch 文件**：`feeds/packages/net/scutclient/patches/001-fix-cmake-minimum-version.patch`

```patch
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -1,4 +1,4 @@
-cmake_minimum_required(VERSION 2.4)
+cmake_minimum_required(VERSION 3.5)
 project(scutclient C)
 ADD_DEFINITIONS(-Os -Wall)
```

### 创建命令

```bash
mkdir -p feeds/packages/net/scutclient/patches

python3 -c "
content = '''--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -1,4 +1,4 @@
-cmake_minimum_required(VERSION 2.4)
+cmake_minimum_required(VERSION 3.5)
 project(scutclient C)
 ADD_DEFINITIONS(-Os -Wall)
'''
with open('feeds/packages/net/scutclient/patches/001-fix-cmake-minimum-version.patch', 'w') as f:
    f.write(content)
print('Done')
"
```

### 重新编译

```bash
rm -rf build_dir/target-x86_64_musl/scutclient-3.1.3
make package/feeds/packages/scutclient/compile V=s -j$(nproc) 2>&1 | tail -5
```

## 验证结果

```
time: package/feeds/packages/scutclient/compile#0.48#0.26#0.53
```

无 ERROR，编译成功。

## 注意事项

- 此类问题在 CMake >= 4.0 后会大量出现，凡是 `cmake_minimum_required(VERSION < 3.5)` 的老项目都会触发
- 修复方式统一：将版本号改为 `3.5` 即可，不影响实际构建行为
````
