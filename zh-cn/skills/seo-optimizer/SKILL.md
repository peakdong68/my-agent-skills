---
name: seo-optimizer
description: >
  当用户需要技术SEO审计、元标签优化、结构化数据标记、
  Core Web Vitals改进或搜索引擎可见性增强时使用。
  触发条件：网站审计请求、元标签审查、Schema.org实施、
  页面速度优化、可索引性问题、sitemap或robots.txt配置、
  hreflang设置、Open Graph或Twitter Card标签、富媒体摘要资格。
---

# SEO优化器

## 概述

通过技术SEO、页面内优化、结构化数据实施和Core Web Vitals性能调优来优化网站的搜索引擎可见性。本技能涵盖可抓取性、可索引性、元标签策略、Schema.org标记、sitemap生成、规范URL管理、国际化（hreflang）以及针对Google排名信号的性能优化。

每当网站需要提升自然搜索表现时应用此技能，无论是单页审计还是全站改造。

## 多阶段流程

### 阶段1：技术审计

1. 抓取网站（使用Screaming Frog、Sitebulb或自定义脚本）
2. 检查robots.txt和XML sitemap的有效性
3. 识别抓取错误、重定向链和死链接
4. 验证规范URL和重复内容处理
5. 评估移动友好性和响应式设计
6. 检查HTTPS实施和混合内容问题
7. 评估页面加载性能（Core Web Vitals）

> **停止 — 在记录并优先处理审计发现之前，切勿进入阶段2。**

### 阶段2：页面内优化

1. 审计标题标签（唯一性、50-60字符、关键词放置）
2. 审查元描述（唯一性、150-160字符、吸引人的行动号召）
3. 分析标题层级结构（单个H1、逻辑清晰的H2-H6结构）
4. 优化图片alt文本和文件名
5. 审查内部链接结构和锚文本
6. 检查URL结构（简短、描述性、使用连字符）
7. 验证Open Graph和Twitter Card标签

> **停止 — 在实施并验证页面内更改之前，切勿进入阶段3。**

### 阶段3：结构化数据实施

1. 识别内容适用的Schema.org类型
2. 实施JSON-LD结构化数据
3. 使用Google富媒体结果测试工具验证
4. 测试富媒体摘要资格
5. 在Search Console中监控结构化数据错误

> **停止 — 在结构化数据通过验证之前，切勿进入阶段4。**

### 阶段4：监控与迭代

1. 设置Google Search Console监控
2. 持续追踪Core Web Vitals表现
3. 监控索引状态和覆盖率
4. 审查搜索表现（点击量、展示量、CTR、排名位置）
5. 设置抓取错误和排名下降的警报

## 审计优先级决策表

| 发现项 | 严重程度 | 修复优先级 | 影响 |
|---|---|---|---|
| 无HTTPS/混合内容 | 严重 | 立即 | 排名惩罚、信任信号受损 |
| 缺少规范URL | 严重 | 立即 | 重复内容权重稀释 |
| 断裂的重定向链（3+跳） | 高 | 本冲刺周期 | 抓取预算浪费、链接权重损失 |
| 缺失或重复的标题标签 | 高 | 本冲刺周期 | CTR下降、排名混淆 |
| 无结构化数据 | 中 | 下一冲刺周期 | 错过富媒体摘要机会 |
| 图片缺少alt文本 | 中 | 下一冲刺周期 | 无障碍访问和图片搜索损失 |
| Core Web Vitals不达标 | 中 | 下一冲刺周期 | 排名信号、用户体验受损 |
| 缺少hreflang标签（多语言） | 低 | 待办列表 | 地理定位问题 |
| 非描述性URL片段 | 低 | 待办列表 | 对排名和CTR影响轻微 |

## 元标签参考

### 核心元标签
```html
<head>
  <!-- 主要标签 -->
  <title>主关键词 - 次关键词 | 品牌名称</title>
  <meta name="description" content="引人注目的150-160字符描述，包含目标关键词和清晰的价值主张。">
  <link rel="canonical" href="https://example.com/page">

  <!-- 机器人指令 -->
  <meta name="robots" content="index, follow">
  <!-- 或针对不应被索引的页面使用 noindex, nofollow -->

  <!-- 视口（移动端） -->
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <!-- Open Graph -->
  <meta property="og:type" content="website">
  <meta property="og:title" content="用于社交分享的页面标题">
  <meta property="og:description" content="针对社交分享优化的描述。">
  <meta property="og:image" content="https://example.com/og-image.jpg">
  <meta property="og:url" content="https://example.com/page">
  <meta property="og:site_name" content="品牌名称">

  <!-- Twitter Card -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="页面标题">
  <meta name="twitter:description" content="针对Twitter优化的描述。">
  <meta name="twitter:image" content="https://example.com/twitter-image.jpg">

  <!-- 国际化 -->
  <link rel="alternate" hreflang="en" href="https://example.com/en/page">
  <link rel="alternate" hreflang="de" href="https://example.com/de/page">
  <link rel="alternate" hreflang="x-default" href="https://example.com/page">
</head>
```

