---
name: php-specialist
description: >
  编写现代 PHP 8.x 代码时使用 — enums、fibers、readonly 属性、PSR 标准、
  Composer、静态分析、SOLID 模式。
  触发条件：PHP 代码编写、enum 设计、readonly DTO 创建、PSR-4 自动加载配置、
  PHPStan 或 Psalm 配置、PHP CS Fixer 或 Pint 配置、Composer 依赖管理、
  SOLID 原则应用、类型安全改进、自定义异常层次结构、接口驱动设计。
---

# PHP 专家

## 概述

编写符合 PSR 标准和 SOLID 原则的现代、类型安全且可维护的 PHP 8.x 代码。本技能涵盖完整的现代 PHP 工具链：PHP 8.0 至 8.4 引入的语言特性、PSR 互操作性标准、Composer 依赖管理、使用 PHPStan 和 Psalm 进行静态分析、使用 PHP CS Fixer 和 Pint 强制执行代码风格，以及利用类型系统在编译时（而非运行时）保证正确性的架构模式。

在任何框架或独立上下文中编写、审查或重构 PHP 代码时，均应应用此技能。

## 多阶段流程

### 阶段 1：环境评估

1. 从 `composer.json` -> `require.php` 中识别 PHP 版本
2. 检查 `composer.json` 中的自动加载策略（PSR-4 命名空间）
3. 检查静态分析配置（`phpstan.neon`、`psalm.xml`）
4. 识别代码规范工具（`pint.json`、`.php-cs-fixer.php`）
5. 编目现有模式：enums、DTO、值对象、接口

> **停止 — 在不知道 PHP 版本和自动加载策略之前，不要编写代码。**

### 阶段 2：设计

1. 在实现之前定义接口和契约
2. 使用 readonly 属性设计值对象和 DTO
3. 将领域概念映射到 backed enums（如适用）
4. 规划领域的异常层次结构
5. 识别依赖注入的接缝点

> **停止 — 在没有为关键边界定义接口之前，不要实现。**

### 阶段 3：实现

1. 先编写接口 — 契约优先于具体类
2. 使用构造函数提升、readonly 属性、联合/交叉类型实现
3. 优先使用 match 表达式而非 switch；使用命名参数提高可读性
4. 利用一等可调用语法进行函数组合
5. 在每个类边界应用 SOLID 原则

> **停止 — 不要在任何 PHP 文件中跳过 strict_types 声明。**

### 阶段 4：质量保证

1. 在可达到的最高级别运行 PHPStan（目标级别 9）
2. 使用 PHP CS Fixer 或 Laravel Pint 强制执行代码风格
3. 验证类型覆盖率 — 无正当理由不使用 `mixed`
4. 检查 SOLID 违规和代码坏味道
5. 确认 Composer 自动加载已优化（`--classmap-authoritative`）

## PHP 版本特性决策表

| 特性 | 最低版本 | 使用场景 |
|---|---|---|
| 构造函数提升 | 8.0 | 任何包含构造函数参数的类 |
| 命名参数 | 8.0 | 具有 3 个以上参数或布尔标志的函数 |
| match 表达式 | 8.0 | 任何 switch 语句（严格、返回值） |
| 联合类型 | 8.0 | 参数接受多种类型 |
| Backed enums | 8.1 | 任何带值的命名常量集合 |
| Readonly 属性 | 8.1 | 不可变的 DTO、值对象 |
| Fibers | 8.1 | 异步框架（很少直接使用） |
| 一等可调用语法 | 8.1 | 函数组合、array_map/filter |
| Readonly 类 | 8.2 | 全只读 DTO（简写形式） |
| DNF 类型 | 8.2 | 复杂的联合 + 交叉类型组合 |
| Override 属性 | 8.3 | 重写父类方法（安全检查） |
| 属性钩子 | 8.4 | 无需单独方法的计算属性 |

## 现代 PHP 8.x 特性

### Enums（PHP 8.1+）

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#enums-php-81)。

