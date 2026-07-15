# Changelog

All notable changes to Self-Evolver will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [1.2.0] - 2026-07-15

### 🎯 核心升级：证据驱动执行

**灵感来源**：web-access 浏览哲学 — "像人一样思考，兼顾高效与适应性的完成任务"

#### 新增：五步执行法

| 步骤 | 说明 | 效果 |
|------|------|------|
| ① Define Goal | 明确成功标准 | 避免无效努力 |
| ② Choose Starting Point | 选最直接路径 | 减少试错 |
| ③ Execute & Validate | 每步结果是证据 | 动态调整 |
| ④ Adapt | 方向错立即调整 | 避免固执 |
| ⑤ Complete | 达到标准就停止 | 防止过度操作 |

#### 新增：站点模式积累

- 新增 `site-patterns/` 目录
- 按域名存储已验证的操作经验
- 跨 session 复用站点知识

#### 新增：Common Pitfalls

| 陷阱 | 教训 |
|------|------|
| Over-planning | 静态计划在动态目标前失效 |
| Assuming failure means wrong method | 有时目标本身不存在 |

#### 优化：Core Principle

> **像人一样思考** — 失败 ≠ 方法错，失败 = 重新评估

---

## [1.1.1] - 2026-07-15

### 🧠 灵感来源扩展

新增两个重要灵感来源：

| 灵感来源 | 贡献 |
|---------|------|
| **Anthropic Skills** | Pushy descriptions, Quick Reference, evals framework |
| **self-improving-proactive** | HOT/WARM/COLD memory tiers, evidence-based updates |

这使 self-evolver 从"单一技能"进化为"整合六大研究的统一框架"。

---

## [1.1.0] - 2026-07-15

### ✨ Anthropic Skills 特性整合

**灵感来源**：GitHub 87.3k Stars 的 Anthropic Skills 仓库

#### 新增功能

| 功能 | 说明 | 效果 |
|------|------|------|
| **Pushy 描述** | 主动触发语句 | 减少遗漏 |
| **Quick Reference** | 快速查询表格 | 提升效率 |
| **Common Pitfalls** | 常见错误警告 | 防止踩坑 |
| **Evals 框架** | 测试用例机制 | 验证有效性 |
| **Memory Stats** | 记忆统计 | 量化健康度 |

#### 新增文件

- `evals/evals.json` — 测试用例库

---

## [1.0.0] - 2026-07-14

### 🚀 首发版本

**定位**：面向 AI Agent 的自进化技能系统

#### 核心特性

| 特性 | 灵感来源 | 效果 |
|------|---------|------|
| 四种反射类型 | EmbodiSkill | 精准分类更新 |
| 棘轮评分 | 达尔文.skill | 防止退化 |
| 独立审计 | SkillEvolver | 验证有效性 |
| 执行失误检测 | EmbodiSkill | 区分失误与缺陷 |
| Body/Appdenix 区分 | EmbodiSkill | 保护有效偏好 |

#### 架构

```
~/self-evolver/
├── memory.md           # Body: 有效偏好
├── memory-appendix.md  # Appendix: 容易被忽视的提醒
├── preference-scores.md # 棘轮评分
├── corrections.md      # 纠正日志
├── reflection-log.md   # 反射分类
└── evals/             # 测试框架
```

---

## 灵感来源

| 研究 | 贡献 | 来源 |
|------|------|------|
| EmbodiSkill | 四种反射类型、Body/Appdenix 区分 | arXiv:2605.10332 |
| SkillEvolver | 部署观察、Silent-bypass 检测、审计机制 | arXiv:2605.10500 |
| 达尔文.skill | 棘轮机制、基于评分的保留 | GitHub |
| Anthropic Skills | Pushy 描述、快速参考、Evals 框架 | GitHub |
| self-improving-proactive | HOT/WARM/COLD 记忆层、证据驱动更新 | This system |
| web-access | 目标导向浏览哲学、证据驱动执行 | eze-is |

---

## 许可证

MIT License - 可以自由使用、修改、分发
