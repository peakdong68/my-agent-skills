---
name: laravel-specialist
description: >
  在构建或维护 Laravel 应用程序时使用 — Eloquent ORM、Blade、Livewire、
  队列、Pest 测试、中间件、服务提供者、迁移。
  触发条件：Laravel 项目设置、Eloquent 模型设计、Blade 或 Livewire
  组件创建、队列/任务实现、Pest 测试编写、中间件配置、
  迁移编写、路由定义、Form Request 验证、策略授权、
  Sanctum/Passport 认证、Horizon 队列监控。
---

# Laravel 专家

## 概述

遵循框架约定和最佳实践，设计、构建和维护生产级 Laravel 应用程序。本技能涵盖完整的 Laravel 生态系统：具有高级关系模式的 Eloquent ORM、Blade 模板和 Livewire 交互、队列和事件系统、中间件管道、服务提供者、各层级的 Pest 测试，以及用于迁移、种子和工厂的 Artisan 工具。

无论项目是全新开发还是存量维护，只要使用 Laravel 作为应用框架，即可应用此技能。

## 多阶段流程

### 阶段 1：上下文发现

1. 识别 Laravel 版本（`composer.json` -> `laravel/framework`）
2. 扫描 `config/` 以了解已启用的包和自定义配置
3. 映射现有模型、关系和迁移历史
4. 审查 `routes/` 中的 API、web、控制台和频道定义
5. 列举已安装的一官方包（Sanctum、Horizon、Telescope、Pulse、Pennant、Scout、Cashier）
6. 检查前端技术栈是 Livewire、Inertia 还是纯 Blade

> **停止 — 在不了解 Laravel 版本和已安装包的情况下，切勿开始架构审查。**

### 文档验证协议

**[硬性门槛]** 当对任何 Laravel API 不确定时 — 请验证，不要猜测。使用 `mcp__context7__resolve-library-id` 然后 `mcp__context7__query-docs`（首选）。备选方案：从 `https://github.com/laravel/docs` 获取。对于 Livewire、Pest、Inertia — 请分别通过 context7 单独解析。返回的文档优先于记忆中的知识。

### 阶段 2：架构审查

1. 验证目录结构是否遵循 Laravel 约定（见下方章节）
2. 评估服务提供者的注册和延迟加载
3. 审查中间件堆栈的顺序和分组
4. 评估队列连接配置和工作器拓扑
5. 检查缓存策略（配置、路由、视图、应用层级）

> **停止 — 在记录架构差距之前，切勿开始实现。**

### 阶段 3：实现

1. 首先编写迁移 — 数据库结构是事实来源
2. 构建带有关系、作用域、类型转换和访问器的 Eloquent 模型
3. 在专用的 Action 或 Service 类中实现业务逻辑
4. 创建控制器（单动作或资源型）并绑定到路由
5. 添加 Form Requests 进行验证，添加 Policies 进行授权
6. 为异步工作流连接事件、监听器和任务

> **停止 — 切勿跳过 Form Requests 和 Policies。内联验证和授权是反模式。**

### 阶段 4：测试

1. 为孤立逻辑编写单元测试（Actions、值对象、类型转换）
2. 为 HTTP 端点和中间件行为编写功能测试
3. 使用 Laravel Dusk 为关键用户流程编写浏览器测试
4. 使用 `assertDatabaseHas`、`assertSoftDeleted` 进行数据库断言
5. 使用队列和事件模拟进行副作用验证

> **停止 — 在所有层级测试通过之前，切勿进入优化阶段。**

### 阶段 5：优化

1. 应用预加载以消除 N+1 查询
2. 缓存耗时计算以及配置/路由/视图
3. 为频繁查询的列添加索引；使用 `EXPLAIN` 验证
4. 在开发环境中使用 Telescope 或 Debugbar 进行性能分析
5. 配置 Horizon 用于生产环境队列监控

## Eloquent 模式

### 关系

