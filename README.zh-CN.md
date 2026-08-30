# Meal Choice & Food Library Mini Program

一个用于记录个人美食库并随机决定“下一顿吃什么”的全栈微信小程序项目。

> **状态：** 持续开发中  
> **角色：** 独立全栈开发  
> **重点：** 微信小程序 · Node.js / Fastify REST API · MySQL · 腾讯云 COS · 微信身份与内容安全 · 容器化部署

[English](README.md) | **简体中文**

## 项目概述

“这顿有解”是一个原生微信小程序，核心是把日常吃过、想吃、做过和想做的内容记录成个人美食库，再在不知道吃什么时从符合条件的候选项里随机抽取结果。

产品分为两个相互独立的使用模式：

- **外面吃：** 记录店铺与具体菜品，可保存分类、评分、就餐方式、标签、备注、价格、时间和可选图片。
- **自己烧：** 记录做过或想做的菜，并在需要时随机决定今天做什么。

核心流程：

```text
记录食物
  ↓
整理个人美食库
  ↓
筛选候选范围
  ↓
随机抽菜品 / 店铺
  ↓
通过轻量盲盒交互揭晓结果
```

完整源代码保存在私有仓库。本公开仓库只用于作品展示，重点说明产品能力、架构、工程决策和当前开发状态。

## 已完成的主要工作

- 原生微信小程序前端
- Node.js / Fastify REST API 后端
- MySQL 8 数据持久化与顺序迁移
- 店铺—菜品关系与批量事务保存
- 美食库搜索、筛选、统计、编辑和删除
- 外食 / 自己烧两套独立抽选逻辑
- 微信静默身份与应用自有 session/token
- 真实微信文字内容安全接口
- 腾讯云 COS 私有图片上传、删除和短期签名访问
- 按用户隔离数据库数据与媒体资产
- local / localApi / wechatApi / wechatMediaApi / production 环境边界
- Docker、Nginx 与 HTTPS 生产部署准备
- 前后端自动化测试及 localhost API 冒烟测试

## 架构

```text
微信小程序
   │
   │ REST / HTTPS
   ▼
Node.js / Fastify
   │
   ├── MySQL：结构化业务数据
   ├── Tencent COS：私有图片
   └── WeChat API：身份与内容安全
```

更详细的分层设计见 [docs/architecture.md](docs/architecture.md)。

## 工程重点

项目没有让页面直接依赖数据库、COS 或微信服务端接口，而是通过 Repository、MediaService 和后端端口进行隔离。

目前的关键工程设计包括：

- production 启动时拒绝 mock、本地图片等不安全配置；
- COS object key 由后端生成，并限制在项目专属前缀内；
- 客户端不能自行指定 object key；
- 所有媒体操作均校验用户归属；
- 图片同时检查 MIME 与实际文件头；
- 数据库只保存媒体元数据，不持久化短期签名 URL；
- 列表页不请求图片 URL，只有详情页按需签名；
- 新增菜品失败时会回滚已经上传的 COS 图片；
- 删除记录时区分“数据库已删但 COS 清理失败”的 partial 状态；
- 店铺最后一道有效菜品被删除后，空店铺自动软删除。

## 当前状态

目前已完成并实际联调：

- localhost MySQL + Node API；
- 真实微信身份与应用 session；
- 真实微信文字审核；
- 私有 COS 上传、显示、替换、删除与失败回滚链路。

尚未完成：

1. 微信图片内容安全；
2. TDSQL-C Serverless 真实连接；
3. Lighthouse + Nginx + HTTPS 实际部署；
4. production 微信合法域名与真机验收；
5. 本地数据迁移到正式环境；
6. 体验版与发布前验收。

详见 [docs/project-status.md](docs/project-status.md)。

## 公开仓库边界

本仓库不会公开：

- 私有生产源码；
- 微信 AppSecret 或其他密钥；
- COS SecretId / SecretKey；
- 数据库密码与真实数据；
- 用户数据；
- 带签名的私有图片 URL；
- 私有部署配置。

该仓库只作为作品集展示，不代替私有生产仓库。