### Readonly 属性和类（PHP 8.1/8.2）

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#readonly-properties-and-classes-php-81-82)。

### 构造函数提升

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#constructor-promotion)。

### 命名参数

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#named-arguments)。

### match 表达式

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#match-expressions)。

### 联合类型和交叉类型

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#union-and-intersection-types)。

### 一等可调用语法（PHP 8.1+）

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#first-class-callable-syntax-php-81)。

### Fibers（PHP 8.1+）

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#fibers-php-81)。

## PSR 标准

| PSR | 名称 | 相关性 |
|---|---|---|
| PSR-1 | 基础编码标准 | 基线：`<?php` 标签、UTF-8、命名空间/类约定 |
| PSR-4 | 自动加载 | 在 `composer.json` 中将命名空间映射到目录 — 强制性 |
| PSR-7 | HTTP 消息接口 | 用于中间件管道的不可变请求/响应对象 |
| PSR-11 | 容器接口 | 依赖注入容器互操作性 |
| PSR-12 | 扩展编码风格 | 取代 PSR-2：格式、空格、声明 |
| PSR-15 | HTTP 服务器中间件 | `MiddlewareInterface` 和 `RequestHandlerInterface` |
| PSR-17 | HTTP 工厂 | 创建 PSR-7 对象（RequestFactory、ResponseFactory） |
| PSR-18 | HTTP 客户端 | `ClientInterface` 用于可互操作的 HTTP 客户端 |

### PSR-4 自动加载

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#psr-4-autoloading)。

## Composer 依赖管理

### 基本命令

| 命令 | 用途 |
|---|---|
| `composer require package/name` | 添加生产依赖 |
| `composer require package/name --dev` | 添加开发依赖 |
| `composer update --dry-run` | 预览将要更改的内容 |
| `composer why package/name` | 显示为什么安装了某个包 |
| `composer audit` | 检查已知的安全漏洞 |
| `composer bump` | 将版本约束更新为已安装版本 |
| `composer validate --strict` | 验证 `composer.json` 和 `composer.lock` |

### 最佳实践
- 始终提交 `composer.lock` — 确保跨环境的可重现安装
- 使用 `^`（插入符）约束：`"laravel/framework": "^12.0"` 允许次版本和补丁更新
- 分离开发依赖：测试、静态分析和调试工具放入 `require-dev`
- 在 CI 中运行 `composer audit` 以捕获已知漏洞
- 在生产环境中使用 `composer dump-autoload --classmap-authoritative` 以提高速度

## 静态分析

### PHPStan 级别

| 级别 | 检查内容 |
|---|---|
| 0 | 基础：未定义变量、未知类、错误的函数调用 |
| 1 | + 可能未定义的变量、`$this` 上的未知方法 |
| 2 | + 所有表达式上的未知方法（不仅仅是 `$this`） |
| 3 | + 验证返回类型 |
| 4 | + 死代码、永远为真/假的条件 |
| 5 | + 函数调用的参数类型 |
| 6 | + 报告缺少的类型提示 |
| 7 | + 穷尽检查联合类型 |
| 8 | + 严格检查可空类型 |
| 9 | + 禁止 `mixed` 类型（除非显式处理） |

### PHPStan 配置

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#phpstan-configuration)。

### PHP CS Fixer / Pint

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#php-cs-fixer-pint)。

对于 Laravel 项目，使用带 `pint.json` 预设的 Pint — 它包装了 PHP CS Fixer 并提供了 Laravel 特定的默认值。

## PHP 中的 SOLID 原则

| 原则 | 指导方针 | PHP 机制 |
|---|---|---|
| **S** — 单一职责 | 每个类只有一个更改的原因 | Action 类、小型服务 |
| **O** — 开闭原则 | 扩展行为而不修改源代码 | 接口、策略模式、enums |
| **L** — 里氏替换 | 子类型必须能替代基类型 | 协变返回、逆变参数 |
| **I** — 接口隔离 | 客户端仅依赖其使用的方法 | 小型、聚焦的接口 |
| **D** — 依赖倒置 | 依赖抽象而非具体实现 | 构造函数注入、接口绑定 |