| 关系类型 | 方法 | 反向方法 | 使用场景 |
|---|---|---|---|
| 一对一 | `hasOne` | `belongsTo` | 用户 -> 个人资料 |
| 一对多 | `hasMany` | `belongsTo` | 文章 -> 评论 |
| 多对多 | `belongsToMany` | `belongsToMany` | 用户 <-> 角色（透视表） |
| 远层一对多 | `hasManyThrough` | — | 国家 -> 文章（通过用户） |
| 多态 | `morphMany` / `morphTo` | `morphTo` | 文章和视频的评论 |
| 多对多多态 | `morphToMany` | `morphedByMany` | 文章和视频的标签 |

### 作用域

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#scopes)。

### 访问器、修改器和类型转换

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#accessors-mutators-and-casts)。

### 使用预加载优化查询

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#eager-loading)。

## Blade 模板和 Livewire 组件

### Blade 约定
- 布局：`resources/views/layouts/app.blade.php` 使用 `@yield` / `@section` 或基于组件的 `<x-app-layout>`
- 组件：`resources/views/components/` — 匿名或基于类
- 局部视图：`@include('partials.sidebar')` 用于可复用片段
- 使用 `{{ }}` 进行转义输出，仅当 HTML 明确可信时使用 `{!! !!}`
- 优先使用 Blade 指令（`@auth`、`@can`、`@env`）而非原始 PHP 条件语句

### Livewire 模式

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#livewire-patterns)。

### 前端技术栈决策表

| 决策因素 | 选择 Livewire | 选择 Inertia |
|---|---|---|
| 现有 Blade 代码库 | 是 | 否 |
| 需要 SPA 式体验 | 部分支持（使用 wire:navigate） | 是 |
| 团队具备 Vue/React 专长 | 否 | 是 |
| 服务端渲染优先级 | 是 | 取决于适配器 |
| 实时响应性 | 是（轮询、流） | 需要配置 Echo |
| SEO 关键页面 | 均可 | 均可（SSR 适配器） |

## 队列、任务和事件模式

### 任务设计

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#job-design)。

### 事件 / 监听器模式

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#event--listener-pattern)。

### 同步 vs 异步决策表

| 任务 | 队列化 | 同步 |
|---|---|---|
| 发送邮件 / 通知 | 是 | 绝不在请求周期内 |
| PDF 生成 | 是 | 仅当 < 2 秒且用户等待时 |
| 支付处理 | 视情况 — 优先使用 webhook 驱动 | 如果网关响应 < 5 秒 |
| 缓存预热 | 是 | 绝不 |
| 审计日志 | 是（高流量）或同步（低流量） | 如果需要保证交付 |
| 搜索索引 | 是 | 绝不 |

## 中间件和服务提供者

### 中间件堆栈顺序

中间件顺序很重要。`bootstrap/app.php` 中的默认堆栈（Laravel 11+）：

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#middleware-stack)。

### 服务提供者最佳实践
- 在 `register()` 中注册绑定，绝不要在那里从容器解析
- 启动逻辑（事件监听器、路由模型绑定、宏）放在 `boot()` 中
- 对非每个请求都需要的绑定使用延迟提供者
- 避免在提供者中放置繁重逻辑 — 委托给专用类

## 使用 Pest 进行测试

### 单元测试

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#pest-tests)。

### 功能测试

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#pest-tests)。

### 队列和事件模拟

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#pest-tests)。

### 浏览器测试（Dusk）

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#pest-tests)。

## Artisan 命令、迁移、种子、工厂

### 迁移约定

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#migration-conventions)。

### 工厂模式

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#factory-patterns)。

## Laravel 目录结构约定

