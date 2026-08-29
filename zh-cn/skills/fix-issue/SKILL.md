---
name: fix-issue
description: >
  执行 Issue 修复——读取 Open/Reopen/Partial 状态的 issue，进行根因分析，实施代码修复，
  并更新 issue 文档为 Fixed 状态、追加修复记录。当用户说"修一下这个 issue"、"把 issue 修了"、
  "修复 issue xxx"、"这个 issue 能修吗"、"fix issue"、"解决这个问题"、
  "把这个 bug 修了"、"改一下这个问题"、"issue 对应的 PR 改完了"时立即触发。
  也适用于用户引用了一个 issue 文件路径并要求执行修复、或 fix-issue 作为下游被
  issue-verify 调用进行修复的场景。与 issue-create 不同，本 skill 专门做根因分析和代码修复，
  不创建 issue。与 issue-verify 不同，本 skill 不处理状态变更验证。
  如果 docs/spec/issues/ 中有 Open 状态的 issue 积压，可以主动询问是否要处理。
---

# fix-issue: Issue 修复执行

你是一个**修复工程师**和**编排者**——将修复拆分为三个专业阶段（诊断、方案、实施），
每个阶段遵循对应专业 skill 的方法论，而不是凭直觉随意行事。

**生命周期中的定位**：

```
issue-create ──→ fix-issue ──→ issue-verify ──→ issue-archive
 (记录症状)      (编排修复)     (验证+状态变更)    (归档沉淀)
```

---

## 🚫 红线：严禁越界操作

| ❌ 禁止 | ✅ 允许 |
|---------|--------|
| 创建新的 issue 文档 | 读取已有的 issue 文档 |
| 修改 issue 状态为 Verified/Closed | 修改 issue 状态为 Fixed，并追加修复记录 |
| 归档 issue 或移动文件 | 保持 issue 在 `docs/spec/issues/` 中不变 |
| 未确认根因果断动手修复 | 用户确认根因和方案后再实施 |
| 修改后不运行构建/lint | 修改后必须运行 cargo build / tsc --noEmit 等验证 |
| 修复不相关的旁边代码或"顺手优化" | 仅修 issue 涉及的最小代码范围 |

### 与 issue-create 的关键边界

```
┌─────────────────────────────────────────────────────┐
│  issue-create (症状记录)          fix-issue (诊断修复)│
│                                                     │
│  ❌ 不追踪数据流找 bug 源头      ✅ 必须追踪数据流    │
│  ❌ 不验证条件是否成立           ✅ 必须验证根因假设  │
│  ❌ 不读超过 5 个文件            ✅ 可读 10+ 个文件   │
│  ❌ 不输出根因分析               ✅ 必须输出根因分析  │
│  ❌ 不写修复方案                 ✅ 必须写修复方案    │
│  ❌ 不修改代码                   ✅ 必须修改代码      │
└─────────────────────────────────────────────────────┘
```

---

## 核心原则

1. **理解症状再动手**：先完整读懂 issue 文档中的症状描述、复现条件、涉及文件，不要跳读
2. **不加诊断不准修复**（Iron Law of Debugging）：没有完成根因分析之前，绝不动手修代码
3. **过程纪律高于速度**：按 systematic-debugging → writing-plans → 实施 的顺序推进，不要跳过步骤
4. **最小修复**：只改必须改的代码，不改无关行（即使附近有不规范代码）
5. **保留痕迹**：每次修复都在 issue 文档中记录——用户原意、根因、改动内容、涉及文件
6. **修复后验证**：确保构建/lint 通过
7. **所有面向用户的文本使用中文**

---

## 工作流程（三阶段编排）

```
阶段0-1 ──→ 阶段2（systematic-debugging）──→ 阶段3（writing-plans）──→ 阶段4-5（实施+验证）──→ 阶段6
 定位+理解     数据流追踪+根因分析           结构化修复方案               代码修改+build验证       更新 Issue 文档
```

---

### 阶段零：定位 Issue

1. 用户通过 `$ARGUMENTS` 提供 issue 引用——可能是文件名、关键词、标题片段或路径
2. **明确路径** → 直接 Read
3. **模糊引用** → 用 Grep 在 `docs/spec/issues/` 中搜索匹配
4. **找不到** → 也在 `docs/spec/archive-issues/` 搜索一次（可能已归档）
5. **仍找不到** → 告知用户未找到，建议先 `/issue-create` 创建

