# 浏览器缓存规则

目标：清理浏览器缓存，但保留登录状态、扩展配置、网站数据和用户偏好。

## 可以清理

只允许清理以下缓存目录：

- Cache
- Code Cache
- GPUCache
- ShaderCache
- GrShaderCache

## 禁止清理

不得删除：

- Cookies
- Login Data
- Preferences
- Local Storage
- IndexedDB
- Web Data
- Extensions
- History

## 发现方式

从基础目录动态发现 Profile：

```text
%LOCALAPPDATA%\Google\Chrome\User Data\
%LOCALAPPDATA%\Microsoft\Edge\User Data\
```

Profile 可能是 Default、Profile 1、Profile 2 等。可以统计每个 Profile 的 Cache 总量，但不要展开 Cache 内部小文件。
