# DHF RPA Skills 按需安装方案设计

## 概述

本方案实现 DHF RPA Skills 的**按需安装**功能，用户可以：
- 一次性克隆包含所有技能的仓库
- 通过交互式安装器选择需要的技能
- 只创建选中技能的链接和配置
- 统一管理和更新

## 核心设计理念

```
仓库层（统一管理）     安装层（按需选择）       使用层（Claude Code）
─────────────────    ─────────────────     ───────────────────
dhf-rpa-skills/   →   ~/.claude/skills/  →   /dhf-rpa-test-workflow
├── skills/            ├── test-workflow  →   /dhf-rpa-mail-sender
│   ├── test-workflow  └── mail-sender    →   ...
│   ├── mail-sender
│   └── web-scraper
└── install.js
```

## 目录结构

```
dhf-rpa-skills/                          # 主仓库
├── .claude-plugin/                      # 插件配置（动态生成）
│   └── marketplace.json                 # 只包含已安装的技能
│
├── skills/                              # 所有技能源码
│   ├── dhf-rpa-test-workflow/          # 技能 1：测试工作流
│   │   ├── SKILL.md                    # 技能文档
│   │   ├── package.json                # 技能配置
│   │   ├── scripts/
│   │   │   ├── cli.js                  # CLI 入口
│   │   │   └── main.ts                 # 主要逻辑
│   │   └── README.md
│   │
│   ├── dhf-rpa-mail-sender/            # 技能 2：邮件发送
│   │   ├── SKILL.md
│   │   ├── package.json
│   │   ├── scripts/
│   │   └── README.md
│   │
│   ├── dhf-rpa-web-scraper/            # 技能 3：网页抓取
│   │   ├── SKILL.md
│   │   ├── package.json
│   │   ├── scripts/
│   │   └── README.md
│   │
│   └── dhf-rpa-form-filler/            # 技能 4：表单填充
│       ├── SKILL.md
│       ├── package.json
│       ├── scripts/
│       └── README.md
│
├── packages/                           # 共享依赖包（可选）
│   ├── dhf-rpa-core/                   # 核心共享代码
│   │   └── package.json
│   └── dhf-rpa-utils/                  # 工具函数
│       └── package.json
│
├── scripts/                            # 仓库脚本
│   ├── install.js                      # 交互式安装器 ⭐
│   ├── uninstall.js                    # 卸载脚本
│   ├── update-marketplace.js           # 更新 marketplace.json
│   └── sync.js                         # 同步已安装技能
│
├── package.json                        # 根配置
│   ├── workspaces: ["packages/*"]     # npm workspaces
│   └── scripts:
│       ├── install: node scripts/install.js
│       ├── uninstall: node scripts/uninstall.js
│       └── sync: node scripts/sync.js
│
├── .gitignore
├── LICENSE
├── README.md                           # 仓库说明
├── INSTALL.md                          # 用户安装指南
├── DEVELOPMENT.md                      # 开发指南
└── SKILLS.md                           # 所有技能列表
```

## 交互式安装器设计

### 安装器功能

