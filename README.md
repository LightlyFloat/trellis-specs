# trellis-specs

个人/团队的 Trellis spec 模板注册表（registry）。用于在新项目初始化时统一注入规范，避免每个项目重复编写。

## 结构

```
trellis-specs/
└── specs/                          ← registry 根（index.json 所在）
    ├── index.json                  ← 模板索引
    └── default-0621/               ← 模板：默认规范模板 (0621)
        └── guides/
            ├── index.md
            ├── document-writing-style.md
            └── project-doc-organization.md
```

## 使用方法

在任意项目中初始化并拉取指定模板：

```bash
trellis init -u <your-name> --registry gh:LightlyFloat/trellis-specs/specs --template default-0621
```

已有项目补充规范（只新增缺失文件）：

```bash
trellis init --registry gh:LightlyFloat/trellis-specs/specs --template default-0621 --append
```

覆盖已存在的 spec 目录：

```bash
trellis init --registry gh:LightlyFloat/trellis-specs/specs --template default-0621 --overwrite
```

模板内容会被下载至目标项目的 `.trellis/spec/` 目录。

## 现有模板

| id | 名称 | 说明 |
|----|------|------|
| `default-0621` | 默认规范模板 (0621) | 面向交付的中文文档文风标准 + 项目文档组织约定（document/ 目录与文档索引）。 |

## 新增模板

1. 在 `specs/` 下新建模板目录，例如 `specs/<template-id>/`，按 `.trellis/spec/` 的目录结构放置规范文件。
2. 在 `specs/index.json` 的 `templates` 数组中追加一条记录，`path` 指向该模板目录（仓库根相对路径），`type` 固定为 `spec`。
3. 提交并推送。

## index.json 字段说明

| 字段 | 必填 | 说明 |
|------|------|------|
| `id` | 是 | 模板标识，对应 `--template <id>` |
| `type` | 是 | 固定为 `spec` |
| `name` | 是 | 模板显示名称 |
| `description` | 否 | 模板说明 |
| `path` | 是 | 模板目录的仓库根相对路径 |
| `tags` | 否 | 标签数组 |
