# Pre-Development Checklist 编写规范

> 适用范围：所有使用本模板的项目。
> 目的：为每个 package/module 的 spec 目录提供统一的"开发前读什么、开发后验什么"闭环结构。

---

## 一、设计意图

每个 package/module 的 `.trellis/spec/<package>/index.md` 应包含两个清单：

1. **Pre-Development Checklist** — 开发者开始写代码前必须读的规范列表
2. **Quality Check** — 代码完成后的自检清单

两者配合形成"输入把关 → 输出验证"的闭环。AI 助手在收到编码任务时自动加载 index.md，确保每次修改都经过规范约束。

---

## 二、模板结构

```markdown
# <Package> 开发规范

> 适用范围：`<path>` 下所有代码文件。

---

## Pre-Development Checklist

开始任何开发任务前，逐项确认：

- [ ] 读取 [目录结构规范](./directory-structure.md) — 确认新文件的放置位置
- [ ] 读取 [<核心规范1>](./<file>.md) — <一句话说明关注什么>
- [ ] 读取 [<核心规范2>](./<file>.md) — <一句话说明关注什么>
- [ ] 读取 [代码质量规范](./quality-guidelines.md) — 命名、分层、审查约束

---

## Quality Check

代码完成后，逐项验证：

- [ ] <该 package 最常犯的错误1>
- [ ] <该 package 最常犯的错误2>
- [ ] <统一的错误处理模式已遵循>
- [ ] <权限/安全约束已满足>
- [ ] <命名/放置规范已满足>
```

---

## 三、编写原则

| 原则 | 说明 |
|------|------|
| 条目必须可执行 | 每条是一个可勾选的动作，不是模糊描述 |
| Pre-Dev 只链接 spec 文件 | 不在 checklist 内重复规范内容，只提供链接 + 一句话聚焦点 |
| Quality Check 是验证条件 | 每条是 boolean 判断："是否满足 X" |
| 条目来源于真实 bug | 只收录实际出过错的检查点，不加教科书条目 |
| 保持精简 | Pre-Dev 3-6 条，Quality Check 5-10 条；太多则无人看 |

---

## 四、与 AI 工具集成

当 `.trellis/spec/<package>/index.md` 存在时，trellis-before-dev skill 会在编码任务开始前自动注入其内容：

- 规范在每次 AI 编码时自动生效，无需人工提醒
- Quality Check 可作为 trellis-check 的验证基准
- 新成员无需口头交代规范，AI 会代为执行