> 目录结构示例请参见 [REFERENCE.md](./REFERENCE.md#directory-structure)。

## 决策表

### 认证策略

| 场景 | 推荐方案 |
|---|---|
| SPA + 同域名 | Sanctum（基于 cookie，CSRF 防护） |
| SPA + 不同域名 | Sanctum（基于 token） |
| 移动应用 | Sanctum（基于 token） |
| 第三方 API 消费者 | Passport（OAuth2） |
| 简单 API token | Sanctum（明文哈希） |
| 社交登录 | Socialite + Sanctum |

### 缓存层

| 数据类型 | 缓存驱动 | TTL | 失效策略 |
|---|---|---|---|
| 配置 / 路由 / 视图 | File（artisan cache） | 直到下次部署 | `artisan optimize:clear` |
| 数据库查询结果 | Redis / Memcached | 5-60 分钟 | 事件驱动或 TTL |
| 完整页面 / 片段 | Redis | 1-15 分钟 | 缓存标签 |
| 会话数据 | Redis | 会话生命周期 | 自动 |
| 速率限制 | Redis | 窗口时长 | 自动 |

### 文件存储

| 场景 | 磁盘 | 驱动 |
|---|---|---|
| 用户上传（生产环境） | `s3` | Amazon S3 / 兼容服务 |
| 用户上传（本地开发） | `local` | 本地文件系统 |
| 公共资源 | `public` | 本地带符号链接 |
| 临时文件 | `local` | 本地，按计划清理 |

## 反模式 / 常见错误

| 反模式 | 失败原因 | 替代方案 |
|---|---|---|
| 胖控制器 | 业务逻辑不可测试、不可维护 | 将逻辑移至 Action 或 Service 类 |
| 控制器中直接使用原始 SQL | SQL 注入风险，不可移植 | 使用 Eloquent 或查询构建器 |
| 缺少批量赋值保护 | 数据操纵漏洞 | 始终定义 `$fillable` 或 `$guarded` |
| 控制器中内联验证 | 将验证与 HTTP 层耦合 | 使用 Form Requests |
| 任务未配置重试/退避 | 静默失败，无法恢复 | 配置 `$tries`、`$backoff`、`failed()` |
| 过度使用全局作用域 | 隐藏的查询行为让开发者困惑 | 优先使用本地作用域 |
| 以浮点数存储金额 | 浮点精度误差 | 使用整数分，展示时转换 |
| 缺少数据库索引 | 大规模时查询缓慢 | 为 WHERE + ORDER BY 添加复合索引 |
| 配置文件中的密钥 | 版本控制中凭证泄露 | 仅使用 `.env` |
| 针对生产数据库测试 | 数据损坏，测试不可靠 | 使用 SQLite 内存或专用测试数据库 |
| API 响应中的懒加载 | N+1 查询，API 响应缓慢 | 在开发环境中启用 `preventLazyLoading()` |

## 反合理化防护

- 切勿跳过迁移而直接编辑数据库 — 迁移是事实来源。
- 切勿因为"更快"而将业务逻辑放入控制器 — 使用 Action 类。
- 切勿因为"验证很简单"而跳过 Form Requests — 它总会增长。
- 切勿因为"很烦人"而禁用 `preventLazyLoading()` — 修复 N+1 查询。
- 切勿因为"金额很小"而以浮点数存储金额 — 精度误差会累积。

## 集成点

| 技能 | 连接方式 |
|---|---|
| `php-specialist` | 现代 PHP 8.x 模式是所有 Laravel 代码的基础 |
| `laravel-boost` | AI 辅助开发指南和 MCP 工具 |
| `senior-backend` | API 设计、缓存策略、事件驱动架构 |
| `test-driven-development` | 采用 RED-GREEN-REFACTOR 的 Pest 测试工作流 |
| `database-schema-design` | 迁移规划、索引策略、数据建模 |
| `security-review` | Sanctum/Passport 配置、CSRF、输入验证 |
| `performance-optimization` | 查询分析、缓存调优、队列工作器扩展 |
| `deployment` | Forge/Vapor/Envoyer 部署、`artisan optimize` |
| `context7 MCP` | 当信息不确定时获取最新的 Laravel 文档 |
| `laravel/docs` GitHub | Laravel API 参考的权威来源 |

## 技能类型

**灵活** — 根据工作范围调整多阶段流程。单个模型变更可能完全跳过阶段 2，而新模块应遵循全部五个阶段。核心约定（预加载、Form Requests、Pest 测试、迁移优先的结构变更）无论范围如何都是不可协商的。
