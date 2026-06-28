---
name: disk-cleanup-non-system
description: Use this skill when the user wants to analyze, organize, or clean a specified non-system Windows drive such as D:, E:, F:, or an external SSD/HDD. Operate only inside the user-specified drive letter, use shallow-first scanning, avoid reading huge small-file trees, classify results, and require confirmation before deletion or movement.
---

# Windows 非系统盘清理 Skill

## 触发条件

当用户想清理 D/E/F 盘、外接硬盘、项目盘、游戏盘、素材盘、下载盘，或想找大文件、旧安装包、缓存、构建产物、重复项目时，使用本 Skill。

用户必须指定盘符。没有指定时先询问，不能自行选择。

可测试触发词：

```text
/disk-cleanup-non-system 帮我清理D盘
/disk-cleanup-non-system 分析E盘被什么占了
/disk-cleanup-non-system 只在D盘内找可清理内容，不要跨盘
/disk-cleanup-non-system 整理外接硬盘F盘
```

## 输入格式

必须包含目标盘符，例如 D:、E:、F:。可选提供清理目标、截图、目录树、扫描结果、不可删除内容、是否允许删除缓存、是否允许归档或移动。

## 输出格式

必须输出为：

1. 目标范围：只处理 `<TARGET_DRIVE>:\`，不得跨盘符；
2. 当前诊断：容量、已用、可用、Top 大目录/文件；
3. 分类结果：缓存、安装包、项目依赖、构建产物、日志、临时文件、游戏、素材、备份、未知目录；
4. 可清理潜力：保守、标准、激进三档；
5. 用户确认清单：路径、类型、大小、建议操作、风险。

输出应类似“D 盘缓存专项分析”：浏览器 Profile 级、应用级、项目级、目录级汇总，而不是单文件列表。

## 盘符边界

如果用户指定 `<TARGET_DRIVE>`，所有扫描、统计、删除、移动、归档都必须限制在 `<TARGET_DRIVE>:\` 下。不得访问 C:\、其他盘符、网络盘或外接盘，除非用户重新明确指定。

## 扫描策略

必须浅层优先、逐步深入、超时即停。禁止默认递归扫描整个盘。

**第一步必须是 `ls` 看目录名，不是 `du`。** 目录名已经透露足够分类信息——通过名字先把目录分为「可能可清理」「受保护」「未知」三类，再针对性地获取大小。

推荐顺序：

1. 确认目标盘符；
2. 读取目标盘容量（`df -h`）；
3. **先用 `ls -la` 列出目标盘根目录所有一级目录名和修改时间**，不做任何大小统计；
4. 根据目录名将一级目录分为三类：
   - 🛡️ 保护类：游戏名（Naraka/Steam/Genshin 等）、素材/视频/照片、备份/资料、虚拟机、数据库。只取总大小，不深入；
   - 🧹 可清理候选：明确含 cache/temp/tmp/log/download/旧版本号 的目录，以及项目目录（可能有 node_modules/venv/build）；
   - ❓ 未知类：无法从名字判断的，先取一级大小再决定；
5. 对可清理候选和未知类**逐个**用 `du -sh` 或 `du --max-depth=1` + 超时包装获取大小。**大目录和小目录不要合并在一个批量命令中**——一个大目录会拖死整批；
6. 只对确认有清理潜力的目录深入 2～3 层；
7. 深度扫描必须限定具体目录，例如 `D:\Projects\some-project`，不能深扫整个 `D:\Projects`；

**超时阈值**：单目录 `du -sh` 或 `du --max-depth=1` 默认超时 **15 秒**。实现方式：`du ... & PID=$!; sleep 15; kill $PID 2>/dev/null; wait $PID 2>/dev/null`。超时后跳过该目录、记录原因、标记「建议单独分析」，**不要反复重试同一目录超过 2 次**。

以下目录类型 **depth-0（只取总大小）即可，不要做 depth-1**：node_modules、.git、venv、.venv、__pycache__、dist、build、target、.next、.nuxt、.turbo、.cache、Cache、Code Cache、GPUCache、temp、tmp、logs、pnpm-store、.pnpm-store、npm/pip/pacman 缓存、.cloud_cache_*（云同步本地缓存）。这些目录内部结构没有决策价值——要么是可重建依赖，要么是纯缓存。

**区分「获取总大小」和「深入展开」**：`du -sh` 获取一个目录的总大小不算深入——即使对游戏/素材目录，总大小也是用户需要知道的。深入展开是指列出子目录明细、或进入第 3+ 层。

如果通过目录名和少量样本已能判断类型，就早停：游戏、素材、照片、视频、备份、虚拟机、数据库、聊天记录、项目源码、未知资料目录——只标注总大小，不展开内部结构、不删除。

遇到权限拒绝、Junction/符号链接循环风险时，跳过当前目录，记录原因，继续分析其他目录。

## 可以自动执行

只读容量统计、一级目录扫描、Top 大项统计、缓存模式识别、分类汇总、可清理潜力估算、执行后复查可以自动执行。

## 必须询问用户

以下必须询问：用户未指定盘符、发现游戏/项目/素材/备份/虚拟机/数据库/未知大目录、删除超过 1GB 的目录、批量删除、多目录移动、跨盘迁移、删除 node_modules/venv、清理旧版本软件、删除压缩包合集。

## 清理分级

保守清理：低风险缓存、日志、临时文件。

标准清理：浏览器缓存、应用纯缓存、包管理器缓存、旧日志、临时目录、旧版本软件（保留最新版即可）。

激进清理：node_modules、venv、旧构建产物、可重建项目依赖。激进清理必须单独确认。

## 执行前确认清单

任何删除、移动、归档前，必须先输出：

| 序号 | 路径 | 类型 | 大小 | 建议操作 | 风险 |
|---|---|---:|---:|---|---|

用户没有明确选择前，不得执行破坏性操作。

## 参考资料

优先读取 references/ 下的非系统盘边界规则、项目缓存模式、游戏和素材保护规则、扫描性能规则、输出粒度规则和示例。