### 依赖倒置示例

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#dependency-inversion-example)。

## 错误处理模式

### 自定义异常层次结构

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#custom-exception-hierarchy)。

### Result 模式（错误作为值）

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#result-pattern-error-as-value)。

## 类型安全模式

### 通过 Readonly 类实现品牌/不透明类型

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#branded-opaque-types-via-readonly-classes)。

### 通过 PHPStan 注解实现泛型集合

> 代码示例请参见 [REFERENCE.md](./REFERENCE.md#generic-collections-via-phpstan-annotations)。

## 反模式 / 常见错误

| 反模式 | 问题所在 | 替代方案 |
|---|---|---|
| 使用 `mixed` 作为逃生舱 | 类型安全网出现漏洞 | 使用联合类型或泛型缩小范围 |
| 字符串类型代码 | 拼写错误导致运行时错误 | 对命名常量使用 backed enums |
| 上帝类（过多职责） | 难以测试、高耦合 | 拆分为 Action 类 |
| 抑制静态分析 | 隐藏真正的 bug | 修复问题，仅在附有说明时使用 `@phpstan-ignore` |
| 缺少 `declare(strict_types=1)` | 静默的类型强制转换错误 | 添加到每个 PHP 文件 |
| 数组形状的领域数据 | 无 IDE 支持、无类型安全 | 使用 readonly DTO 或值对象 |
| 服务定位器（逻辑中的 `app()`） | 隐藏依赖、难以测试 | 构造函数注入 |
| 广泛捕获 `\Exception` | 吞没意外错误 | 捕获特定异常类型 |
| 可变值对象 | 共享状态错误 | 使用 `readonly` 类，返回新实例 |
| 忽略 `composer audit` | 生产环境中的已知漏洞 | 在 CI 中运行，视为构建失败 |
| 深层继承（3 层以上） | 脆弱的基类问题 | 优先使用组合和接口 |
| 类未标记 `final` | 意外的扩展 | 默认使用 `final`，仅在为此设计时开放 |

## 反合理化防护

- 不要因为"这只是个小脚本"而跳过 `declare(strict_types=1)` — 随处添加。
- 不要在无注释说明为何无法使用更窄类型的情况下使用 `mixed`。
- 不要在未附上书面解释说明代码为何正确的情况下抑制 PHPStan 错误。
- 不要在业务逻辑中使用服务定位器模式（`app()`），即使在 Laravel 中也不行。
- 不要因为"只有一个实现"而跳过关键边界的接口 — 迟早会有第二个。

## 文档查询（Context7）

先使用 `mcp__context7__resolve-library-id`，然后使用 `mcp__context7__query-docs` 获取最新文档。返回的文档将覆盖记忆中的知识。
- `php` — 语言特性、内置函数或 PHP 8.x 语法
- `composer` — 包管理、自动加载或脚本配置

---

## 集成点

| 技能 | 连接方式 |
|---|---|
| `laravel-specialist` | PHP 8.x 特性驱动 Eloquent  casts、enums、readonly DTO 和类型化集合 |
| `senior-backend` | SOLID 架构、接口驱动设计、错误处理模式 |
| `test-driven-development` | PHPUnit/Pest 测试，带有强类型断言 |
| `clean-code` | PHP 层面的 SOLID、DRY、代码坏味道检测 |
| `security-review` | 输入验证、类型强制转换风险、依赖漏洞 |
| `laravel-boost` | 通过指南和 MCP 工具提高 AI 生成的 PHP 代码质量 |

## 技能类型

**灵活** — 根据工作范围调整流程阶段。单个函数可能只需要阶段 3 和 4。新模块或包应遵循所有四个阶段。无论范围大小，以下要求不可协商：`declare(strict_types=1)`、项目配置级别的 PHPStan 合规性，以及 PSR-4 自动加载。
