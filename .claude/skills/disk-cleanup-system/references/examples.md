# 系统盘清理示例

一个典型流程：

1. Temp + 浏览器缓存释放约 12.4GB；
2. 开发缓存 + Docker prune 释放约 7.3GB；
3. 关闭休眠释放约 15GB；
4. 通过 Junction 将微信、WPS、Edge、腾讯会议、Office 等应用数据迁移到目标盘；
5. 后续用 DISM 清理 WinSxS；
6. 最终输出清理前后容量对比。

注意：

- 浏览器只清 Cache、Code Cache、GPUCache；
- 不清 Cookies、Login Data、Preferences；
- Docker prune、DISM、关闭休眠、Junction 迁移均需用户确认；
- 不认识的目录标注给用户决定。
