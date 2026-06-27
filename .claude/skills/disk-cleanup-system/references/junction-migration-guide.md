# Junction 迁移指南

适用于某个应用数据目录长期占用系统盘空间，但不能直接删除的情况。

## 必须确认

迁移前必须确认：

1. 用户指定目标盘符；
2. 目标盘空间足够；
3. 目标盘长期可用，不是临时 U 盘；
4. 相关应用已关闭；
5. 用户接受数据实际存放在目标盘；
6. 迁移失败时保留原数据。

## 路径模板

```text
源路径：<SOURCE_PATH>
目标路径：<TARGET_DRIVE>:\Migrated\<APP_NAME>\<ORIGINAL_FOLDER_NAME>
备份路径：<SOURCE_PATH>.backup-before-junction
```

## 标准流程

1. 关闭相关应用；
2. 检查源目录；
3. 检查目标盘空间；
4. robocopy 复制；
5. 校验复制结果；
6. 原目录重命名为备份；
7. mklink /J 创建 Junction；
8. 测试应用；
9. 用户确认正常后，再删除备份。