### 阶段一：理解 Issue

1. **完整阅读 issue 文档**，重点关注：
   - **问题描述**和**症状详情**——用户看到了什么现象
   - **复现条件**——在什么场景下出现
   - **涉及文件**——为用户创建 issue 时标注的涉及文件
   - **状态**——必须是 `Open` / `Reopen` / `Partial` 才执行修复
   - **修复记录**——检查是否有过历史修复尝试
2. **确认用户意图**：如果需要明确修复目标或决策，用 AskUserQuestion 提问
3. **如果状态已是 Fixed/Verified/Closed** → 提示用户该 issue 已处理过

---

### 阶段二：根因诊断（遵循 systematic-debugging 方法论）

**这是 fix-issue 与 issue-create 最大的区别。** issue-create 被严禁做根因分析，而你必须做。

#### systematic-debugging 的四个阶段

**Phase 1: Root Cause Investigation（根因调查）**

从 issue 描述的入口点出发，追踪数据流到问题点。关键方法：

1. **从入口到出口追踪**：找到调用路径的起点，逐层往下读
2. **在关键节点检查**：参数传递是否丢失、状态变更是否符合预期、条件分支是否覆盖全、错误路径是否被正确处理
3. **不要随机读代码**——如果发现自己不知道在找什么，回到入口点重新开始
4. **多文件追踪**：这是允许的（不同于 issue-create 的 ≤5 文件限制）

**Phase 2: Pattern Analysis（模式分析）**

1. 找代码库中相似但正确的实现做对比
2. 列出差异：正确版本和错误版本有什么不同
3. 不要假设"那个差异应该不影响"

**Phase 3: Hypothesis and Testing（假设验证）**

1. 形成单一假设："我认为根因是 X，因为 Y"——写下来
2. 验证假设：读代码确认逻辑推理成立
3. 如果推测错误，形成新假设，不要叠加多个猜测

**Phase 4: Implementation（实施阶段）→ 跳过，留到阶段四**

#### 自我诊断：正在做正确的事吗？

如果你发现自己想：
- "先试着改一下看能不能好" → **停下来**，返回 Phase 1
- "这个看起来可能是 X 的问题" → 形成假设并验证，而不是修改
- "一次改好几处吧" → 只改一处，验证，再下一处

#### 阶段性产出：根因陈述

找到根因后，向用户呈现诊断结果：

```markdown
## 根因分析

[问题出现在哪个模块、哪个文件、哪条代码路径]

### 根本原因

[一句话概括——什么条件/逻辑/状态导致了用户看到的现象]

### 代码定位

- `path/to/file.rs:NNN` —— [具体问题]（如"条件判断遗漏了 admin 角色"）
- `path/to/file.rs:MMM` —— [上游调用]（如"调用方未检查返回值"）
```

**用户确认后进入阶段三**。如果用户提出疑问或补充信息，回到追踪流程。

---

### 阶段三：方案设计（遵循 writing-plans 方法论）

#### writing-plans 的核心要求

1. **文件结构**：列出所有需要创建/修改的文件及职责
2. **任务粒度**：每个 Task 是独立可测试的单元，2-5 分钟
3. **完整代码**：每个步骤包含实际代码，不用"TBD"、"TODO"等占位符
4. **构建验证**：每条改动附带验证命令和预期输出

#### 修复方案格式

保存到 `docs/superpowers/plans/YYYY-MM-DD-fix-<slug>.md`：

```markdown
# [Issue 标题] 修复方案

> 源于 issue: `docs/spec/issues/YYYY-MM-DD-<slug>.md`
> **根因**: [阶段二的根因]

**Goal:** [一句话说明修复要达到什么效果]

**Architecture:** [修复方案的核心思路]

## 改动清单

| 文件 | 操作 | 说明 |
|------|------|------|

## 实施步骤

### Task 1: [具体任务]

**Files:**
- Modify: `path/to/file.rs:NNN-NNN` —— [改动说明]

- [ ] **Step 1: 修改代码**
  ```rust
  // 实际代码
  ```
- [ ] **Step 2: 构建验证**
  ```bash
  cd gateway && cargo build 2>&1 | tail -20
  ```
  预期：编译通过，无 warning
```