```javascript
// scripts/install.js

import inquirer from 'inquirer';
import fs from 'fs';
import path from 'path';
import { execSync } from 'child_process';

// 技能注册表
const SKILLS_REGISTRY = [
  {
    id: 'dhf-rpa-test-workflow',
    name: 'RPA 测试工作流',
    description: '测试 DHF Agent 基础连接和 RPA 操作',
    category: '测试',
    version: '1.0.0',
    dependencies: [],
    size: '2MB',
    tags: ['test', 'workflow', '基础']
  },
  {
    id: 'dhf-rpa-mail-sender',
    name: '邮件发送',
    description: '自动化发送邮件（163/Outlook/QQ）',
    category: '邮件',
    version: '1.0.0',
    dependencies: [],
    size: '1.5MB',
    tags: ['mail', 'email', '自动化']
  },
  {
    id: 'dhf-rpa-web-scraper',
    name: '网页抓取',
    description: '自动化抓取网页数据',
    category: '数据',
    version: '1.0.0',
    dependencies: ['dhf-rpa-core'],
    size: '2.5MB',
    tags: ['scrape', 'data', '爬虫']
  },
  {
    id: 'dhf-rpa-form-filler',
    name: '表单填充',
    description: '自动化填充和提交表单',
    category: '表单',
    version: '1.0.0',
    dependencies: ['dhf-rpa-core'],
    size: '2MB',
    tags: ['form', 'fill', '自动化']
  }
];

class SkillInstaller {
  constructor() {
    this.skillsDir = path.join(process.cwd(), 'skills');
    this.claudeSkillsDir = this.getClaudeSkillsDir();
    this.pluginDir = path.join(process.cwd(), '.claude-plugin');
    this.marketplaceFile = path.join(this.pluginDir, 'marketplace.json');
  }

  // 获取 Claude skills 目录
  getClaudeSkillsDir() {
    const home = process.env.HOME || process.env.USERPROFILE;
    return path.join(home, '.claude', 'skills');
  }

  // 获取已安装的技能
  getInstalledSkills() {
    if (!fs.existsSync(this.marketplaceFile)) {
      return [];
    }
    const marketplace = JSON.parse(fs.readFileSync(this.marketplaceFile, 'utf-8'));
    const plugin = marketplace.plugins.find(p => p.name === 'dhf-rpa-skills');
    return plugin ? plugin.skills.map(s => path.basename(s)) : [];
  }

  // 显示欢迎信息
  showWelcome() {
    console.log('\n╔══════════════════════════════════════════════════════════════╗');
    console.log('║          DHF RPA Skills - 交互式安装器                      ║');
    console.log('║          按需选择你需要的技能                               ║');
    console.log('╚══════════════════════════════════════════════════════════════╝\n');
  }

  // 按分类显示技能
  categorizeSkills() {
    const categories = {};
    SKILLS_REGISTRY.forEach(skill => {
      if (!categories[skill.category]) {
        categories[skill.category] = [];
      }
      categories[skill.category].push(skill);
    });
    return categories;
  }

  // 用户选择技能
  async selectSkills() {
    const categories = this.categorizeSkills();
    const installed = this.getInstalledSkills();

    const choices = [];
    for (const [category, skills] of Object.entries(categories)) {
      choices.push(new inquirer.Separator(`\n📦 ${category}`));
      skills.forEach(skill => {
        const isInstalled = installed.includes(skill.id);
        const status = isInstalled ? '✓' : ' ';
        choices.push({
          name: `${status} ${skill.name.padEnd(20)} - ${skill.description} [${skill.size}]`,
          value: skill.id,
          checked: isInstalled,
          short: skill.name
        });
      });
    }

    const answers = await inquirer.prompt([
      {
        type: 'checkbox',
        name: 'selected',
        message: '选择要安装的技能（空格选择，回车确认）:',
        choices: choices,
        pageSize: 20
      }
    ]);

    return answers.selected;
  }

  // 确认安装
  async confirmInstallation(selectedSkills) {
    const skills = SKILLS_REGISTRY.filter(s => selectedSkills.includes(s.id));
    const totalSize = skills.reduce((sum, s) => {
      const size = parseFloat(s.size);
      return sum + size;
    }, 0);

    console.log('\n📋 即将安装以下技能:\n');
    skills.forEach(skill => {
      console.log(`  • ${skill.name} (${skill.size})`);
    });
    console.log(`\n  总计: ${skills.length} 个技能，约 ${totalSize.toFixed(1)}MB\n`);

    const answers = await inquirer.prompt([
      {
        type: 'confirm',
        name: 'confirm',
        name: '确认安装这些技能？',
        default: true
      }
    ]);

    return answers.confirm;
  }

  // 安装单个技能
  installSkill(skillId) {
    const skillPath = path.join(this.skillsDir, skillId);
    const targetPath = path.join(this.claudeSkillsDir, skillId);

    // 检查技能目录是否存在
    if (!fs.existsSync(skillPath)) {
      throw new Error(`技能目录不存在: ${skillPath}`);
    }

    // 创建符号链接
    if (fs.existsSync(targetPath)) {
      fs.unlinkSync(targetPath);
    }
    fs.symlinkSync(skillPath, targetPath, 'junction');

    // 安装依赖
    if (fs.existsSync(path.join(skillPath, 'package.json'))) {
      process.chdir(skillPath);
      try {
        execSync('npm install', { stdio: 'inherit' });
      } catch (error) {
        console.warn(`  ⚠️  依赖安装失败: ${error.message}`);
      }
    }

    console.log(`  ✓ ${skillId}`);
  }

  // 更新 marketplace.json
  updateMarketplace(installedSkills) {
    const marketplace = {
      name: 'dhf-rpa-skills',
      owner: {
        name: 'DHF RPA Community',
        email: 'community@dhf.pub'
      },
      metadata: {
        description: 'DHF Agent RPA 自动化技能包',
        version: '1.0.0'
      },
      plugins: [
        {
          name: 'dhf-rpa-skills',
          description: 'DHF RPA 自动化技能',
          source: './',
          strict: true,
          skills: installedSkills.map(id => `./skills/${id}`)
        }
      ]
    };

    // 确保目录存在
    if (!fs.existsSync(this.pluginDir)) {
      fs.mkdirSync(this.pluginDir, { recursive: true });
    }

    fs.writeFileSync(this.marketplaceFile, JSON.stringify(marketplace, null, 2));
  }

  // 执行安装
  async install() {
    try {
      this.showWelcome();

      // 选择技能
      const selectedSkills = await this.selectSkills();
      if (selectedSkills.length === 0) {
        console.log('\n❌ 未选择任何技能，安装取消。');
        return;
      }

      // 确认安装
      const confirmed = await this.confirmInstallation(selectedSkills);
      if (!confirmed) {
        console.log('\n❌ 安装已取消。');
        return;
      }

      // 确保目标目录存在
      if (!fs.existsSync(this.claudeSkillsDir)) {
        fs.mkdirSync(this.claudeSkillsDir, { recursive: true });
      }

      // 安装技能
      console.log('\n🔧 开始安装...\n');
      for (const skillId of selectedSkills) {
        await this.installSkill(skillId);
      }

      // 更新 marketplace.json
      this.updateMarketplace(selectedSkills);

      console.log('\n✅ 安装完成！');
      console.log('\n📝 提示:');
      console.log('  1. 请重新启动 Claude Code');
      console.log('  2. 使用 /dhf-rpa-xxx 命令调用技能');
      console.log('  3. 运行 "npm run sync" 同步技能\n');

    } catch (error) {
      console.error('\n❌ 安装失败:', error.message);
      process.exit(1);
    }
  }
}

// 运行安装器
const installer = new SkillInstaller();
installer.install();
```

