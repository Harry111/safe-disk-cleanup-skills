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

推荐顺序：

1. 确认目标盘符；
2. 读取目标盘容量；
3. 扫描目标盘一级目录；
4. 找 Top 20～50 大目录和大文件；
5. 只深入 Top 大项或明显可疑目录；
6. 默认深入 2～3 层；
7. 深度扫描必须限定具体目录，例如 `D:\Projects`，不能直接深扫整个 `D:\`。

大量小文件目录只统计总大小、文件数量、最后修改时间，不展开文件级明细。典型包括 node_modules、.git、venv、__pycache__、dist、build、target、.cache、Cache、temp、tmp、logs、pnpm-store、npm/pip/pacman 缓存。

如果通过目录名和少量样本已能判断类型，就早停：游戏、素材、照片、视频、备份、虚拟机、数据库、聊天记录、项目源码、未知资料目录默认保护，只标注，不深入、不删除。

遇到长时间无输出、权限拒绝、文件数量过多、Junction/符号链接循环风险时，跳过当前目录，记录原因，继续分析其他目录。

## 可以自动执行

只读容量统计、一级目录扫描、Top 大项统计、缓存模式识别、分类汇总、可清理潜力估算、执行后复查可以自动执行。

## 必须询问用户

以下必须询问：用户未指定盘符、发现游戏/项目/素材/备份/虚拟机/数据库/未知大目录、删除超过 1GB 的目录、批量删除、多目录移动、跨盘迁移、删除 node_modules/venv、清理旧版本软件、删除压缩包合集。

## 清理分级

保守清理：低风险缓存、日志、临时文件。

标准清理：浏览器缓存、应用纯缓存、包管理器缓存、旧日志、临时目录。

激进清理：node_modules、venv、旧版本软件、旧构建产物、可重建项目依赖。激进清理必须单独确认。

## 执行前确认清单

任何删除、移动、归档前，必须先输出：

| 序号 | 路径 | 类型 | 大小 | 建议操作 | 风险 |
|---|---|---:|---:|---|---|

用户没有明确选择前，不得执行破坏性操作。

## 参考资料

优先读取 references/ 下的非系统盘边界规则、项目缓存模式、游戏和素材保护规则、扫描性能规则、输出粒度规则和示例。