**完整代码要求**：每个 Step 必须包含可直接粘贴的完整代码，不要写"类似上面的逻辑"、"参考 Task 1"。

#### 多方案可选时使用对比表

如果存在多种修复方式且各有取舍，在方案中列出对比表，用 AskUserQuestion 让用户选择：

```markdown
| 方案 | 描述 | 优点 | 缺点 |
|------|------|------|------|
| A) 同步重试 | 原地重试，阻塞响应 | 数据必达，实现简单 | 增加 ~700ms 响应延迟 |
| B) 异步后台 | tokio::spawn 后台重试 | 不增加延迟 | Gateway crash 时丢失 |
```

#### 确认方案

向用户呈现方案概要：

```markdown
✅ 修复方案已创建 → docs/superpowers/plans/YYYY-MM-DD-fix-<slug>.md

改动范围：N 个文件 | N 个 Task
验证方式：cargo build

请确认此方案是否可行？
```

用户确认后进入阶段四。

---

### 阶段四～五：实施修复 + 构建验证

按修复方案的 Task 顺序逐任务实施。**不要在多个 Task 间同时修改文件。**

#### 每个 Task 的执行步骤

1. **读取 Task 要求**：明确要改什么文件、改什么逻辑
2. **修改代码**：使用 Edit 精确修改，只改指定范围
3. **构建验证**：运行对应项目的构建命令
4. **如果构建失败**：检查错误，修正，重新验证
5. **提交到版本控制**：`git add` + `git commit -m "fix: [具体问题]"`（仅当有 git commit 需求时）

#### 验证命令速查

| 项目 | 命令 |
|------|------|
| Gateway | `cd gateway && cargo build 2>&1 \| tail -20` |
| Billing Service | `cd billing-service && cargo build 2>&1 \| tail -20` |
| Backend | `cd backend && source venv/bin/activate && python3 -c "from app.main import app; print('OK')"` |
| Frontend | `cd frontend && npx tsc --noEmit 2>&1 \| tail -20` |

**所有 Task 完成后**，在终端运行最终构建验证确认整体通过。

---

### 阶段六：更新 Issue 文档 + 输出摘要

1. **更新状态头部**：`**状态**：Open/Reopen/Partial` → `**状态**：Fixed`

2. **追加修复记录**（在「修复记录」段落追加新条目）：

```markdown
### 修复 #N（YYYY-MM-DD）

- **操作人**：agent
- **用户原意**：[从 issue 的「问题描述」提取——用户希望达到什么效果]
- **根因分析**：[阶段二的根因陈述]
- **修复内容**：[做了什么改动——修改了哪些文件、改了什么逻辑]
- **涉及文件**：
  - `path/to/file.rs` —— [具体改动说明]
- **涉及 commit**：[如有，commit hash]
- **构建验证**：[通过 / 失败及处理]
- **验证状态**：待验证
```

编号规则：`N = max(已有条目编号) + 1`。

3. **追加状态变更记录**：

```markdown
| YYYY-MM-DD | [旧状态] | Fixed | agent | 修复完成：[简述修改概要] |
```

4. **输出摘要**：

```markdown
✅ Issue 修复完成 → docs/spec/issues/YYYY-MM-DD-<slug>.md

状态：[旧状态] → Fixed
根因：[一句话根因]
改动：N 个文件
  - path/to/file.rs —— [改动说明]
验证：cargo build 通过

📋 下一步：请用 /issue-verify 验证修复效果
```

---

## 修复示例

### 场景：Bug 修复（API Path 不匹配）

用户输入：`修一下 docs/spec/issues/2026-06-27-gateway-post-task-id-silent-failure.md`

**执行流程**：

**阶段 0-1**：Read 该 issue —— Gateway 创建视频任务后调 `post_task_id`，失败时静默丢弃。

**阶段 2**（systematic-debugging）：
1. 从 `handle()` 入口追踪：`post_task_id` → `backend_client.rs` 中 HTTP PATCH 调用
2. 分析：当前代码用 `if let Err(e) = ... { tracing::warn!(...) }`，只记日志不重试
3. 假设验证：Backend 重启/闪断 → post_task_id 失败 → real_task_id 永久 NULL → 对账释放不扣费
4. 确认根因
5. 向用户呈现根因陈述

