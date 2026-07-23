# 搜索工具初始化规范

> 适用范围：所有使用高质量 MCP 搜索工具替代 Claude Code 内置搜索的项目。
> 目的：确保项目创建后立即使用 MCP 搜索，避免模型降级使用内置劣质搜索。

---

## 一、问题

Claude Code 内置 WebSearch / WebFetch 工具质量较低（结果浅、无法深度提取）。当用户已配置高质量搜索 MCP（如 grok-search）时，需要在项目级禁用内置工具，否则模型会不自觉使用内置搜索。

这是一个"初始化遗忘"问题：每次新建项目时容易忘记禁用，等到需要搜索时发现模型已经使用了劣质搜索，难以补救。

---

## 二、解决方案

在项目 `.claude/settings.json` 中添加 `permissions.deny` 规则，项目级永久生效。

```json
{
  "permissions": {
    "deny": ["WebFetch", "WebSearch"]
  }
}
```

---

## 三、实施方式

### 方式 A：模板自动包含（推荐）

在 trellis init 模板的 `.claude/settings.json` 骨架中默认包含 deny 规则。新项目创建即生效，零操作。

### 方式 B：MCP 工具调用

若项目已存在 settings.json 且未包含 deny 规则，可通过 MCP 工具补充：

```
调用 grok-search toggle_builtin_tools action="on"
```

此命令会自动向 `.claude/settings.json` 追加 `permissions.deny` 字段。

### 方式 C：CLAUDE.md 半自动提醒

在 CLAUDE.md 中加入初始化指令，模型首次进入时执行：

```markdown
## 会话初始化

首次进入项目时，执行以下初始化（仅需一次，settings.json 持久生效）：

- 调用 `grok-search toggle_builtin_tools action="on"` 禁用内置搜索
```

---

## 四、验证

```
调用 grok-search toggle_builtin_tools action="status"
```

预期输出：`blocked: true, deny_list: ["WebFetch", "WebSearch"]`

禁用后，模型在需要搜索时只能使用已配置的 MCP 搜索工具。

---

## 五、注意事项

- 此设置是项目级的，不影响其他项目
- 若临时需要使用内置搜索，可执行 `toggle_builtin_tools action="off"` 恢复
- 建议将 `.claude/settings.json` 纳入版本控制，团队成员共享同一配置
