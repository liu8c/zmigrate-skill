# zmigrate-skill

ZMigrate 本地知识库助手，用于快速查询 zmigrate 安装部署、注册接入、同步切换、日志排障和操作经验文档。

## 安装

这是一个 **Claude Code marketplace 仓库**，团队成员执行：

```text
/plugin marketplace add https://github.com/liu8c/zmigrate-skill
/plugin install zmigrate-skill
/reload-plugins
```

## 内容

- `.claude-plugin/marketplace.json`：marketplace 元数据
- `plugins/zmigrate-skill/.claude-plugin/plugin.json`：单插件元数据
- `plugins/zmigrate-skill/skills/zmigrate/SKILL.md`：Claude Code skill 定义
- `plugins/zmigrate-skill/skills/zmigrate/references/`：ZMigrate 知识库正文与索引文档

## 使用场景

- 查询同步任务异常
- 查询切换报错
- 查询安装/注册问题
- 查询日志排障和现场经验

## 使用方式

```text
/zmigrate 同步任务不显示源端主机的磁盘
/zmigrate 服务器与管理控制台之间的回报连接失败
/zmigrate 立即切换报 Server service is not ready
```

## 目录结构

```text
zmigrate-skill/
├── README.md
├── .claude-plugin/
│   └── marketplace.json
└── plugins/
    └── zmigrate-skill/
        ├── .claude-plugin/
        │   └── plugin.json
        └── skills/
            └── zmigrate/
                ├── SKILL.md
                └── references/
```
