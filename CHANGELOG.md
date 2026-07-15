# Changelog

All notable changes to Self-Evolver will be documented in this file.

## [1.3.0] - 2026-07-15

### 改进后的优势

---

### 一、Token 效率的质的飞跃

| 场景 | 改进前 | 改进后 | 节省 |
|:-----|:-------|:-------|:-----|
| 启动注入 | 全文 dump（~2,000 chars） | 索引摘要（~500 chars） | **~75%** |
| 大 memory（8+条目） | 全部占 prompt budget | 索引 + 按需 fetch 1-2 条 | **~80%** |
| 无提示缓存的模型 | 每次请求都送 memory 全文 | 固定少量索引 | 显著 |

**核心机制：** 之前 memory 全文无论相关与否都在 system prompt 里占着 token。现在只占索引的 4-5 行，Agent 用 `memory(action=read)` 按需获取。如果当前 session 跟 memory 无关（比如纯聊天），Agent 可以不读——0 浪费。

---

### 二、让 Agent 自己做决策，而非被动接受

之前是"系统猜测什么相关就塞什么"——本质上是传统 RAG 的"推送"模式。改进后是 **拉取（pull）模式**：

```
改进前：
System → [判断相关性] → 把N条记忆塞进prompt → Agent被动接收

改进后：
System → [展示索引轻量摘要] → Agent看到"有什么"、"每条花多少token"
→ Agent根据当前任务判断是否值得取详情
→ Agent主动调用 memory(read) 获取全文
```

这不只是 token 省了——**检索决策权回到了 Agent 手里**。Agent 比系统更清楚当前任务需要什么记忆。

---

### 三、三层渐进式架构落地

claude-mem 最值钱的设计不是"怎么存"，而是**"怎么取"**——三层渐进式：

| 层 | 改进前 | 改进后 |
|:---|:-------|:-------|
| **层1 — 索引** | ❌ memory 直接 dump 全文 | ✅ 常驻 system prompt 的索引摘要（~500 chars） |
| **层2 — 上下文** | ❌ 缺少 ROI 提示 | ✅ 工具描述引导 Agent 从 discover 开始 |
| **层3 — 详情** | ✅ session_search 已有 | ✅ 新增 memory(read) 统一入口 |

之前有层3但没有层1和层2的引导。现在三层都有了，Agent 在**直觉上**会先看索引、再决定 fetch，而不是盲搜。

---

### 四、实际场景对比

**场景：用户说"开始写第一章"**

- **改进前**：system prompt 里带 2,000 chars 的 memory 全文（包含完全无关的工具配置）+ 论文信息。Agent 浪费 tokens 处理无用信息。

- **改进后**：system prompt 里只看到：
  ```
  📝 ~28 tok | 论文项目"历史的天空"...
  📝 ~81 tok | 论文"历史的天空"理论框架定版...
  📝 ~28 tok | MCP: scholarmcp + semantic-scholar + zotero...
  📝 ~45 tok | Memory 注入改为 Progressive Disclosure 索引...
  ```
  一目了然哪些跟第一章写作有关（前两条），无关的跳过。Agent 3 秒内决定不读无关条目。

---

### 新增功能

| 功能 | 说明 |
|------|------|
| **observations/ 目录** | 原始观察记录存储，压缩后写入 memory |
| **Token 感知检索** | 显示加载消耗，用户可控上下文大小 |
| **Layer 2 Timeline** | 获取时间线上下文（~200-300 tokens） |

---

## [1.2.0] - 2026-07-15

### 核心升级：证据驱动执行

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

---

## [1.1.1] - 2026-07-15

### 灵感来源扩展

新增两个重要灵感来源：

| 灵感来源 | 贡献 |
|---------|------|
| **Anthropic Skills** | Pushy descriptions, Quick Reference, evals framework |
| **self-improving-proactive** | HOT/WARM/COLD memory tiers, evidence-based updates |

---

## [1.1.0] - 2026-07-15

### Anthropic Skills 特性整合

| 功能 | 说明 | 效果 |
|------|------|------|
| **Pushy 描述** | 主动触发语句 | 减少遗漏 |
| **Quick Reference** | 快速查询表格 | 提升效率 |
| **Common Pitfalls** | 常见错误警告 | 防止踩坑 |
| **Evals 框架** | 测试用例机制 | 验证有效性 |
| **Memory Stats** | 记忆统计 | 量化健康度 |

---

## [1.0.0] - 2026-07-14

### 首发版本

**定位**：面向 AI Agent 的自进化技能系统

#### 核心架构

```
~/self-evolver/
├── memory.md              # Body: 有效偏好
├── memory-appendix.md     # Appendix: 容易被忽视的提醒
├── preference-scores.md  # 棘轮评分
├── corrections.md         # 纠正日志
├── reflection-log.md      # 反射分类
└── evals/               # 测试框架
```

#### 核心特性

| 特性 | 灵感来源 | 效果 |
|------|---------|------|
| 四种反射类型 | EmbodiSkill | 精准分类更新 |
| 棘轮评分 | 达尔文.skill | 防止退化 |
| 独立审计 | SkillEvolver | 验证有效性 |
| 执行失误检测 | EmbodiSkill | 区分失误与缺陷 |
| Body/Appendix 区分 | EmbodiSkill | 保护有效偏好 |

---

## 灵感来源

| 研究 | 贡献 | 来源 |
|------|------|------|
| EmbodiSkill | 四种反射类型、Body/Appendix 区分 | arXiv:2605.10332 |
| SkillEvolver | 部署观察、Silent-bypass 检测、审计机制 | arXiv:2605.10500 |
| 达尔文.skill | 棘轮机制、基于评分的保留 | GitHub |
| Anthropic Skills | Pushy 描述、快速参考、Evals 框架 | GitHub |
| self-improving-proactive | HOT/WARM/COLD 记忆层、证据驱动更新 | This system |
| web-access | 目标导向浏览哲学、证据驱动执行 | eze-is |
| claude-mem | 3层渐进披露、Token 感知检索、拉取模式 | GitHub 87.3k ⭐ |

---

## 许可证

MIT License
