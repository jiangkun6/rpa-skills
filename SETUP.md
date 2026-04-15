# DHF Skills 项目设置指南

## 发布方式说明

这个项目支持两种发布方式，用户可以选择不同的安装方式：

| 发布方式 | 发布目标 | 用户安装命令 | 说明 |
|----------|----------|--------------|------|
| **npm 发布** | npm registry | `npm install @dhf-skills/chatgpt-ai-task` | 用户按需安装单个 skill |
| **GitHub 发布** | GitHub 仓库 | `/plugin marketplace add your-username/rpa-skills` | 用户用 /plugin 一次性安装所有 skills |

### 简单来说

1. **发布到 npm** → 用户可以按需安装单个 skill
2. **发布到 GitHub** → 用户可以用 `/plugin` 一次性安装所有 skills
3. **两者都发** → 用户两种方式都能用 ✅ **推荐**

---

## 你需要做的事情

```
1. 注册 npm 账号（如没有）
   └── https://www.npmjs.com/ 注册 → npm login

2. 创建 GitHub 仓库
   └── https://github.com/new

3. 修改配置中的 your-username
   └── 所有 package.json 和 marketplace.json

4. 发布
   ├── git push → GitHub
   └── npm publish → npm
```

### 两种方式是独立的

你可以只选择其中一种：
- 只发布 npm → 用户只能单个安装
- 只发 GitHub → 用户只能 /plugin 安装全部
- 两者都发 → 用户两种方式都能用

---

## 项目目录结构

```
D:\wwwroot\rpa-skills\
├── skills/                          # 所有 skills (npm workspaces)
│   ├── chatgpt-ai-task/             # @dhf-skills/chatgpt-ai-task
│   │   ├── package.json             # npm 包配置
│   │   ├── SKILL.md                 # 技能定义文档
│   │   └── scripts/
│   │       └── main.ts              # TypeScript 脚本
│   ├── deepseek-ai-task/            # @dhf-skills/deepseek-ai-task
│   ├── gemini-ai-task/              # @dhf-skills/gemini-ai-task
│   ├── kimi-ai-task/                # @dhf-skills/kimi-ai-task
│   ├── qwen-ai-task/                # @dhf-skills/qwen-ai-task
│   ├── claude-ai-task/              # @dhf-skills/claude-ai-task
│   ├── bing-search-task/            # @dhf-skills/bing-search-task
│   ├── google-search-task/          # @dhf-skills/google-search-task
│   ├── duckduckgo-search-task/      # @dhf-skills/duckduckgo-search-task
│   ├── naver-search-task/           # @dhf-skills/naver-search-task
│   ├── yahoo-japan-search-task/     # @dhf-skills/yahoo-japan-search-task
│   ├── sogou-wechat-search-task/    # @dhf-skills/sogou-wechat-search-task
│   ├── ctrip-itinerary-workflow/    # (暂未配置 package.json)
│   ├── dhf-install-agent/           # (暂未配置 package.json)
│   └── dhf-qq-mail-task/            # (暂未配置 package.json)
├── .claude-plugin/
│   └── marketplace.json             # Claude Code 插件配置
├── .claude/skills/                  # 原位置 (已复制到 skills/)
├── package.json                     # 根配置 (workspaces)
├── tsconfig.json                    # TypeScript 配置
├── README.md                        # 项目说明
├── PUBLISH.md                       # 发布指南
└── SETUP.md                         # 本文件
```

---

## 用户使用方式

### 方式 1: npm 按需安装单个 skill

```bash
# 安装单个 skill
npm install @dhf-skills/chatgpt-ai-task

# 或直接运行（无需安装）
npx @dhf-skills/chatgpt-ai-task -k "你好"

# 带参数
npx @dhf-skills/chatgpt-ai-task -k "帮我写代码" -r "资深程序员" -o "markdown"
```

### 方式 2: /plugin 安装所有 skills

```bash
# 添加到 marketplace
/plugin marketplace add your-username/rpa-skills

# 安装插件
/plugin install dhf-skills@dhf-skills
```

---

## 详细步骤