## 用户使用流程

### 1. 克隆仓库

```bash
# 克隆到本地（包含所有技能源码，但只安装需要的）
git clone https://github.com/xxx/dhf-rpa-skills.git
cd dhf-rpa-skills
```

### 2. 运行安装器

```bash
npm install              # 安装安装器依赖
npm run install          # 运行交互式安装器
```

### 3. 选择技能

```
╔══════════════════════════════════════════════════════════════╗
║          DHF RPA Skills - 交互式安装器                      ║
║          按需选择你需要的技能                               ║
╚══════════════════════════════════════════════════════════════╝

? 选择要安装的技能 (空格选择，回车确认):
❯ ◯ RPA 测试工作流       - 测试 DHF Agent 基础连接和 RPA 操作 [2MB]
  ◯ 邮件发送             - 自动化发送邮件（163/Outlook/QQ） [1.5MB]
  ✔ 网页抓取             - 自动化抓取网页数据 [2.5MB]
  ◯ 表单填充             - 自动化填充和提交表单 [2MB]
```

### 4. 确认安装

```
📋 即将安装以下技能:

  • RPA 测试工作流 (2MB)
  • 网页抓取 (2.5MB)

  总计: 2 个技能，约 4.5MB

? 确认安装这些技能？ (Y/n)
```

### 5. 安装完成

```
🔧 开始安装...

  ✓ dhf-rpa-test-workflow
  ✓ dhf-rpa-web-scraper

✅ 安装完成！

📝 提示:
  1. 请重新启动 Claude Code
  2. 使用 /dhf-rpa-xxx 命令调用技能
  3. 运行 "npm run sync" 同步技能
```

