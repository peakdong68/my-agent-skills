---
name: laravel-boost
description: >
  在设置或配置 Laravel Boost 以进行 AI 辅助开发时使用 — 包括包安装、MCP 服务器配置、guideline 自定义、skill 编写、文档 API 集成。
  触发条件：安装 Laravel Boost、为 IDE 配置 MCP、创建自定义 AI guidelines、编写项目特定 skills、验证 MCP tool 连接、依赖变更后更新 Boost、为自定义 agents 扩展 Boost。
---

# Laravel Boost

## 概述

Laravel Boost 是一个官方 Laravel 包，通过提供可组合的 guidelines、按需 agent skills、一个 MCP（Model Context Protocol）服务器以及一个包含 17,000+ 条 Laravel 特定知识片段的语义搜索文档 API，来加速 AI 辅助开发。它连接了 AI 编码工具与 Laravel 生态系统，确保 AI agents 生成高质量、符合约定的 Laravel 代码。

在项目中设置、配置或扩展 Laravel Boost，或将 AI agents 与 Laravel 应用集成时应用此 skill。

## 多阶段流程

### 阶段 1：评估

1. 确认 Laravel 版本兼容性（10.x、11.x、12.x）
2. 确定所使用的 AI IDE 或 agent（Cursor、Claude Code、Codex、Gemini CLI、GitHub Copilot、Junie）
3. 检查是否已存在 MCP 配置文件（`.mcp.json`）
4. 检查 `.ai/` 中现有的 guideline 和 skill 自定义内容

> **停止 — 在确认 Laravel 版本兼容性和目标 IDE 之前，不要安装 Boost。**

### 阶段 2：安装与配置

1. 安装包并运行安装器
2. 为目标 IDE 配置 MCP 服务器
3. 通过 Composer hooks 设置自动更新
4. 验证 MCP tools 可从 AI agent 访问

> **停止 — 在至少通过一次 tool 调用验证 MCP 服务器连通性之前，不要继续。**

### 阶段 3：自定义

1. 在 `.ai/guidelines/` 中添加项目特定的 guidelines
2. 在 `.ai/skills/` 中创建领域特定的 skills
3. 当项目约定与默认值不同时，覆盖内置的 guidelines 或 skills
4. 如果扩展到不支持的 IDE，注册自定义 agents

> **停止 — 除非项目约定确实与 Laravel 默认值不同，否则不要覆盖内置 guidelines。**

### 阶段 4：验证

1. 确认 MCP 服务器响应 tool 调用（Application Info、Database Schema、Search Docs）
2. 验证 guidelines 已加载到 AI agent 的 context window 中
3. 测试领域相关任务的 skill 激活
4. 检查依赖变更后 `boost:update` 是否保持资源最新

## IDE 设置决策表

| IDE / Agent | 设置方式 | 配置文件 |
|---|---|---|
| **Claude Code** | CLI 命令 | `.mcp.json`（自动生成）|
| **Codex** | CLI 命令 | `.mcp.json` |
| **Gemini CLI** | CLI 命令 | `.mcp.json` |
| **Cursor** | Command Palette GUI | `.cursor/mcp.json` |
| **GitHub Copilot** | Command Palette GUI | `.mcp.json` |
| **Junie** | Settings GUI | `.mcp.json` |
| **自定义 / 不支持** | 自定义 agent 类 | 手动 MCP 配置 |

## 安装

```bash
# 作为开发依赖安装
composer require laravel/boost --dev

# 运行安装器 — 生成 .mcp.json、guideline 文件和 boost.json
php artisan boost:install
```

安装器生成：
- `.mcp.json` — 用于 IDE 集成的 MCP 服务器配置
- Guideline 文件（`CLAUDE.md`、`AGENTS.md` 等）—— 针对检测到的包进行定制
- `boost.json` — Boost 配置文件

### IDE 特定的 MCP 设置

| IDE / Agent | 设置命令或操作 |
|---|---|
| **Claude Code** | `claude mcp add -s local -t stdio laravel-boost php artisan boost:mcp` |
| **Codex** | `codex mcp add laravel-boost -- php "artisan" "boost:mcp"` |
| **Gemini CLI** | `gemini mcp add -s project -t stdio laravel-boost php artisan boost:mcp` |
| **Cursor** | Command Palette -> "Open MCP Settings" -> 打开 `laravel-boost` |
| **GitHub Copilot** | Command Palette -> "MCP: List Servers" -> 选择 `laravel-boost` -> "Start server" |
| **Junie** | Shift-Shift -> "MCP Settings" -> 勾选 `laravel-boost` -> Apply |

### 手动 MCP 配置

```json
{
    "mcpServers": {
        "laravel-boost": {
            "command": "php",
            "args": ["artisan", "boost:mcp"]
        }
    }
}
```

### 保持资源更新

