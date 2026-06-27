# Windows Disk Cleanup Claude Skills v3 GitHub Edition

本仓库包含两个 Claude Code Skill：

- `disk-cleanup-system`：Windows 系统盘安全清理
- `disk-cleanup-non-system`：指定非系统盘清理与整理

## 目录结构

```text
.claude/
└─ skills/
   ├─ disk-cleanup-system/
   │  ├─ SKILL.md
   │  ├─ references/
   │  └─ scripts/
   │     └─ README.md
   └─ disk-cleanup-non-system/
      ├─ SKILL.md
      ├─ references/
      └─ scripts/
         └─ README.md
README.md
```

本版本不包含 `.claude/commands/`，只保留正式 Skill 文件。上传 GitHub 时可以直接上传整个仓库目录。

## v3 重点规则

- 禁止默认全盘递归扫描；
- 默认浅层优先，只看 Top 大项；
- 默认扫描深度 2～3 层；
- 默认输出 Top 20～50 项；
- 大量小文件目录只统计总量，不展开文件级明细；
- 能判断目录类型就早停，不为了完整性强行读完；
- 超时、权限拒绝、Junction 循环风险时跳过并标注；
- 输出为“分类汇总 + 可清理潜力 + 确认清单”；
- 删除、迁移、清空、Docker prune、DISM、关闭休眠等操作必须用户确认。

## 安装到全局 Claude Code

将本仓库里的 `.claude` 文件夹合并到：

```text
C:\Users\你的用户名\.claude\
```

最终结构应类似：

```text
C:\Users\你的用户名\.claude\skills\disk-cleanup-system\SKILL.md
C:\Users\你的用户名\.claude\skills\disk-cleanup-non-system\SKILL.md
```

然后重启 Claude Code，或执行：

```text
/reload-plugins
```

## 测试触发

```text
/disk-cleanup-system 帮我安全清理C盘，不要丢浏览器登录
```

```text
/disk-cleanup-non-system 帮我分析D盘，只能在D盘内操作，不要跨盘
```

## 注意

本 Skill 默认不提供一键清理脚本。`scripts/README.md` 只说明脚本策略：如确需脚本，只允许按实际环境临时生成只读扫描脚本；删除、移动、迁移、清空等操作必须先输出确认清单并得到用户确认。