## 高级功能

### 卸载技能

```bash
npm run uninstall
```

### 同步技能

```bash
# 更新仓库后，同步已安装的技能到最新版本
npm run sync
```

### 查看已安装技能

```bash
npm run list
```

### 批量安装

```bash
# 命令行批量安装
npm run install -- --skills=test-workflow,mail-sender,web-scraper
```

## 技能开发规范

### 技能目录结构

```
skills/dhf-rpa-xxx/
├── SKILL.md              # 技能文档（必需）
├── package.json          # 技能配置（必需）
├── README.md             # 技能说明（必需）
├── scripts/              # 脚本目录（可选）
│   ├── cli.js           # CLI 入口
│   └── main.ts          # 主要逻辑
├── prompts/              # 提示词模板（可选）
└── references/           # 参考资料（可选）
```

### SKILL.md 格式

```markdown
---
name: dhf-rpa-test-workflow
description: 测试 DHF Agent 基础连接和 RPA 操作
version: 1.0.0
author: DHF RPA Community
license: MIT
tags: [test, workflow, 基础]
category: 测试
dependencies: []
---

# DHF RPA 测试工作流

## 功能
...
```

### package.json 配置

```json
{
  "name": "dhf-rpa-test-workflow",
  "version": "1.0.0",
  "description": "测试 DHF Agent 基础连接和 RPA 操作",
  "type": "module",
  "main": "scripts/main.js",
  "bin": {
    "dhf-rpa-test": "scripts/cli.js"
  },
  "scripts": {
    "start": "node scripts/cli.js",
    "test": "node scripts/cli.js --check"
  },
  "keywords": ["dhf", "rpa", "test"],
  "author": "DHF RPA Community",
  "license": "MIT",
  "dependencies": {
    "@modelcontextprotocol/sdk": "^1.0.4"
  },
  "engines": {
    "node": ">=18.0.0"
  }
}
```

## 持续集成

### GitHub Actions 自动更新

```yaml
# .github/workflows/sync.yml
name: Sync Skills

on:
  push:
    branches: [main]
  schedule:
    - cron: '0 0 * * *'  # 每天同步

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Update skills registry
        run: node scripts/update-registry.js
      - name: Commit changes
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "GitHub Action"
          git add .
          git commit -m "chore: 更新技能注册表" || exit 0
          git push
```

## 发布流程

### 1. 更新技能注册表

```bash
node scripts/update-registry.js
```

### 2. 创建 GitHub Release

```bash
npm version patch  # 或 minor, major
git push --follow-tags
gh release create $(node -p "require('./package.json').version")
```

### 3. 发布到市场（可选）

```bash
npm run publish:marketplace
```

## 文件清单

| 文件 | 说明 |
|------|------|
| `README.md` | 仓库说明 |
| `INSTALL.md` | 用户安装指南 |
| `DEVELOPMENT.md` | 开发指南 |
| `SKILLS.md` | 所有技能列表 |
| `.claude-plugin/marketplace.json` | 插件配置（动态生成） |
| `scripts/install.js` | 交互式安装器 |
| `scripts/uninstall.js` | 卸载脚本 |
| `scripts/sync.js` | 同步脚本 |
| `scripts/update-registry.js` | 更新技能注册表 |
| `scripts/list.js` | 列出已安装技能 |
| `package.json` | 根配置 |

## 与 baoyu-skills 的对比

| 特性 | baoyu-skills | dhf-rpa-skills (本方案) |
|------|-------------|------------------------|
| 按需安装 | ❌ | ✅ |
| 统一仓库 | ✅ | ✅ |
| 交互式安装 | ❌ | ✅ |
| 符号链接 | ❌ | ✅ |
| 动态配置 | ❌ | ✅ |
| 用户友好 | 中 | 高 |

## 总结

本方案通过以下方式实现按需安装：

1. **符号链接** - 只创建选中技能的链接，不复制文件
2. **动态配置** - 根据已安装技能生成 marketplace.json
3. **交互式选择** - 用户可以自由选择需要的技能
4. **统一管理** - 所有技能在一个仓库中，易于维护

这种方案既保持了 Monorepo 的优势，又实现了按需安装的灵活性。