```bash
# 依赖变更后手动更新
php artisan boost:update

# 自动更新 — 添加到 composer.json 的 scripts 部分
{
    "scripts": {
        "post-update-cmd": [
            "@php artisan boost:update --ansi"
        ]
    }
}
```

## MCP Server Tools

Laravel Boost 通过其 MCP 服务器暴露以下 tools，使 AI agents 能够直接访问应用上下文：

| Tool | 用途 | 典型使用场景 |
|---|---|---|
| **Application Info** | 读取 PHP 和 Laravel 版本、数据库引擎、生态系统包、Eloquent models | 会话开始时进行上下文发现 |
| **Database Schema** | 读取完整数据库 schema | 迁移规划、模型生成 |
| **Database Query** | 对数据库执行查询 | 数据检查、调试 |
| **Database Connections** | 检查可用的数据库连接 | 多数据库配置 |
| **Search Docs** | 对 Laravel 文档 API 进行语义搜索 | 查找最佳实践、API 参考 |
| **Last Error** | 读取最近的应用日志错误 | 调试工作流 |
| **Read Log Entries** | 读取最近的 N 条日志条目 | 监控、调试 |
| **Browser Logs** | 读取浏览器中的日志和错误 | 前端调试 |
| **Get Absolute URL** | 将相对路径 URI 转换为绝对 URL | 链接生成 |

## AI Guidelines

Guidelines 是预加载到 AI agent 上下文中的可组合指令文件，提供广泛的约定和最佳实践。

### 可用的内置 Guidelines

| Package | 支持的版本 |
|---|---|
| Laravel Framework | Core, 10.x, 11.x, 12.x |
| Livewire | Core, 2.x, 3.x, 4.x |
| Flux UI | Core, Free, Pro |
| Inertia | React, Vue, Svelte (1.x-3.x) |
| Tailwind CSS | Core, 3.x, 4.x |
| Pest | Core, 3.x, 4.x |
| PHPUnit, Pint, Sail, Pennant, Volt, Wayfinder, Folio, Herd, MCP | Core |

### 自定义 Guidelines

在 `.ai/guidelines/` 中创建 `.blade.php` 或 `.md` 文件：

```
.ai/guidelines/team-conventions.md
.ai/guidelines/billing/stripe-patterns.blade.php
```

通过匹配路径来覆盖内置 guideline：

```
.ai/guidelines/inertia-react/2/forms.blade.php
```

### 第三方包 Guidelines

包作者可以在以下位置提供 guidelines：

```
resources/boost/guidelines/core.blade.php
```

## Agent Skills

Skills 是按需激活的知识模块，仅在相关时加载，以减少上下文窗口膨胀。

### 可用的内置 Skills

| Skill | 领域 |
|---|---|
| `livewire-development` | Livewire 组件和响应式 |
| `inertia-react-development` | Inertia.js with React |
| `inertia-vue-development` | Inertia.js with Vue |
| `inertia-svelte-development` | Inertia.js with Svelte |
| `pest-testing` | Pest 测试模式 |
| `fluxui-development` | Flux UI 组件 |
| `folio-routing` | Folio 基于页面的路由 |
| `tailwindcss-development` | Tailwind CSS 工具类 |
| `volt-development` | Volt 单文件 Livewire 组件 |
| `pennant-development` | Pennant 特性开关 |
| `wayfinder-development` | Wayfinder 类型安全路由 |
| `mcp-development` | MCP 服务器/tool 开发 |

### 自定义 Skills

创建 `.ai/skills/{skill-name}/SKILL.md`：

```markdown
---
name: invoice-management
description: 构建和处理发票功能，包括 PDF 生成和支付跟踪。
---

# Invoice Management

## 何时使用此 skill
在处理发票模块时使用...
```

### Guidelines vs Skills 决策表

| 问题 | Guidelines | Skills |
|---|---|---|
| 何时加载？ | 始终 — 预先加载到上下文中 | 按需 — 当任务匹配时加载 |
| 范围有多广？ | 基础性约定 | 聚焦的实现模式 |
| 对上下文窗口的影响？ | 恒定（始终存在） | 最小（仅在需要时加载）|
| 最适合？ | 编码标准、包版本 | 逐步实现指南 |
| 内容经常变化吗？ | 很少（稳定的约定） | 频繁（不断演进的模式）|
| 团队通用性？ | 高（每个人都遵循） | 各异（领域特定）|

## 文档 API

Boost 提供对 17,000+ 份文档片段的语义搜索，涵盖：

| Package | 版本 |
|---|---|
| Laravel Framework | 10.x, 11.x, 12.x |
| Filament | 2.x, 3.x, 4.x, 5.x |
| Flux UI | 2.x Free, 2.x Pro |
| Inertia | 1.x, 2.x |
| Livewire | 1.x, 2.x, 3.x, 4.x |
| Nova | 4.x, 5.x |
| Pest | 3.x, 4.x |
| Tailwind CSS | 3.x, 4.x |

