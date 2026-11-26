# TEMPLATE_GUIDE.md — 文件分工说明

## 一、文件与各角色职责

| 文件 | 谁写 | 何时写 | 其他人 |
|---|---|---|---|
| `input.md` | 人 | 开新任务时 | 只读 |
| `state.json` | 每个角色 | 干完活时改一次 | 开工前先读 |
| `task.md` | coordinator | 接到需求后 | 只读 |
| `research.md` | researcher | 接到任务后 | 只读 |
| `draft.md` | writer | 成稿/返工后 | 只读 |
| `review.md` | reviewer | 每轮审核后 | writer 必读 |
| `output.md` | reviewer | 审核通过时 | 只读 |
| `shturl` | 所有角色 | 每次动作后追加一行 | 只追加不改 |

## 二、state.json 字段

- `current_stage`：wait / running / review / done / failed
- `next_agent`：下一个该干的角色名；不干活写 `none`
- `finished_agents`：已完成角色列表
- `task_status`：pending / running / done / failed
- `retry_count`：已返工轮数
- `max_retry`：返工上限（默认 3）

## 三、统一的开工/收工协议

1. 开工先读 `state.json`，`next_agent` 不等于自己 → **立即停止，不输出任何内容**
2. 读需要的上游文件（task / research / review）
3. 干完写自己的文件
4. 改 `state.json` 的 `next_agent` 指向下一环
5. `shturl` 追加一行：`时间 | 角色 | 动作 | 结果`
6. 只做本职，不越权

## 四、流程分支

```
input.md → coordinator → researcher → writer → reviewer
                                                ├─ 通过 → output.md，task_status=done
                                                └─ 不通过 → writer 返工（最多 3 轮）
```

## 五、安全红线

- **不要**把 API Key、ghp_ 令牌、密码写进任何文件
- 这个仓库是私有仓库，但一旦改成公开，历史记录会一并暴露

## 六、开新任务前重置

将 `state.json` 恢复为初始值（next_agent=coordinator，retry_count=0，finished_agents=[]，task_status=pending），清空 task/research/draft/review/output 的旧内容。
