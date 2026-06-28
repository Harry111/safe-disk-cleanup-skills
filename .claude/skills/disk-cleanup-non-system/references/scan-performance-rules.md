# 扫描性能与早停规则

## 核心原则

够用即可，不追求读完整盘。默认浅层优先、逐步深入、超时即停。

## 禁止

- 禁止默认全盘深度递归；
- 禁止逐个读取大量细碎小文件；
- 禁止跨盘符扫描；
- 禁止跟随 Junction 或符号链接无限深入；
- 禁止为了完整性强行读完权限异常或极慢目录。

## 默认限制

- 默认扫描深度：2～3 层；
- 默认输出数量：Top 20～50 项；
- 大目录先看总大小，再决定是否深入；
- 小文件爆炸目录只统计大小、数量、修改时间。

## 超时与重试

- 单目录 `du -sh` 或 `du --max-depth=1` 默认超时 **15 秒**；
- bash 实现：`du ... & PID=$!; sleep 15; kill $PID 2>/dev/null; wait $PID 2>/dev/null`；
- 超时后跳过该目录、记录原因、标记「建议单独分析」；
- **同一目录最多重试 2 次**，不要反复死磕；
- 权限拒绝、Junction 循环同理——跳过，不要重试。

## depth-0 vs depth-1

以下目录类型 **depth-0 够用**（只取总大小），不需要 depth-1：

- node_modules、.git、venv、.venv、__pycache__
- dist、build、target、.next、.nuxt、.turbo
- .cache、Cache、Code Cache、GPUCache
- temp、tmp、logs
- pnpm-store、.pnpm-store、npm cache、pip cache
- .cloud_cache_*（云同步本地缓存）

原因：内部结构没有决策价值。要么是可重建依赖（删不删取决于项目是否还在用），要么是纯缓存（可以直接清理）。

## 只统计不展开的目录

- node_modules
- .git
- .venv / venv
- __pycache__
- dist / build / target
- .cache / Cache / Code Cache / GPUCache
- temp / tmp
- logs
- pnpm-store
- npm cache
- pip cache
- pacman cache

## 早停规则

如果少量样本已经能判断目录类型，就停止深入：

- 浏览器缓存：统计 Profile 或目录总量即可；
- node_modules：标记项目依赖即可；
- .git：标记 Git 仓库即可；
- 游戏、素材、照片、视频、论文、报告、备份、数据库、虚拟机、聊天记录：标记保护，停止深入；
- 未知大目录：标记需用户确认。

## 异常处理

遇到长时间无输出、权限拒绝、文件数量过多、Junction 风险、命令执行时间明显过长，应跳过当前目录，输出路径、原因、是否建议单独分析。
