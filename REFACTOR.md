# DexManager 模块化重构任务

## 概述

将 `DexManager.java` 从 4000+ 行拆分为多个独立模块，提高代码可维护性。

## 已完成

### 阶段1: 工具类拆分 ✅
- **SmaliUtils.java** (~220 行) - Smali 代码操作工具
  - `extractMethodSmali` / `extractMethodFromSmali`
  - `insertMethodToSmali` / `removeMethodFromSmali`
  - `replaceMethodInSmali`
  - `insertFieldToSmali` / `removeFieldFromSmali`
  - `countMatches`

- **FileUtils.java** (~85 行) - 文件操作工具
  - `collectSmaliFiles`
  - `readFileContent` / `writeFileContent`
  - `readFileBytes`
  - `deleteRecursive`

### 阶段2: APK 资源操作拆分 ✅
- **ApkResourceOperations.java** (~480 行) - Manifest/资源操作
  - `getManifestFromApk` / `getManifestFallback`
  - `modifyManifestInApk` / `replaceInManifest`
  - `listResourcesInApk` / `getResourceFromApk`
  - `modifyResourceInApk`
  - `decodeAxml` / `encodeAxml`

### 清理的遗留代码
- `replaceStringInAxml` (被 AxmlEditor 替代)
- `readStringFromAxml` (未使用)
- `getStringDataStart` (未使用)
- `ReplaceResult` 内部类 (未使用)

## 待完成

### 阶段3: 搜索操作 (优先级: 高)
**目标文件**: `ops/DexSearchOperations.java`

| 方法 | 行号 | 说明 |
|------|------|------|
| `searchString` | 1001 | 搜索字符串 |
| `searchCode` | 1050 | 搜索代码 |
| `searchMethod` | 1080 | 搜索方法 |
| `searchField` | 1129 | 搜索字段 |
| `findMethodXrefs` | 1180 | 方法交叉引用 |
| `findFieldXrefs` | 1217 | 字段交叉引用 |

### 阶段4: 多DEX会话管理 (优先级: 中)
**目标文件**: `ops/MultiDexOperations.java`

| 方法 | 行号 | 说明 |
|------|------|------|
| `openMultipleDex` | 1867 | 打开多DEX |
| `listAllSessions` | 1927 | 列出会话 |
| `closeMultiDexSession` | 1957 | 关闭会话 |
| `getClassesFromMultiSession` | 1965 | 获取类列表 |
| `searchInMultiSession` | 2025 | 会话内搜索 |
| `getClassSmaliFromSession` | 2095 | 获取Smali |
| `modifyClassInSession` | 2128 | 修改类 |
| `addClassToSession` | 2170 | 添加类 |
| `deleteClassFromSession` | 2204 | 删除类 |
| `saveMultiDexSessionToApk` | 2576 | 保存到APK |

### 阶段5: APK文件操作 (优先级: 中)
**目标文件**: `ops/ApkFileOperations.java`

| 方法 | 行号 | 说明 |
|------|------|------|
| `listApkFiles` | 3080 | 列出APK文件 |
| `readApkFile` | 3151 | 读取文件 |
| `deleteFileFromApk` | 2426 | 删除文件 |
| `addFileToApk` | 2497 | 添加文件 |
| `searchTextInApk` | 3245 | 搜索文本 |

### 阶段6: DEX工具操作 (优先级: 低)
**目标文件**: `ops/DexToolsOperations.java`

| 方法 | 行号 | 说明 |
|------|------|------|
| `disassemble` | 906 | 反编译 |
| `assemble` | 955 | 编译 |
| `fixDex` | 1280 | 修复DEX |
| `mergeDex` | 1303 | 合并DEX |
| `splitDex` | 1328 | 拆分DEX |

### 阶段7: APK-DEX操作 (优先级: 低)
**目标文件**: `ops/ApkDexOperations.java`

| 方法 | 行号 | 说明 |
|------|------|------|
| `listDexClassesFromApk` | 1550 | 列出类 |
| `getDexStringsFromApk` | 1605 | 获取字符串 |
| `searchInDexFromApk` | 1711 | 搜索 |
| `listDexFilesInApk` | 1831 | 列出DEX文件 |
| `getClassSmaliFromApk` | 2819 | 获取Smali |
| `saveClassSmaliToApk` | 2859 | 保存Smali |

## 文件结构

```
com.aetherlink.dexeditor/
├── DexManager.java          # 核心协调器 (目标: <1500行)
├── DexEditorPlugin.java     # Capacitor 插件入口
├── CppDex.java              # C++ JNI 接口
├── AxmlEditor.java          # AXML 编辑器
├── AxmlParser.java          # AXML 解析器
├── utils/
│   ├── SmaliUtils.java      # ✅ Smali 工具
│   └── FileUtils.java       # ✅ 文件工具
├── ops/
│   ├── ApkResourceOperations.java    # ✅ 资源操作
│   ├── DexSearchOperations.java      # 待完成
│   ├── MultiDexOperations.java       # 待完成
│   ├── ApkFileOperations.java        # 待完成
│   ├── DexToolsOperations.java       # 待完成
│   └── ApkDexOperations.java         # 待完成
└── editor/                  # 原生 Smali 编辑器
```

## 进度追踪

| 阶段 | 状态 | 减少行数 | 版本 |
|------|------|---------|------|
| 阶段1 | ✅ 完成 | -170 行 | v0.0.83 |
| 阶段2 | ✅ 完成 | -604 行 | v0.0.83 |
| 阶段3 | ⏳ 待完成 | ~350 行 | - |
| 阶段4 | ⏳ 待完成 | ~500 行 | - |
| 阶段5 | ⏳ 待完成 | ~300 行 | - |
| 阶段6 | ⏳ 待完成 | ~200 行 | - |
| 阶段7 | ⏳ 待完成 | ~400 行 | - |

**当前 DexManager.java**: 3390 行
**目标**: <1500 行

## 注意事项

1. 每个阶段拆分后必须编译测试通过
2. 保持向后兼容 - DexManager 中保留委托方法
3. 新模块使用静态方法，避免状态管理复杂性
4. 涉及会话管理的模块需要传递必要的上下文
