# 子代理使用边界规范

> 适用范围：所有支持派发子代理的 AI 开发平台，主要目标为 Claude Code 与 Codex。
> 目的：把子代理限定在确有必要的场合，实施工作由主会话直接完成。

---

## 一、规则

默认不派发实施类子代理，实施工作由主会话直接完成。

唯一例外是 review / critic 类子代理，在 Trellis 中即 `trellis-check`。

`trellis-research` 不在例外名单内，调研回到主会话内联进行。

---

## 二、保留 review 的理由

评审需要一份不带实施者预设的独立上下文。实施者在实现过程中已经形成一套判断，
若由同一份上下文评审自己的产出，等同于实施者自行批准实施结果，预设本身的错误无法被发现。
Trellis 的 `[workflow-state:in_progress]` 块中之所以存在 self-exemption 规则，
原因正在于此。

---

## 三、排除 research 的已知代价

调研内联进行会使规划期主会话的上下文增长更快。这是本规则的已知代价，不是疏漏。

该代价被规划会话的一次性特征抵消。按 [规划与执行分离规范](./plan-execute-handoff.md)，
规划会话在交接完成后即被清空丢弃，其上下文不会带入执行期，因此规划期的上下文增长
只影响单次会话，不会累积。

此处明确记录这一代价，目的是避免后续维护者将其误判为规则遗漏，
从而擅自把 `trellis-research` 重新加入例外名单。

---

## 四、与每轮注入文本的冲突消解

Trellis 项目中的 `.trellis/workflow.md` 有一个 `[workflow-state:in_progress]` 块，
每轮向会话注入「Main-session default: dispatch implement/check sub-agents」，
与第一节的规则直接冲突。

每轮注入的文本在上下文中位置靠后，通常权重更高，因此仅在全局配置中写一般性表述
不足以压制它。全局配置必须显式点名它所覆盖的文本，以声明方式指明优先级：

> 若每轮注入的 `workflow-state` 块要求派发 `trellis-implement`，以本规则为准：
> 实施内联完成，只有 `trellis-check` 可派发为子代理。

配置落点为 `~/.claude/CLAUDE.md` 与 `~/.codex/AGENTS.md`，分别由 Claude Code 与
Codex 读取，两处内容须保持一致。全局配置只写这条覆盖声明，完整依据指向本文件，
以免全局配置膨胀。

---

## 五、可选硬化

若要在单个项目内彻底消除第四节所述的冲突，可直接编辑该项目
`.trellis/workflow.md` 中的 `[workflow-state:in_progress]` 块，
删除或改写要求派发实施子代理的那句。`workflow.md` 自身说明支持这种编辑方式：
直接修改块体，重启会话后即生效，无需改动脚本。

此步骤为可选，且有明确代价。`.trellis/workflow.md` 受 `trellis update` 的模板哈希
跟踪，哈希登记在 `.trellis/.template-hashes.json`。修改该文件后，将来执行
`trellis update` 会对它产生更新冲突，需要手工处置。采用此硬化前须先知情这一代价。

---

## 六、验证

启动一个新会话，进入一个状态已为 `in_progress` 且实施尚未开始的任务。

预期行为：主会话直接开始编辑代码，不出现 `trellis-implement` 的派发动作。

若仍然出现派发，检查全局配置中的覆盖声明是否显式点名了 `workflow-state` 块。
未点名的一般性表述不足以压制每轮注入的文本。
