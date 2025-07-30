# 开发文档

## 环境配置

### 前置要求
- Node.js 18.0+
- Git
- Vercel CLI (可选)
- Wrangler CLI (Cloudflare 开发)

### 快速开始

```bash
# 克隆项目
git clone https://github.com/tech-shrimp/gemini-balance-lite.git
cd gemini-balance-lite

# 本地开发 (使用 Vercel)
npm install -g vercel
vercel dev

# 或者使用 Node.js 本地运行
node src/index.js
```

## 代码结构说明

### 主要文件

- `src/index.js` - Cloudflare Workers 入口点
- `src/handle_request.js` - 核心请求处理逻辑
- `src/verify_keys.js` - API Key 验证功能
- `src/openai.mjs` - OpenAI 格式兼容层
- `src/deno_index.ts` - Deno Deploy 入口点

### 核心功能模块

#### 1. 请求路由
```javascript
// handle_request.js 中的路由逻辑
if (pathname === '/') {
  // 状态检查
} else if (pathname === '/verify') {
  // API Key 验证
} else if (pathname.endsWith('/chat/completions')) {
  // OpenAI 格式处理
} else {
  // Gemini 原生 API 代理
}
```

#### 2. 负载均衡
```javascript
// 多 API Key 负载均衡实现
const apiKeys = value.split(',').map(k => k.trim()).filter(k => k);
const selectedKey = apiKeys[Math.floor(Math.random() * apiKeys.length)];
```

#### 3. 错误处理
- 网络请求异常处理
- API Key 验证失败处理
- 响应格式错误处理

## 调试指南

### 本地调试

1. **启用详细日志**
```javascript
console.log('Request URL:', request.url);
console.log('Selected API Key:', selectedKey);
```

2. **测试不同端点**
```bash
# 测试状态
curl http://localhost:3000/

# 测试验证
curl -X POST http://localhost:3000/verify \
  -H "x-goog-api-key: YOUR_TEST_KEY"

# 测试 OpenAI 格式
curl -X POST http://localhost:3000/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TEST_KEY" \
  -d '{"model":"gpt-3.5-turbo","messages":[{"role":"user","content":"test"}]}'
```

### 生产环境调试

各平台都提供了日志查看功能：
- **Vercel**: Functions 标签页查看实时日志
- **Cloudflare**: Workers 控制台的 Logs 部分
- **Deno Deploy**: Deploy 页面的 Logs 标签
- **Netlify**: Functions 标签页查看执行日志

## 性能优化

### 1. 减少冷启动时间
- 最小化依赖包大小
- 避免复杂的初始化逻辑
- 使用边缘函数而不是传统服务器

### 2. 请求优化
- 实现请求缓存（适当场景）
- 优化请求头处理
- 减少不必要的数据传输

### 3. 错误处理优化
- 快速失败机制
- 智能重试策略
- 降级处理方案

## 测试策略

### 单元测试
```bash
# 如果需要添加测试
npm install --save-dev jest
npm test
```

### 集成测试
- 测试各个部署平台的兼容性
- 验证 API Key 负载均衡功能
- 确认 OpenAI 格式兼容性

### 压力测试
```bash
# 使用 ab 进行简单压力测试
ab -n 1000 -c 10 http://your-domain.com/
```

## 部署流程

### 自动化部署
大部分平台支持 Git 集成，推送代码即可自动部署：

1. **Vercel**: 连接 GitHub 仓库，自动部署
2. **Netlify**: 同样支持 Git 集成
3. **Cloudflare**: 使用 Wrangler CLI 部署
4. **Deno Deploy**: 支持 GitHub Actions 自动部署

### 手动部署
```bash
# Cloudflare Workers
wrangler publish

# Vercel
vercel --prod

# Deno Deploy
deployctl deploy --project=your-project src/deno_index.ts
```

## 安全考虑

### API Key 安全
- 永远不要在代码中硬编码 API Key
- 使用环境变量或平台密钥管理
- 定期轮换 API Key

### 请求验证
- 验证请求格式
- 限制请求大小
- 防止 SSRF 攻击

### 日志安全
- API Key 脱敏处理
- 避免记录敏感用户数据
- 设置适当的日志保留期

## 贡献指南

### 提交规范
- 使用 [Conventional Commits](https://conventionalcommits.org/) 格式
- 提供清晰的变更说明
- 包含必要的测试

### 代码风格
- 使用 ESLint 检查代码质量
- 遵循现有的缩进和命名规范
- 添加适当的注释

### Pull Request 流程
1. Fork 项目
2. 创建功能分支
3. 完成开发和测试
4. 提交 Pull Request
5. 等待代码审查

## 常见开发问题

### Q: 本地开发时如何模拟不同的部署环境？
A: 可以通过环境变量切换不同的配置，或者使用各平台提供的本地开发工具。

### Q: 如何测试负载均衡功能？
A: 配置多个测试 API Key，通过日志观察是否轮询选择。

### Q: 如何处理平台特定的差异？
A: 通过入口文件适配不同平台的 API，核心逻辑保持一致。