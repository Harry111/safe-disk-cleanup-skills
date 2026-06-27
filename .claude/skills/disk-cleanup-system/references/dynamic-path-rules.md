# 动态路径规则

不要写死用户名和盘符。优先使用环境变量和用户确认的目标盘。

常用变量：

```powershell
$env:SystemDrive
$env:SystemRoot
$env:USERPROFILE
$env:LOCALAPPDATA
$env:APPDATA
$env:TEMP
$env:TMP
```

错误示例：

```text
C:\Users\holly\AppData\Local\Temp
D:\Migrated\Edge
```

正确示例：

```text
$env:TEMP
$env:LOCALAPPDATA\Microsoft\Edge\User Data\<Profile>\Cache
<TARGET_DRIVE>:\Migrated\<AppName>\<OriginalFolderName>
```

如用户明确指定 C: 或 D:，可使用用户指定值，但仍不得套用本机个人路径。
