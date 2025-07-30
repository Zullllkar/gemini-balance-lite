# Gemini Balance Lite
# Gemini API 代理和负载均衡无服务器轻量版（边缘函数）

### 作者：技术爬爬虾
[B站](https://space.bilibili.com/316183842)，[Youtube](https://www.youtube.com/@Tech_Shrimp)，抖音，公众号 全网同名。转载请注明作者。

---

## 📑 目录

- [项目简介](#项目简介)
- [核心特性](#核心特性)
- [技术架构](#技术架构)
- [快速部署](#快速部署)
  - [Vercel部署(推荐)](#vercel部署推荐)
  - [Deno部署](#deno部署)
  - [Cloudflare Worker 部署](#cloudflare-worker-部署)
  - [Netlify部署](#netlify部署)
- [技术实现详解](#技术实现详解)
- [API 说明](#api-说明)
- [开发指南](#开发指南)
- [故障排除](#故障排除)
- [常见问题 (FAQ)](#常见问题-faq)
- [部署最佳实践](#部署最佳实践)
- [贡献指南](#贡献指南)

---


## 项目简介

Gemini Balance Lite 是一个轻量级的无服务器边缘函数项目，专为解决国内用户访问 Google Gemini API 的网络连通性问题而设计。本项目通过智能代理和负载均衡技术，为用户提供稳定、高效的 Gemini AI 服务访问体验。

### 🌟 核心特性

**1. 智能代理服务**
- 利用边缘计算网络，实现 Gemini API 的国内高速中转
- 支持流式（SSE）和非流式两种响应模式
- 自动处理网络请求头和响应头，确保最佳兼容性

**2. 负载均衡与容错**
- 支持多个 API Key 的智能轮询和随机分发
- 实现故障转移机制，提高服务可用性
- 自动检测和过滤无效的 API Key

**3. 多平台兼容性**
- 原生支持 Gemini API 格式
- 完整兼容 OpenAI API 格式，可直接替换现有 OpenAI 集成
- 支持主流 AI 客户端（如 Cherry Studio、ChatBox 等）

**4. 多云部署支持**
- Vercel（推荐）：国内访问需配置自定义域名
- Deno Deploy：提供全球 CDN 加速
- Cloudflare Workers：边缘计算性能优异
- Netlify：免费域名，国内可直连

### 🏗️ 技术架构

**边缘函数架构**
```
用户请求 → 边缘节点 → API Key 负载均衡 → Google Gemini API → 响应处理 → 用户
```

**核心模块**
- `handle_request.js`：主要请求处理逻辑
- `verify_keys.js`：API Key 验证和健康检查
- `openai.mjs`：OpenAI 格式适配器
- `deno_index.ts`：Deno 平台适配

**负载均衡算法**
- 随机选择策略：从可用 API Key 池中随机选择
- 实时健康检查：自动验证 API Key 有效性
- 故障隔离：自动排除失效的 API Key

### 🔒 安全特性

- **API Key 保护**：支持多 Key 隔离，降低单点故障风险
- **请求验证**：严格的请求格式验证和错误处理
- **日志脱敏**：敏感信息自动脱敏处理
- **跨域安全**：完整的 CORS 策略配置

### 📈 性能优势

- **边缘计算**：利用全球 CDN 网络，降低访问延迟
- **无服务器架构**：按需扩容，无需维护服务器
- **轻量级设计**：最小化资源占用，快速响应
- **智能缓存**：优化重复请求的处理效率

---

## 🚀 快速部署

选择以下任一平台快速部署你的 Gemini API 代理服务：

## Vercel部署(推荐)
[![Deploy to Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/tech-shrimp/gemini-balance-lite)


1. 点击部署按钮⬆️一键部署。
2. 国内使用需要配置自定义域名
    <details>
    <summary>配置自定义域名：</summary>

    ![image](/docs/images/5.png)
    </details>
3. 去[AIStudio](https://aistudio.google.com)申请一个免费Gemini API Key
<br>将API Key与自定义的域名填入AI客户端即可使用，如果有多个API Key用逗号分隔
    <details>
    <summary>以Cherry Studio为例：</summary>

    ![image](/docs/images/2.png)
    </details>




## Deno部署

1. [fork](https://github.com/tech-shrimp/gemini-balance-lite/fork)本项目
2. 登录/注册 https://dash.deno.com/
3. 创建项目 https://dash.deno.com/new_project
4. 选择此项目，填写项目名字（请仔细填写项目名字，关系到自动分配的域名）
5. Entrypoint 填写 `src/deno_index.ts` 其他字段留空 
   <details>
   <summary>如图</summary>
   
   ![image](/docs/images/3.png)
   </details>
6. 点击 <b>Deploy Project</b>
7. 部署成功后获得域名
8. 国内使用需要配置自定义域名
9. 去[AIStudio](https://aistudio.google.com)申请一个免费Gemini API Key
10. 将API Key与分配的域名填入AI客户端即可使用，如果有多个API Key用逗号分隔

<details>
<summary>以Cherry Studio为例：</summary>

![image](/docs/images/2.png)
</details>


## Cloudflare Worker 部署
[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/tech-shrimp/gemini-balance-lite)

0. CF Worker有可能会分配香港的CDN节点导致无法使用(Gemini不允许香港IP连接)
0. 广东地区不建议使用Cloudflare Worker 部署
1. 点击部署按钮
2. 登录Cloudflare账号
3. 链接Github账户，部署
4. 打开dash.cloudflare.com，查看部署后的worker
6. 国内使用需要配置自定义域名
<details>
<summary>配置自定义域名：</summary>

![image](/docs/images/4.png)
</details>


## Netlify部署
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/tech-shrimp/gemini-balance-lite)
<br>点击部署按钮，登录Github账户即可
<br>免费分配域名，国内可直连。
<br>但是不稳定

<details>
<summary>将分配的域名复制下来，如图：</summary>

![image](/docs/images/1.png)
</details>

去[AIStudio](https://aistudio.google.com)申请一个免费Gemini API Key
<br>将API Key与分配的域名填入AI客户端即可使用，如果有多个API Key用逗号分隔

<details>
<summary>以Cherry Studio为例：</summary>

![image](/docs/images/2.png)
</details>



## ❓ 常见问题 (FAQ)

### 部署相关

**Q: 推荐使用哪个平台部署？**
A: 
- **Vercel**（推荐）：部署简单，性能稳定，需配置自定义域名
- **Netlify**：国内直连，免费域名，但稳定性一般
- **Deno Deploy**：全球 CDN，性能优异
- **Cloudflare Workers**：边缘计算性能最佳，但可能遇到香港节点问题

**Q: 为什么需要配置自定义域名？**
A: 大部分平台的默认域名在国内访问受限，自定义域名可以绕过这些限制。

**Q: 如何配置多个 API Key？**
A: 在请求头中使用逗号分隔多个 Key：`key1,key2,key3`，系统会自动进行负载均衡。

### 使用相关

**Q: 支持哪些 Gemini 模型？**
A: 支持所有 Gemini 系列模型：
- `gemini-2.5-pro`：最强性能模型
- `gemini-2.5-flash`：快速响应模型
- `gemini-1.5-pro`：长上下文模型

**Q: 如何在现有项目中集成？**
A: 
1. 原生 Gemini 项目：直接替换 API 端点
2. OpenAI 项目：修改 `base_url` 为你的部署域名
3. 其他 AI 客户端：配置为 OpenAI 兼容模式

**Q: 有 API 调用限制吗？**
A: 本项目本身不设限制，限制来自：
- Google Gemini API 的官方限制
- 部署平台的请求限制（如 Vercel 的函数执行时间）

### 安全相关

**Q: API Key 会被记录吗？**
A: 代码中仅在控制台输出脱敏后的 Key（前7位+...+后7位），不会完整记录。

**Q: 如何保护 API Key 安全？**
A: 
- 使用多个 Key 分散风险
- 定期轮换 API Key
- 监控使用量，及时发现异常

## 🚀 部署最佳实践

### Vercel 部署优化

1. **自定义域名配置**
   - 选择国内可访问的域名服务商
   - 完成域名备案（如需要）
   - 配置 SSL 证书

2. **环境变量配置**
   ```bash
   # 在 Vercel 控制台配置环境变量
   GEMINI_API_KEYS=key1,key2,key3
   ```

3. **性能优化**
   - 启用 Edge Functions
   - 配置适当的缓存策略
   - 监控函数执行时间

### Cloudflare Workers 优化

1. **路由配置**
   ```toml
   # wrangler.toml
   [env.production]
   routes = [
     { pattern = "your-domain.com/*", zone_id = "your-zone-id" }
   ]
   ```

2. **KV 存储集成**
   ```javascript
   // 缓存常用响应
   const cache = await CACHE_KV.get(cacheKey);
   if (cache) return new Response(cache);
   ```

### Deno Deploy 优化

1. **环境配置**
   ```typescript
   // 环境变量访问
   const apiKeys = Deno.env.get("GEMINI_API_KEYS")?.split(",") || [];
   ```

2. **性能监控**
   - 配置 Deno 的内置监控
   - 设置告警规则

## 🤝 贡献指南

### 开发流程

1. **Fork 项目**
   ```bash
   git clone https://github.com/your-username/gemini-balance-lite.git
   ```

2. **创建功能分支**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **代码规范**
   - 使用 ESLint 检查代码质量
   - 遵循现有的代码风格
   - 添加必要的注释

4. **测试验证**
   ```bash
   # 本地测试
   npm run dev
   
   # 功能测试
   curl -X POST localhost:3000/verify \
     -H "x-goog-api-key: test-key"
   ```

5. **提交更改**
   ```bash
   git add .
   git commit -m "feat: add new feature"
   git push origin feature/your-feature-name
   ```

### 代码贡献规范

- **提交信息格式**：使用 [Conventional Commits](https://conventionalcommits.org/)
- **代码注释**：关键逻辑必须添加中文注释
- **错误处理**：完善的异常捕获和错误信息
- **向后兼容**：确保修改不破坏现有功能

### 问题反馈

- **Bug 报告**：提供详细的复现步骤和环境信息
- **功能建议**：说明使用场景和预期效果
- **文档改进**：指出不清楚或错误的文档内容

---

## 💝 支持项目

#### 如果这个项目对您有帮助，请帮忙点点关注点点赞，谢谢啦~

- **B站**：[https://space.bilibili.com/316183842](https://space.bilibili.com/316183842)
- **Youtube**: [https://www.youtube.com/@Tech_Shrimp](https://www.youtube.com/@Tech_Shrimp)
- **全网同名**：抖音、公众号

---

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🙏 致谢

- 感谢 [PublicAffairs/openai-gemini](https://github.com/PublicAffairs/openai-gemini) 项目提供的 OpenAI 兼容性代码
- 感谢所有贡献者和用户的支持与反馈
- 特别感谢边缘计算平台提供的免费服务额度

---

**项目维护者**：技术爬爬虾 | **最后更新**：2024年

## 🔧 技术实现详解

### 请求处理流程

1. **请求接收**：边缘函数接收用户请求
2. **路径解析**：根据请求路径判断处理方式
   - `/` : 服务状态检查
   - `/verify` : API Key 验证
   - `/chat/completions` : OpenAI 格式处理
   - 其他路径：原生 Gemini API 代理
3. **负载均衡**：从 API Key 池中智能选择可用密钥
4. **请求转发**：向 Google Gemini API 发送请求
5. **响应处理**：清理和优化响应头
6. **结果返回**：向用户返回处理后的响应

### 负载均衡实现

```javascript
// 核心负载均衡逻辑
const apiKeys = value.split(',').map(k => k.trim()).filter(k => k);
if (apiKeys.length > 0) {
  const selectedKey = apiKeys[Math.floor(Math.random() * apiKeys.length)];
  headers.set('x-goog-api-key', selectedKey);
}
```

### 容错机制

- **自动重试**：网络异常时自动切换到备用 API Key
- **健康检查**：实时监控 API Key 状态
- **错误隔离**：自动排除失效的 API Key
- **优雅降级**：服务异常时提供友好的错误信息

## 📋 API 说明

### 🔑 API Key 管理

**多 Key 配置格式**
```
YOUR_API_KEY_1,YOUR_API_KEY_2,YOUR_API_KEY_3
```

**Key 验证端点**
- 端点：`/verify`
- 方法：`POST`
- 功能：批量验证 API Key 有效性
- 响应：实时流式返回验证结果

### 🚀 Gemini 原生 API 代理

可以使用 Gemini 的原生 API 格式进行代理请求。
**Curl 示例:**
```bash
curl --location 'https://<YOUR_DEPLOYED_DOMAIN>/v1beta/models/gemini-2.5-pro:generateContent' \
--header 'Content-Type: application/json' \
--header 'x-goog-api-key: <YOUR_GEMINI_API_KEY_1>,<YOUR_GEMINI_API_KEY_2>' \
--data '{
    "contents": [
        {
         "role": "user",
         "parts": [
            {
               "text": "Hello"
            }
         ]
      }
    ]
}'
```
**Curl 示例:（流式）**
```bash
curl --location 'https://<YOUR_DEPLOYED_DOMAIN>/v1beta/models/gemini-2.5-pro:generateContent?alt=sse' \
--header 'Content-Type: application/json' \
--header 'x-goog-api-key: <YOUR_GEMINI_API_KEY_1>,<YOUR_GEMINI_API_KEY_2>' \
--data '{
    "contents": [
        {
         "role": "user",
         "parts": [
            {
               "text": "Hello"
            }
         ]
      }
    ]
}'
```
> 注意: 请将 `<YOUR_DEPLOYED_DOMAIN>` 替换为你的部署域名，并将 `<YOUR_GEMINI_API_KEY>` 替换为你的 Gemini API Ke，如果有多个用逗号分隔


### API Key 校验

可以通过向 `/verify` 端点发送请求来校验你的 API Key 是否有效。可以一次性校验多个 Key，用逗号隔开。

**Curl 示例:**
```bash
curl --location 'https://<YOUR_DEPLOYED_DOMAIN>/verify' \
--header 'x-goog-api-key: <YOUR_GEMINI_API_KEY_1>,<YOUR_GEMINI_API_KEY_2>'
```

### 🤖 OpenAI 格式兼容

本项目完全兼容 OpenAI API 格式，支持无缝迁移现有应用。

**支持的端点**
- `/chat/completions` - 聊天补全（推荐）
- `/completions` - 文本补全
- `/embeddings` - 文本嵌入
- `/models` - 模型列表

**模型映射关系**
- `gpt-3.5-turbo` → `gemini-2.5-flash`
- `gpt-4` → `gemini-2.5-pro`
- `text-embedding-ada-002` → `text-embedding-004`

**完整 OpenAI 格式示例**
```bash
curl --location 'https://<YOUR_DEPLOYED_DOMAIN>/chat/completions' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <YOUR_GEMINI_API_KEY>' \
--data '{
    "model": "gpt-3.5-turbo",
    "messages": [
        {
            "role": "system",
            "content": "你是一个有用的AI助手"
        },
        {
            "role": "user",
            "content": "请介绍一下人工智能的发展历史"
        }
    ],
    "stream": true,
    "temperature": 0.7,
    "max_tokens": 2000
}'
```

## 🛠️ 开发指南

### 本地开发环境

**环境要求**
- Node.js 18.0+
- npm 或 yarn
- Git

**快速启动**
```bash
# 克隆项目
git clone https://github.com/tech-shrimp/gemini-balance-lite.git
cd gemini-balance-lite

# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 或使用 Vercel CLI
npm install -g vercel
vercel dev
```

### 项目结构

```
gemini-balance-lite/
├── src/                    # 源代码目录
│   ├── index.js           # Cloudflare Workers 入口
│   ├── handle_request.js  # 核心请求处理逻辑
│   ├── verify_keys.js     # API Key 验证模块
│   ├── openai.mjs         # OpenAI 格式适配器
│   ├── deno_index.ts      # Deno Deploy 入口
│   └── edge-functions/    # Netlify 边缘函数
├── api/                   # Vercel API 路由
├── docs/                  # 文档和图片资源
├── package.json           # 项目配置
├── vercel.json           # Vercel 部署配置
├── wrangler.toml         # Cloudflare 配置
└── netlify.toml          # Netlify 配置
```

## 🔍 故障排除

### 常见问题

**1. API Key 无效**
- 症状：返回 401 或 403 错误
- 解决：使用 `/verify` 端点检查 API Key 状态
- 获取新 Key：[Google AI Studio](https://aistudio.google.com)

**2. 域名访问问题**
- 症状：国内无法访问默认域名
- 解决：配置自定义域名并完成备案
- 备选：使用 Netlify 部署（默认国内可访问）

**3. Cloudflare 香港节点问题**
- 症状：使用 CF Workers 时偶发连接失败
- 原因：Gemini API 不支持香港 IP
- 解决：切换到其他部署平台或配置 CF 路由规则

**4. 负载均衡失效**
- 症状：多个 API Key 但仍然失败
- 排查：检查 Key 格式（逗号分隔，无空格）
- 验证：使用 `/verify` 端点逐个测试

### 调试技巧

**1. 开启详细日志**
```javascript
// 在 handle_request.js 中添加
console.log('Request URL:', request.url);
console.log('Headers:', [...request.headers.entries()]);
```

**2. 测试 API 连通性**
```bash
# 测试代理服务状态
curl https://your-domain.com/

# 测试 API Key 验证
curl -X POST https://your-domain.com/verify \
  -H "x-goog-api-key: YOUR_API_KEY"
```

**3. 性能监控**
- 使用各平台提供的监控面板
- 设置告警规则监控错误率
- 定期检查 API Key 配额使用情况