### 元标签优化矩阵

| 元素 | 最大长度 | 优先关键词 | 常见错误 |
|---|---|---|---|
| 标题标签 | 50-60字符 | 前置主关键词 | 过长、关键词堆砌、重复 |
| 元描述 | 150-160字符 | 包含行动号召和关键词 | 缺失、重复、无行动号召 |
| H1 | 不适用（每页单个） | 主关键词变体 | 多个H1、缺失H1 |
| URL片段 | 3-5个词 | 目标关键词 | 过长、含参数、使用下划线 |
| 图片alt | 125字符 | 描述性、自然融入关键词 | 空白、"图片显示..."、关键词堆砌 |
| OG标题 | 60-90字符 | 吸引人、易分享 | 与标题标签相同（错失机会） |

## 结构化数据（JSON-LD）

### 文章
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "如何优化Core Web Vitals",
  "author": {
    "@type": "Person",
    "name": "Jane Smith",
    "url": "https://example.com/authors/jane-smith"
  },
  "datePublished": "2026-03-01",
  "dateModified": "2026-03-15",
  "image": "https://example.com/images/article-hero.jpg",
  "publisher": {
    "@type": "Organization",
    "name": "Example Blog",
    "logo": {
      "@type": "ImageObject",
      "url": "https://example.com/logo.png"
    }
  },
  "description": "一份提升LCP、FID和CLS分数的综合指南。"
}
</script>
```

### 产品
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Widget Pro",
  "description": "面向企业使用的专业级小部件。",
  "image": "https://example.com/widget-pro.jpg",
  "brand": { "@type": "Brand", "name": "WidgetCo" },
  "offers": {
    "@type": "Offer",
    "price": "49.99",
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock",
    "url": "https://example.com/widget-pro"
  },
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "4.7",
    "reviewCount": "312"
  }
}
</script>
```

### FAQ页面
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "什么是Core Web Vitals？",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Core Web Vitals是一组衡量加载、交互性和视觉稳定性方面真实用户体验的指标。"
      }
    }
  ]
}
</script>
```

### Schema类型决策指南

| 内容类型 | Schema类型 | 富媒体结果 |
|---|---|---|
| 博客文章 | Article | 文章摘要 |
| 产品页面 | Product | 价格、评分、库存状态 |
| FAQ板块 | FAQPage | 可展开的问答 |
| 操作指南 | HowTo | 步骤摘要 |
| 食谱 | Recipe | 图片、时间、评分 |
| 活动 | Event | 日期、地点、价格 |
| 本地商家 | LocalBusiness | 地图包、营业时间 |
| 软件应用 | SoftwareApplication | 评分、价格 |
| 面包屑导航 | BreadcrumbList | 面包屑路径 |
| 视频 | VideoObject | 缩略图、时长 |

## Core Web Vitals

### 指标与阈值

| 指标 | 良好 | 需要改进 | 较差 | 衡量内容 |
|---|---|---|---|---|
| LCP（最大内容绘制） | <= 2.5秒 | <= 4.0秒 | > 4.0秒 | 加载性能 |
| INP（下次绘制交互） | <= 200毫秒 | <= 500毫秒 | > 500毫秒 | 交互性 |
| CLS（累积布局偏移） | <= 0.1 | <= 0.25 | > 0.25 | 视觉稳定性 |

### LCP优化
```html
<!-- 预加载首屏图片 -->
<link rel="preload" as="image" href="/hero.webp" fetchpriority="high">

<!-- 使用现代图片格式 -->
<picture>
  <source srcset="/hero.avif" type="image/avif">
  <source srcset="/hero.webp" type="image/webp">
  <img src="/hero.jpg" alt="首屏图片描述" width="1200" height="600"
       fetchpriority="high" decoding="async">
</picture>
```

### CLS预防
```css
/* 始终为图片和视频设置尺寸 */
img, video {
  width: 100%;
  height: auto;
  aspect-ratio: 16 / 9;
}

/* 为动态内容预留空间 */
.ad-slot {
  min-height: 250px;
}

