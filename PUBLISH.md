# 发布指南

## 发布到 npm

### 前置条件

1. **注册 npm 账号**
   - 访问 https://www.npmjs.com/ 注册账号
   - 验证邮箱

2. **登录 npm**
   ```bash
   npm login
   ```

### 发布单个 skill

```bash
# 进入 skill 目录
cd skills/chatgpt-ai-task

# 发布
npm publish --access public
```

### 发布所有 skills

```bash
# 从根目录发布所有 skills
npm run publish:all
```

### 验证发布

```bash
# 查看 skill 信息
npm view @dhf-skills/chatgpt-ai-task

# 测试 npx 运行
npx @dhf-skills/chatgpt-ai-task -k "你好"
```

---

## 发布到 GitHub（用于 /plugin 安装）

### 步骤

1. **创建 GitHub 仓库**
   - 访问 https://github.com/new
   - 创建名为 `rpa-skills` 的仓库

2. **推送代码**

   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/jiangkun6/rpa-skills.git
   git push -u origin main
   ```

3. **用户安装方式**

   ```bash
   # 添加到 marketplace
   /plugin marketplace add jiangkun6/rpa-skills

   # 安装插件
   /plugin install dhf-skills@dhf-skills
   ```

---

## 配置说明

### 修改 package.json 中的仓库信息

将以下内容中的 `your-username` 替换为你的 GitHub 用户名：

```json
"repository": {
  "type": "git",
  "url": "https://github.com/your-username/rpa-skills.git",
  "directory": "skills/chatgpt-ai-task"
}
```

### 修改作者信息

在 `.claude-plugin/marketplace.json` 中修改：

```json
{
  "owner": {
    "name": "Your Name",
    "email": "your@email.com"
  }
}
```

---

## 版本管理

### 更新版本

```bash
# 更新单个 skill 版本
cd skills/chatgpt-ai-task
npm version patch  # 1.0.0 -> 1.0.1
npm version minor  # 1.0.0 -> 1.1.0
npm version major  # 1.0.0 -> 2.0.0

# 重新发布
npm publish --access public
```

### 批量更新版本

```bash
# 使用 npm workspaces
npm version patch --workspaces
```

---

## 注意事项

1. **包名唯一性**：确保 `@dhf-skills/` 前缀下的包名未被占用
2. **版本号**：遵循语义化版本规范 (Semantic Versioning)
3. **文件选择**：package.json 中的 `files` 字段指定要发布的文件
4. **access public**：使用 `--access public` 确保包为公开可访问

---

## 常见问题

### Q: 发布失败提示包名已存在？

A: 包名可能已被占用，可以尝试更换名称或联系原所有者。

### Q: 如何删除已发布的包？

A:

```bash
# 删除特定版本（24小时内）
npm unpublish @dhf-skills/chatgpt-ai-task@1.0.0

# 删除整个包（需要等待72小时）
npm unpublish @dhf-skills/chatgpt-ai-task --force
```

### Q: npx 运行失败？

A: 确保：

1. package.json 中 `bin` 字段配置正确
2. 脚本文件有可执行权限
3. bun 运行时已安装（如果使用 .ts 文件）
