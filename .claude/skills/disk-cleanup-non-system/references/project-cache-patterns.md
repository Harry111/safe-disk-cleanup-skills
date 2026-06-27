# 项目缓存与依赖模式

以下目录通常属于可重建依赖或构建产物，但必须确认项目是否还在使用：

- node_modules
- .pnpm-store
- dist
- build
- target
- .next
- .nuxt
- .turbo
- .cache
- __pycache__
- .venv
- venv
- logs
- temp
- tmp

处理原则：

- 不展开大量小文件；
- 只统计目录总大小；
- 标注所属项目路径；
- 询问用户项目是否还在用；
- node_modules、venv 可重建，但删除前必须确认。