`Search Docs` MCP tool 会查询此 API。Guidelines 和 skills 会自动指示 agents 在需要实现细节时使用它。

## 何时使用 vs 何时不需要

| 场景 | 是否使用 Laravel Boost？ | 原因 |
|---|---|---|
| 使用 AI 辅助开发的 Laravel 项目 | 是 | 主要使用场景 |
| 团队使用 Cursor、Claude Code、Copilot 或其他 AI IDE | 是 | MCP 集成提升输出质量 |
| 需要在 AI 生成的代码中保持一致的 Laravel 约定 | 是 | Guidelines 强制执行标准 |
| 非 Laravel 的 PHP 项目 | 否 | Boost 是 Laravel 特定的 |
| 工作流中没有 AI 编码工具 | 否 | Boost 的价值在于 AI agent 集成 |
| 生产运行时性能优化 | 否 | Boost 仅在开发时使用，不是运行时优化器 |
| 已有完善的自定义 AI prompts | 可选 | Boost 可以补充或替代它们 |

## 扩展 Boost

### 自定义 Agent 注册

对于开箱即用不支持的 AI 工具：

```php
// 在 AppServiceProvider::boot() 中
use Laravel\Boost\Boost;

Boost::registerAgent('custom-ide', CustomAgent::class);
```

自定义 agent 类必须继承 `Laravel\Boost\Install\Agents\Agent` 并实现相关接口：
- `SupportsGuidelines` — 用于生成 guideline 文件
- `SupportsMcp` — 用于 MCP 服务器配置
- `SupportsSkills` — 用于生成 skill 文件

## 反模式 / 常见错误

| 反模式 | 为什么会失败 | 应该怎么做 |
|---|---|---|
| 在生产环境安装 | Boost 仅在开发时使用，增加不必要的开销 | 使用 `composer require --dev` |
| 覆盖所有内置 guidelines | 偏离 Laravel 核心团队的推荐 | 仅覆盖项目确实不同的部分 |
| 忽略 `boost:update` | Guidelines 与已安装的包不同步 | 每次 `composer update` 后运行 |
| 过于宽泛的自定义 skills | 激活时浪费上下文窗口 tokens | 将每个 skill 聚焦于单个领域 |
| 跳过 MCP 验证 | 配置错误的 MCP 会降低 AI agent 质量 | 安装后测试 tool 调用 |
| 不提交 `.mcp.json` | 团队成员获得不一致的 AI agent 体验 | 提交到版本控制 |
| 混用 guidelines 和 skills | 始终加载的内容造成上下文窗口污染 | conventions 放 guidelines，patterns 放 skills |
| 升级后未运行安装器 | 缺少新的 guideline 文件和 MCP tools | 主要升级后运行 `php artisan boost:install` |

## 防合理性化保护

- 不要因为"安装成功了"就跳过 MCP 验证 — 至少测试一次 tool 调用。
- 不要在没有记录偏离原因的情况下覆盖内置 guidelines。
- 不要创建宽泛的 skills — 如果覆盖多个领域，请拆分它。
- 不要在生产环境安装 Boost — 它纯粹是 `--dev` 依赖。
- 不要忘记在依赖变更后运行 `boost:update` — 过时的 guidelines 会降低 AI 输出质量。

## 文档查询（Context7）

使用 `mcp__context7__resolve-library-id` 然后 `mcp__context7__query-docs` 获取最新文档。返回的文档会覆盖记忆中的知识。
- `laravel/framework` — 用于核心 Laravel APIs、配置或 Artisan 命令
- `livewire` — 用于组件生命周期、wire 指令或 Alpine.js 集成

---

## 集成点

| Skill | 连接方式 |
|---|---|
| `laravel-specialist` | Boost guidelines 和 skills 提升 AI 生成的 Laravel 代码质量 |
| `php-specialist` | Boost 在生成的 guidelines 中尊重 PHP 版本和 PSR 标准 |
| `mcp-builder` | Boost 的 MCP 服务器是 MCP 模式的一个示例；可扩展它以添加自定义 tools |
| `self-learning` | Boost 的 Application Info tool 将项目上下文输入到学习阶段 |
| `senior-backend` | Boost 的 Database Schema 和 Query tools 支持后端架构决策 |
| `test-driven-development` | Boost 的 Pest skill 为 AI 生成的测试提供测试模式 |

## Skill 类型

**FLEXIBLE** — 根据项目需求调整流程阶段。新项目需要完整安装和 IDE 设置（阶段 1-4）。现有的 Boost 安装可能只需要自定义（阶段 3）或在 Laravel 升级后进行验证（阶段 4）。不可协商的最低要求：验证 MCP 服务器连通性，并确认 guidelines 与已安装的包版本匹配。