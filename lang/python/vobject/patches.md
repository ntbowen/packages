````markdown
# vobject 0.9.9 编译修复

> 环境：zagwrt · x86_64 · musl · Python 3.14 · 2026-04-25

---

## 问题

`python3-vobject 0.9.9` 编译失败：

```
AttributeError: module 'vobject' has no attribute 'VERSION'
ERROR Backend subprocess exited when trying to invoke get_requires_for_build_wheel
```

---

## 根因

```
setup.cfg: version = attr: vobject.VERSION
    ↓
setuptools 静态 AST 解析，不执行代码
    ↓
vobject/__init__.py: from .base import VERSION  ← 动态 import，静态看不到
    ↓
vobject/base.py: VERSION = "0.9.9"              ← 实际定义在这里
    ↓
AttributeError: module 'vobject' has no attribute 'VERSION'
```

`attr:` 只能解析**直接定义在目标模块文件里的字面量赋值**，跨模块 import 的变量无法静态追踪。  
Python 3.14 + 新版 setuptools 对此直接报错，不再降级处理。

---

## 修复方案

**Patch 文件**：`feeds/packages/lang/python/vobject/patches/001-fix-setup-cfg-version-attr.patch`

```patch
--- a/setup.cfg
+++ b/setup.cfg
@@ -1,4 +1,4 @@
 [metadata]
-version = attr: vobject.VERSION
+version = 0.9.9
 
 [egg_info]
```

### 创建命令

```bash
python3 -c "
content = '''--- a/setup.cfg
+++ b/setup.cfg
@@ -1,4 +1,4 @@
 [metadata]
-version = attr: vobject.VERSION
+version = 0.9.9
 
 [egg_info]
'''
import os
os.makedirs('feeds/packages/lang/python/vobject/patches', exist_ok=True)
with open('feeds/packages/lang/python/vobject/patches/001-fix-setup-cfg-version-attr.patch', 'w') as f:
    f.write(content)
print('Done')
"
```

### 重新编译

```bash
rm -rf build_dir/target-x86_64_musl/pypi/vobject-0.9.9
make package/feeds/packages/vobject/compile V=s -j$(nproc) 2>&1 | tail -5
```

---

## 验证结果

```
time: package/feeds/packages/vobject/compile#2.05#0.33#2.43
```

无 ERROR，编译成功。

---

## 注意事项

- 升级 vobject 版本时需同步修改 patch 中的硬编码版本号
- 此问题是 setuptools `attr:` 机制的固有限制，与 Python 版本无关，只是 Python 3.14 更严格
- 使用 `python3 -c` 写入 patch 文件，避免 shell heredoc 空行截断问题
````