/* 避免在现有内容上方插入内容 */
.notification-bar {
  position: fixed; /* 不会导致布局偏移 */
}
```

### INP优化
```javascript
// 通过yield拆分长任务
async function processLargeDataset(data) {
  for (let i = 0; i < data.length; i++) {
    processItem(data[i]);
    if (i % 100 === 0) {
      await new Promise(resolve => setTimeout(resolve, 0)); // 让出主线程
    }
  }
}

// 使用requestIdleCallback处理非关键工作
requestIdleCallback(() => {
  loadAnalytics();
  initNonCriticalFeatures();
});
```

## XML Sitemap

### 生成模式
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://example.com/</loc>
    <lastmod>2026-03-15</lastmod>
    <changefreq>daily</changefreq>
    <priority>1.0</priority>
  </url>
  <url>
    <loc>https://example.com/products</loc>
    <lastmod>2026-03-14</lastmod>
    <changefreq>weekly</changefreq>
    <priority>0.8</priority>
  </url>
</urlset>
```

### Sitemap最佳实践
- 每个sitemap文件最多50,000个URL
- 大型网站使用sitemap索引
- 仅包含规范的、可索引的URL
- 仅在内容真正变更时更新`lastmod`
- 通过Google Search Console和robots.txt提交

## robots.txt模板
```
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/
Disallow: /search?
Disallow: /*?sort=
Disallow: /*?filter=

Sitemap: https://example.com/sitemap.xml
```

## Next.js / React SEO模式

### Next.js Metadata API（App Router）
```typescript
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: {
    template: '%s | 品牌名称',
    default: '品牌名称 - 标语',
  },
  description: '全站默认描述。',
  openGraph: {
    type: 'website',
    locale: 'en_US',
    url: 'https://example.com',
    siteName: '品牌名称',
  },
  twitter: {
    card: 'summary_large_image',
  },
  robots: {
    index: true,
    follow: true,
  },
  alternates: {
    canonical: 'https://example.com',
  },
};
```

### 每页动态元数据
```typescript
export async function generateMetadata({ params }): Promise<Metadata> {
  const product = await getProduct(params.slug);

  return {
    title: product.name,
    description: product.description.slice(0, 160),
    openGraph: {
      title: product.name,
      description: product.description.slice(0, 200),
      images: [{ url: product.image, width: 1200, height: 630 }],
    },
  };
}
```

## 反模式/常见错误

| 反模式 | 失败原因 | 替代方案 |
|---|---|---|
| 标题/内容中关键词堆砌 | 触发垃圾过滤、降低CTR | 自然使用一次主关键词，添加变体 |
| 多页使用相同标题/描述 | 重复内容信号、浪费机会 | 为每个可索引URL设置独特、页面专属的元数据 |
| 在robots.txt中屏蔽CSS/JS | Googlebot无法渲染页面 | 允许所有渲染资源 |
| 结构化数据与页面内容不匹配 | Google惩罚、富媒体摘要被移除 | Schema必须精确反映可见内容 |
| 重定向链超过2跳 | 抓取预算浪费、链接权重损失 | 直接重定向到最终目标 |
| 仅使用JS导航且无SSR链接 | 爬虫无法发现页面 | 服务器渲染导航链接 |
| 忽略Core Web Vitals | 排名信号降级 | 分析并优化LCP、INP、CLS |
| 缺少规范URL | 重复内容惩罚 | 为每个可索引页面设置规范链接 |
| 过度优化的锚文本 | 非自然链接模式触发惩罚 | 使用描述性、多样化的锚文本 |
| 用CSS隐藏内容以优化SEO | 违反伪装政策 | 所有SEO内容必须对用户可见 |

## 反合理化防护

- 切勿因"网站看起来没问题"而跳过技术审计 — 请实际抓取。
- 切勿在未通过富媒体结果测试验证前添加结构化数据。
- 切勿在未检查每种页面类型前假设元标签正确。
- 切勿在未进行前后对比测量的情况下部署SEO更改。
- 切勿以牺牲用户体验为代价优化搜索引擎。

## 集成点

| 技能 | 连接方式 |
|---|---|
| `content-creator` | SEO为营销内容的关键词定位和标题策略提供指导 |
| `content-research-writer` | 基于研究的内容需要SEO优化的结构和元标签 |
| `senior-frontend` | Core Web Vitals优化需要前端性能调优 |
| `performance-optimization` | 页面速度直接影响LCP和INP分数 |
| `deployment` | SEO更改需要正确的缓存失效和重定向配置 |
| `tech-docs-generator` | 文档网站需要sitemap、规范链接和结构化数据设置 |

## 技能类型

**灵活** — 根据网站的技术栈、内容类型和竞争格局调整优化策略。技术SEO基础知识和结构化数据最佳实践强烈推荐；具体实施模式因框架而异。