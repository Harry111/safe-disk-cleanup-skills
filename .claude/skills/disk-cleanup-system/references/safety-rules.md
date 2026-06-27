# 系统盘安全规则

## 绝对禁止直接删除

- `%SystemRoot%\System32`
- `%SystemRoot%\SysWOW64`
- `%SystemRoot%\Boot`
- `%SystemRoot%\Recovery`
- `pagefile.sys`
- `swapfile.sys`
- `hiberfil.sys`

`hiberfil.sys` 只能在用户确认后通过 `powercfg -h off` 关闭休眠，由系统处理，不得手动删除。

## 用户数据保护

默认不得删除 Desktop、Documents、Pictures、Videos、Downloads 中无法判断用途的内容。项目源码、Git 仓库、数据库、虚拟机、游戏目录、论文、报告、照片、视频素材、聊天记录、备份目录默认保护。

## 未知目录

无法判断用途时标注为“未知，需用户确认”。不能为了弄清楚就无限深入扫描，也不能自动删除。