**阶段 3**（writing-plans）：
1. 写入修复方案：`docs/superpowers/plans/2026-07-02-fix-post-task-id-retry.md`
2. 含改动清单、完整代码、构建验证命令
3. 向用户确认方案

**阶段 4-5**：
1. 修改 `video.rs`：在 post_task_id 调用处加指数退避重试
2. `cargo build` 验证

**阶段 6**：
1. 更新 issue 状态为 Fixed
2. 追加修复记录
3. 输出摘要

---

## 复杂场景处理

### 修复方案有多个选项

当存在多种修复方式时，用 AskUserQuestion 让用户决策，并提供方案对比表：

```
这个修复有两个方案：

| 方案 | 优点 | 缺点 |
|------|------|------|
| A) 同步重试 | 数据必达、实现简单 | 增加 ~700ms 响应延迟 |
| B) 异步后台重试 | 不增加延迟 | Gateway crash 时重试丢失 |

对于计费链路关键步骤，优先选 A（同步），因为数据完整比响应延迟更重要。
你倾向哪个？
```

### 重试策略选择

在诊断阶段区分目标调用的**业务关键性**，选择不同的重试策略：

| 场景 | 推荐策略 | 原因 |
|------|---------|------|
| **计费链路关键写入**（post_task_id 写 DB） | **同步重试**（首次 + N 次退避重试，失败后 `error!`） | DB 源数据一旦丢失不可恢复，响应延迟可接受 |
| **缓存/非关键写入**（post_task_model 写 Redis） | **异步 backlog**（`tokio::spawn` + best-effort） | 丢失可重建，不阻塞响应 |
| **普通业务调用** | 简单同步重试（N 次退避） | 平衡延迟与可靠性 |

**推荐实现模式**（计费关键路径）：

```rust
// 同步重试 + 指数退避（不改为 tokio::spawn）
let mut last_err = String::new();
for attempt in 0..3 {
    match some_critical_call(...).await {
        Ok(v) => break Ok(v),
        Err(e) => {
            last_err = e;
            if attempt < 2 {
                tokio::time::sleep(std::time::Duration::from_millis(
                    100 * 2u64.pow(attempt),
                ))
                .await;
            }
        }
    }
}
// 全部失败后升级为 error!
if let Err(e) = result {
    tracing::error!(... "critical call failed after 3 retries: {e}");
}
```

### 修复引入 breaking change

- 在修复记录中标注「Breaking Change」
- 更新 CLAUDE.md 或相关文档

### 修复中发现相关问题

- 在 issue 的「症状详情」追加子段记录发现
- **不在此次修复中处理**——建议创建新 issue

---

## 自检清单

- [ ] 根因是否经过 systematic-debugging 流程验证（Phase 1→2→3）？
- [ ] 是否确实找到了根本原因，而不是停留在现象层面？
- [ ] 修复方案是否用 writing-plans 格式包含完整代码？
- [ ] 方案是否向用户确认过？
- [ ] 改动是否只限于 issue 指定的最小范围？
- [ ] `cargo build` / 对应验证是否通过？
- [ ] issue 状态是否已改为 Fixed？
- [ ] 修复记录是否包含用户原意和根因分析？
- [ ] 是否提醒用户用 `/issue-verify` 验证？

---

## 与其他 Issue Skills 的关系

| Skill | 时机 | 输入 | 输出 |
|-------|------|------|------|
| `issue-create` | 用户描述问题 | 现象描述 | `Open` issue 文档 |
| **`fix-issue`** | **用户要求修复** | **`Open`/`Reopen`/`Partial` issue** | **`Fixed` issue + 代码改动** |
| `issue-verify` | 用户反馈修复效果 | `Fixed` issue | 状态变更为 `Verified`/`Reopen`/`Closed` |
| `issue-archive` | 用户要求归档 | `Verified`/`Closed` issue | 归档文件 + 领域认知沉淀 |

**典型端到端调用链**：

```
1. /issue-create
2. /fix-issue docs/spec/issues/xxx.md
3. /issue-verify
4. /issue-archive
5. 更新 CLAUDE.md（从归档中提取经验）
```
