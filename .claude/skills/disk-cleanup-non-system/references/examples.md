# 非系统盘清理示例

## D 盘缓存专项分析示例

输出粒度应保持在目录级和类别级：

- Edge 浏览器缓存：按 Default、Profile 1、Profile 2 等 Profile 汇总；
- 微信数据：区分纯缓存和聊天记录/图片/文件；
- 旧版本软件：列出旧版本目录，建议只保留最新版；
- node_modules / pnpm-store / venv：标记为可重建依赖，需确认项目是否还用；
- logs/temp：汇总总量；
- 输出保守合计和激进合计。

不要展开浏览器 Cache、node_modules、logs 内部的每个小文件。
