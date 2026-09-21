# 投标报价计算器

## How to Run

```bash
# 构建并启动
docker compose up --build -d

# 停止服务
docker compose down
```

## 服务

| 服务 | 端口 | 地址 |
|------|------|------|
| 前端应用 | 8082 | http://localhost:8082 |

## 网关规则说明

Nginx 配置以模板形式放在 `frontend-user/templates/`，容器启动时由官方镜像的
envsubst 机制渲染到 `/etc/nginx`：

- **多级路径直达 / 刷新**：非静态资源统一回退到 `index.html`，多级路径直接进入或刷新都落在同一页面；缺失的静态资源（js/css 等）直接返回 404，不回退成 HTML，避免页面拿到错误内容而白屏。
- **缓存**：入口 HTML `no-cache`（每次协商校验，发布后立即生效）；无指纹的 js/css 使用短缓存 + ETag 协商（`max-age=300, must-revalidate`）；带内容哈希的 `/assets/*` 资源长期 `immutable` 缓存。
- **内嵌（iframe）**：使用 CSP `frame-ancestors` 控制，默认 `*` 允许任意页面内嵌；通过环境变量 `FRAME_ANCESTORS` 收紧（在 `docker-compose.yml` 的 `environment` 中设置，如 `'self'` 或 `'self' https://example.com`）。
- **异常页**：404 与 5xx 返回自包含的中文提示页（`404.html` / `50x.html`），不出现空白页。
- **压缩**：对文本类响应启用 gzip（`Vary: Accept-Encoding`）。

## 测试账号

无需登录，纯前端静态应用。

## 题目内容

> 新建一个html应用，用于模拟计算投标报价计算，支持单低和双低模式，比例、限价及分数都可以自定义，且可以任意添加多家报价。并计算显示报价是否有效和报价得分。

## 项目介绍

投标报价模拟计算工具，用于模拟评标过程中的价格分计算。

### 功能说明

1. **评标模式**
   - 单低模式：基准价 = 最低有效报价
   - 双低模式：基准价 = 最低价×权重 + 平均价×权重

2. **限价设置**
   - 上限价：超出则废标
   - 下限价：低于则废标

3. **评分规则**
   - 可配置满分、上浮扣分系数、下浮扣分系数、最低得分
   - 得分公式：得分 = 满分 - |偏离率| × 扣分系数

4. **报价管理**
   - 支持添加/删除多家投标报价
   - 实时计算并显示有效性和得分排名

### 技术栈

- 前端：HTML + TailwindCSS + Alpine.js
- 部署：Docker + Nginx