### 步骤 1: 注册 npm 账号（如未注册）

1. 访问 https://www.npmjs.com/ 注册账号
2. 验证邮箱
3. 本地登录：`npm login`

### 步骤 2: 创建 GitHub 仓库

1. 访问 https://github.com/new
2. 创建名为 `rpa-skills` 的仓库
3. 记住你的 GitHub 用户名

### 步骤 3: 修改配置信息

**需要修改所有 `skills/*/package.json` 中的仓库信息：**

将以下内容中的 `your-username` 替换为你的 GitHub 用户名：
```json
"repository": {
  "type": "git",
  "url": "https://github.com/your-username/rpa-skills.git",
  "directory": "skills/chatgpt-ai-task"
}
```

**修改 `.claude-plugin/marketplace.json` 中的作者信息：**
```json
{
  "owner": {
    "name": "Your Name",
    "email": "your@email.com"
  }
}
```

### 步骤 4: 推送到 GitHub

```bash
cd D:\wwwroot\rpa-skills
git init
git add .
git commit -m "Initial commit: DHF RPA Skills"
git branch -M main
git remote add origin https://github.com/your-username/rpa-skills.git
git push -u origin main
```

### 步骤 5: 发布到 npm

**发布单个 skill：**
```bash
cd skills/chatgpt-ai-task
npm publish --access public
```

**发布所有已配置的 skills：**
```bash
# Windows PowerShell
cd D:\wwwroot\rpa-skills
Get-ChildItem -Path skills -Directory | ForEach-Object {
    $pkgPath = Join-Path $_.FullName "package.json"
    if (Test-Path $pkgPath) {
        Write-Host "Publishing $($_.Name)..."
        cd $_.FullName
        npm publish --access public
        cd ..
    }
}
```

---

## 验证

```bash
# 验证 npm 发布
npm view @dhf-skills/chatgpt-ai-task

# 测试 npx 运行
npx @dhf-skills/chatgpt-ai-task -k "测试"

# 测试 /plugin 安装（需要先发布到 GitHub）
/plugin marketplace add your-username/rpa-skills
/plugin install dhf-skills@dhf-skills
```

---

## 可用 Skills 列表

| 包名 | 描述 | 状态 |
|------|------|------|
| @dhf-skills/chatgpt-ai-task | ChatGPT AI 搜索 | ✅ 已配置 |
| @dhf-skills/deepseek-ai-task | DeepSeek AI 搜索 | ✅ 已配置 |
| @dhf-skills/gemini-ai-task | Gemini AI 搜索 | ✅ 已配置 |
| @dhf-skills/kimi-ai-task | Kimi AI 搜索 | ✅ 已配置 |
| @dhf-skills/qwen-ai-task | 通义千问 AI 搜索 | ✅ 已配置 |
| @dhf-skills/claude-ai-task | Claude AI 搜索 | ✅ 已配置 |
| @dhf-skills/bing-search-task | 必应搜索 | ✅ 已配置 |
| @dhf-skills/google-search-task | 谷歌搜索 | ✅ 已配置 |
| @dhf-skills/duckduckgo-search-task | DuckDuckGo 搜索 | ✅ 已配置 |
| @dhf-skills/naver-search-task | Naver 搜索 | ✅ 已配置 |
| @dhf-skills/yahoo-japan-search-task | 日本雅虎搜索 | ✅ 已配置 |
| @dhf-skills/sogou-wechat-search-task | 搜狗微信搜索 | ✅ 已配置 |
| ctrip-itinerary-workflow | 携程行程工作流 | ⏳ 待配置 |
| dhf-install-agent | DHF Agent 安装 | ⏳ 待配置 |
| dhf-qq-mail-task | QQ 邮件发送 | ⏳ 待配置 |

---

## 常用命令

```bash
# 安装依赖
npm install

# 构建（如果需要编译）
npm run build

# 发布所有 skills
npm run publish:all

# 查看已发布的包
npm view @dhf-skills/chatgpt-ai-task

# 删除已发布的包（24小时内）
npm unpublish @dhf-skills/chatgpt-ai-task@1.0.0
```
