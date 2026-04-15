# DHF Skills

DHF RPA Skills for Claude Code - AI search and automation tasks.

## 安装方式

### 方式 1: 通过 /plugin 安装所有 skills

```bash
/plugin marketplace add your-username/rpa-skills
/plugin install dhf-skills@dhf-skills
```

### 方式 2: 通过 npm 按需安装单个 skill

```bash
# 安装单个 skill
npm install @dhf-skills/chatgpt-ai-task

# 或直接运行（无需安装）
npx @dhf-skills/chatgpt-ai-task -k "你好"
```

## 可用 Skills

### AI 搜索类
| Skill | 描述 | 安装命令 |
|-------|------|----------|
| chatgpt-ai-task | ChatGPT AI 搜索 | `npm install @dhf-skills/chatgpt-ai-task` |
| deepseek-ai-task | DeepSeek AI 搜索 | `npm install @dhf-skills/deepseek-ai-task` |
| gemini-ai-task | Gemini AI 搜索 | `npm install @dhf-skills/gemini-ai-task` |
| kimi-ai-task | Kimi AI 搜索 | `npm install @dhf-skills/kimi-ai-task` |
| qwen-ai-task | 通义千问 AI 搜索 | `npm install @dhf-skills/qwen-ai-task` |
| claude-ai-task | Claude AI 搜索 | `npm install @dhf-skills/claude-ai-task` |

### 搜索引擎类
| Skill | 描述 | 安装命令 |
|-------|------|----------|
| bing-search-task | 必应搜索 | `npm install @dhf-skills/bing-search-task` |
| google-search-task | 谷歌搜索 | `npm install @dhf-skills/google-search-task` |
| duckduckgo-search-task | DuckDuckGo 搜索 | `npm install @dhf-skills/duckduckgo-search-task` |
| naver-search-task | Naver 搜索 | `npm install @dhf-skills/naver-search-task` |
| yahoo-japan-search-task | 日本雅虎搜索 | `npm install @dhf-skills/yahoo-japan-search-task` |
| sogou-wechat-search-task | 搜狗微信搜索 | `npm install @dhf-skills/sogou-wechat-search-task` |

## 使用示例

### ChatGPT AI Task
```bash
# 基本调用
npx @dhf-skills/chatgpt-ai-task -k "你好"

# 带角色设定
npx @dhf-skills/chatgpt-ai-task -k "帮我写代码" -r "资深程序员"

# 完整参数
npx @dhf-skills/chatgpt-ai-task -k "分析代码" -r "专家" -o "markdown"
```

## 前置条件

1. **DHF Agent 已安装并运行**
   - 启动：`/dhf-install-agent --open`

2. **浏览器可用**
   - DHF Agent 会自动调用浏览器

## 开发

```bash
# 安装依赖
npm install

# 构建
npm run build

# 发布所有 skills
npm run publish:all
```

## 相关资源

- **DHF 官网：** https://dhf.pub
- **任务市场：** https://dhf.pub/nl/explore

## License

MIT
