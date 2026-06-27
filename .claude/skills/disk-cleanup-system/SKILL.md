---
name: disk-cleanup-system
description: Use this skill when the user wants to safely analyze, clean, or reclaim space on a Windows system drive such as C:. Diagnose disk usage with shallow-first scanning, protect system files, browser login data, and personal files, then produce a staged confirmation-based cleanup plan. Never run destructive actions without explicit approval.
---

# Windows 系统盘安全清理 Skill

## 触发条件

当用户提到 C 盘满了、系统盘空间不足、想知道系统盘被什么占了、想安全清理 Windows、清理 Temp/浏览器缓存/开发缓存/WinSxS/hiberfil.sys，或想把微信、WPS、Edge、Chrome、Office、腾讯会议等应用数据迁移到其他盘时，使用本 Skill。

可测试触发词：

```text
/disk-cleanup-system 帮我安全清理C盘
/disk-cleanup-system C盘满了，不要丢浏览器登录
/disk-cleanup-system 分析系统盘被什么占了
/disk-cleanup-system 把C盘大应用数据迁移到D盘
```

## 输入格式

用户可提供：目标系统盘、可用目标盘、磁盘截图、PowerShell 输出、目录占用结果、是否允许管理员权限、是否保留浏览器登录、哪些应用或目录不能动。

如未提供，先执行只读探测或给出只读命令。不要默认删除任何内容。

## 输出格式

必须输出为：

1. 当前诊断：系统盘容量、已用、可用、Top 大项；
2. 分类结果：安全缓存、系统维护项、应用数据、个人数据、未知目录、禁止触碰；
3. 可清理潜力：保守、标准、激进三档估算；
4. 需要确认的操作清单；
5. 执行后复查：释放空间、应用是否正常、Junction 是否有效。

最终输出应偏“目录级/类别级汇总”，不要输出海量单文件列表。

## 扫描策略

必须浅层优先、逐步深入、超时即停。不要一开始递归扫描整个系统盘。

推荐顺序：

1. 读取系统盘容量；
2. 扫描系统盘一级目录；
3. 找出 Top 20～50 大目录；
4. 只对明显的大目录继续深入，如 Users、AppData、WinSxS、ProgramData、浏览器缓存、开发缓存；
5. 默认深入 2～3 层即可；
6. 深度扫描必须限定具体目录，并先说明原因。

遇到大量小文件目录，只统计总大小、文件数量、最后修改时间，不展开内部文件。典型包括 node_modules、.git、venv、__pycache__、Cache、Code Cache、GPUCache、Docker overlay、logs、temp、npm/pip/pnpm 缓存。

如果读了少量信息已经能判断某目录不能直接删除，例如游戏、聊天记录、个人资料、数据库、虚拟机、备份、论文、素材、未知应用数据，应立即早停并标注“需用户确认”，不要继续深挖。

遇到长时间无输出、权限拒绝、Junction/符号链接循环风险、系统目录异常、文件数量过多时，跳过该目录，记录原因，继续分析其他目录。不要为了完整性强行读完。

## 可以自动执行

只读扫描、容量统计、Top 大项统计、缓存目录识别、是否管理员检测、输出方案、执行后复查可以自动执行。

低风险清理也必须先获得用户授权，例如 Windows Temp、用户 Temp、浏览器 Cache/Code Cache/GPUCache、npm/pip/pnpm 缓存、VS Code/JetBrains 缓存、旧日志。

## 必须询问用户

以下操作必须明确确认：删除未知目录、删除非缓存目录、Docker prune、关闭休眠、DISM 组件清理、迁移应用数据、创建 Junction、删除迁移备份、清理浏览器登录相关数据、删除项目依赖、删除旧版本软件。

## 禁止触碰

默认禁止删除 System32、SysWOW64、Boot、Recovery、pagefile.sys、swapfile.sys、浏览器 Cookies/Login Data/Preferences/Local Storage/IndexedDB，以及 Desktop、Documents、Pictures、Videos 中无法判断用途的个人文件。

hiberfil.sys 不允许手动删除，只能在用户确认后通过 `powercfg -h off` 处理。

## 执行前确认清单

任何删除、移动、迁移、清空前，必须先输出：

| 序号 | 路径 | 类型 | 大小 | 建议操作 | 风险 |
|---|---|---:|---:|---|---|

用户没有明确选择前，不得执行破坏性操作。

## 参考资料

优先读取 references/ 下的安全规则、动态路径规则、浏览器缓存规则、扫描性能规则、输出粒度规则、Junction 迁移规则和示例。
