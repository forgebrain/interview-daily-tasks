# [面试备考] Day 2/56 · W1D2 · 2026-07-02 — Sliding Window + Two-Tower 召回基础 + 故事精修(AI Search) + 条件概率/Bayes

> **Day 2/56 · Week 1 Day 2（新内容日 · 四轨齐发）**
> 今日主题一句话：装完框架（Day 1）后，今天开始「往框架里灌第一批高频料」——Sliding Window 是 Meta 数组/字符串题（占其 MLE coding **38%**）的核心武器；SD 进入推荐系统的地基 two-tower；行为把 Day 1 的故事矩阵里第一个 hook（AI Search）打磨成三版本；数学从计数进到条件概率/Bayes。
> 预计总耗时：**2.5–3.5h**（Coding 70min · SD 45min · Behavioral 45min · Math 30min · 复盘 15min）

---

## 🔥 今日先看：2026 面试形式最新变化（今天 Web 检索到的）
- **Meta 已在 loop 里加了独立的 "AI-assisted coding" 轮**（2025 起）：给你一段真实代码库，让你用 LLM debug + 扩展它。这不是刷 LeetCode 能覆盖的——它考的是「你能不能像 Senior 一样把模糊需求拆成 prompt、审查 AI 产出、快速定位 bug」。**今天写 Sliding Window 时刻意练一件事：先把思路口述清楚再动手**，这套「先规格化再实现」的肌肉正是 AI 轮的核心。
- Meta MLE 电话/onsite coding：**45min 至少 AC 2 题**，数组/字符串占 38%（最高频类目），Minimum Window Substring 是点名常青题。
> 来源：[Exponent – Meta MLE Interview Guide 2026](https://www.tryexponent.com/guides/meta-machine-learning-engineer-interview) · [Prepfully – Meta MLE 2026](https://prepfully.com/interview-guides/meta-machine-learning-engineer)

---

## 1️⃣ Coding 轨 — Pattern: Sliding Window（滑动窗口）

### ① 今天做什么
- **LC 3. Longest Substring Without Repeating Characters**（Medium，热身+必刷，20min）→ [leetcode](https://leetcode.com/problems/longest-substring-without-repeating-characters/) · [neetcode](https://neetcode.io/problems/longest-substring-without-repeating-characters)
- **★ LC 76. Minimum Window Substring**（Hard，Meta 点名题，必须 30–35min 内独立 AC）→ [leetcode](https://leetcode.com/problems/minimum-window-substring/) · [neetcode](https://neetcode.io/problems/minimum-window-with-characters)
- 有余力再扫 **LC 209. Minimum Size Subarray Sum**（变长窗口热身）+ **LC 239. Sliding Window Maximum**（定长窗口 + 单调双端队列，读题想 5min，明天/后天可正式写）→ [LC209](https://leetcode.com/problems/minimum-size-subarray-sum/) · [LC239](https://leetcode.com/problems/sliding-window-maximum/)

### ② 在计划中的位置 / 为什么现在练
Sliding Window 是 W1「数组基础四件套」第二件，紧接 Day 1 的 Two Pointers——它就是「双指针 + 维护一个窗口内状态」的进化版。掌握它你就拿下了 Meta coding 最高频类目里的半壁江山。76 是**变长窗口 + 计数哈希**的天花板题，练透它，其他 sliding window 都是它的降级版。

### ③ 关键思路 / senior 要点 / 常见坑
- **滑动窗口两大类，先在心里分类再动手（这就是 UMPIRE 的 Match）**：
  - **变长窗口**（3、76、209）：右指针扩张吃字符，违反约束时左指针收缩。核心是「什么时候收缩」。
  - **定长窗口**（239）：窗口大小固定 k，右进左出，配单调队列 O(n)。
- **LC 3 思路链**：右指针遍历，用 `set`/`map` 记录窗口内字符；遇到重复就收缩左指针直到无重复；每步更新 max 长度。O(n)/O(min(n,charset))。进阶：用「last seen index」把左指针一步跳到位，避免逐格收缩。
- **LC 76 思路链（think aloud，别直接写）**：
  1. brute force：枚举所有子串 O(n²) 检查 → 先说，再说「用滑动窗口降到 O(n)」。
  2. 用 `need`（t 的字符计数）+ `window`（当前窗口计数）+ `formed`（已满足的字符种类数）。
  3. 右指针扩张，当某字符 `window[c]==need[c]` 时 `formed++`；当 `formed==required`（t 的不同字符数）时窗口「合法」，此时**收缩左指针**求最短，收缩到不合法为止。
  4. 全程记录最小窗口的 (start, len)。
- 复杂度：时间 O(|s|+|t|)，空间 O(charset)。
- **Edge cases 清单**：t 比 s 长 / t 有重复字符（`need` 必须用计数而非 set！这是最常见的 bug）/ s 或 t 为空 / 无解返回 `""` / 大小写敏感 / 整个 s 才是答案。
- **Senior 沟通话术（UMPIRE，复用 Day 1 的模板）**：Understand（问：字符集范围？大小写？t 有重复吗？要返回子串还是长度？）→ Match（"变长滑动窗口 + 计数哈希"）→ Plan（口述 formed/required 收缩逻辑再写）→ Implement（边写边说）→ Review（手动 trace `s="ADOBECODEBANC", t="ABC"` → 期望 `"BANC"`）→ Evaluate（复杂度 + follow-up：若 t 超大 / 流式 s 怎么办）。

### ④ 验收标准（怎样算今天达标）
- LC 3 一次 AC，能说清「last seen index 跳跃优化」。
- ★ LC 76 在 35min 内独立 AC，**且能口头解释为什么 `need` 必须用计数**；能手动 trace 一个含重复字符的样例不出错。

---

## 2️⃣ System Design 轨 — Two-Tower 召回（Retrieval）基础

### ① 今天做什么
用 Day 1 的**七步框架**（clarify → metrics(业务+ML代理双层) → data → model → architecture → trade-offs → monitoring）走一遍「大规模推荐的召回层：two-tower 模型」。今天不求做完整个推荐系统，只把**召回（retrieval）这一层**吃透——它是 W2 所有推荐题的地基。

### ② 在计划中的位置 / 为什么现在练
W1 的 SD 目标是「装框架 + two-tower 基础」。几乎所有推荐/搜索/广告 SD 题（YouTube 推荐、TikTok、Feed、Ads）都是「**召回(retrieval) → 排序(ranking)** 两级漏斗」，而召回层的默认答案就是 two-tower。今天把它讲干净，W2 就能直接复用。你 TikTok 的 AI Search / Visual Search 项目本身就是召回系统，讲这题你有天然弹药。

### ③ clarify 问题清单（开场先问，别急着画图）
- 语料规模？（millions vs billions of items → 决定 ANN 索引选型）
- 延迟预算？（召回通常要 < 50ms）
- 用户/物品有哪些特征？冷启动物品占比？
- 是纯推荐还是带 query 的搜索？（搜索要加 query tower）
- 反馈信号：点击 / 观看时长 / 转化？隐式还是显式？

### ④ 关键架构要点 + senior signal
- **双塔结构**：user tower 和 item tower 各自出一个 embedding，相似度 = 两个 embedding 的**点积**。关键优势：**item embedding 可离线预计算并建索引**，线上只算 user embedding，然后做 **ANN（近似最近邻）** 检索，millions→hundreds 只需个位数毫秒。
- **训练 = 对比学习**：正样本 = 用户真实交互过的 item；负样本怎么取是**真正的考点**：
  - **In-batch negatives**（同 batch 里别人的正样本当我的负样本）——最常用、最省算力。
  - **进阶：cross-batch / hard negative mining**（跨 batch 采样、挖难负例提升 recall）。
- **★ logQ correction（这是让面试官眼睛一亮的点）**：in-batch 负采样会让模型**偏向热门 item**（热门 item 更常出现在 batch 里被当负例，被过度惩罚 → 反而？其实是采样概率高需要修正）。做法：从 logits 里减去 `log Q(item)`（item 的经验采样概率）。**不加这个修正，长尾/冷门 item 的 recall 会崩**——这一句直接命中 senior signal 词 **long-tail** 和 **distribution correction**。
- **ANN 索引选型（显式 trade-off，必须给至少一个）**：HNSW（高 recall、内存贵、构建慢）vs IVF+PQ（省内存、可 billion 级、recall 略低）vs 暴力（小语料才行）。→ **W3 会专门讲 Vector DB，今天点到为止**。

### ⑤ ≥1 个显式 trade-off（今天必须说出口）
"**点积双塔 vs 交叉特征模型**：two-tower 为了能预计算 item 向量，牺牲了 user-item 的**早期交叉**（early interaction），表达力弱于排序层的 DLRM/交叉网络。所以我们用它做**召回**（要快、要覆盖），把精细打分留给**排序层**——这就是两级漏斗的分工。"

### ⑥ senior signal 词今天至少出现一个
**cold start**（冷启动新 item 无交互 → 用内容特征塔 / 双塔的 content-based 初始化）· **long-tail**（logQ correction 救长尾 recall）· **distribution shift**（线上分布漂移 → 定期重训 + 监控 embedding 分布）。

### ⑦ 验收标准
- 能在 8–10min 内不看稿讲完 two-tower 召回：结构 → 为什么能预计算 → 负采样 + logQ → ANN 选型 → 与排序层的分工。
- 至少显式说出 **1 个 trade-off** 和 **1 个 senior signal 词**。

> 来源：[Shaped.ai – Two-Tower Deep Dive](https://www.shaped.ai/blog/the-two-tower-model-for-recommendation-systems-a-deep-dive) · [Cross-Batch Negative Sampling (arXiv 2110.15154)](https://arxiv.org/pdf/2110.15154) · [Rohan Paul – Two-Tower + logQ case study](https://www.rohan-paul.com/p/ml-case-study-interview-question-a3e)

---

## 3️⃣ Behavioral 轨 — 精修故事 #1：AI Search 跨团队 alignment（Influence without authority）

### ① 今天做什么
把 Day 1 故事矩阵里的第一个 hook——**AI Search 跨团队 alignment**——写成 **30s / 2min / 4min 三个版本**，用 CARL 骨架，全程盯 **70% "I"**。

### ② CARL 骨架（Context → Action → Result → Learning）
- **Context**：一句话交代 stakes。"我在 TikTok 做 AI Search，要上线一个新的 ranking signal，但它依赖 **3 个团队**（召回、排序、索引）同步改动——我没有对他们的管理权（influence without authority）。"
- **Action（这里必须 70% 是 "I"，不是 "we"）**：
  - "**I** 主动约了三个团队的 tech lead，先各自摸清他们的顾虑和排期冲突。"
  - "**I** 把这个 signal 的收益翻译成每个团队各自关心的指标，让他们看到对自己 KPI 的好处。"
  - "**I** 设计了一个分阶段联调方案 + 一个共享的 rollout dashboard，让三方能同步观察上线状态。"
  - （对比反例：别说 "we aligned and shipped it"——那是中国背景最典型的 "we" 淹没个人贡献的坑。）
- **Result（3 个必须量化的数字位）**：
  - 🔢 **Technical**：`___`（如：搜索相关性指标 nDCG / CTR +X%，或延迟持平下 recall +X%）
  - 🔢 **Business**：`___`（如：搜索转化 / 人均搜索次数 / 留存 +X%）
  - 🔢 **Scope**：`___`（3 个团队、影响 X 亿 DAU 的搜索流量、X 周内上线）
- **Learning**："**I** 学到：跨团队推动力的核心不是技术，是**把你的目标翻译成对方的语言**。后来 **I** 把那个联调 dashboard 沉淀成了团队复用的模板。"

### ③ 三版本要点
- **30s**（电梯版）：Context 1 句 + 我做的最关键 1 件事 + 1 个数字结果。
- **2min**（标准版）：完整 CARL，Action 讲 2–3 个「I」动作，Result 给全 3 个数字。
- **4min**（深挖版）：加 1 个具体冲突细节（某团队最初反对的理由 + 我怎么化解）+ second-order effect（这个模板后来被别的项目复用）。

### ④ 易踩坑提醒（中国背景三大坑今天专治「we 过多」）
- 录音回放，**数一下 "I" vs "we" 的比例**，目标 I ≥ 70%。集体决策用 "we"，但**你个人的判断、行动、说服**必须用 "I"。
- 去掉弱词：`I think / maybe / probably / kind of` → 换成 `I decided / I drove / I proposed`。
- 三个数字位**今天就填上真实数字**（哪怕是量级估算），空着的故事等于没有故事。

### ⑤ 验收标准
- 三版本各录一遍音；2min 版 "I" 占比 ≥ 70%；3 个数字位全部填上；能不看稿讲 30s 版。

---

## 4️⃣ Math & Probability 轨 — 条件概率 / Bayes 定理

### ① 概念精讲
- **条件概率**：`P(A|B) = P(A∩B) / P(B)`。读作「已知 B 发生，A 的概率」。
- **Bayes 定理**：`P(A|B) = P(B|A)·P(A) / P(B)`，其中 `P(B) = P(B|A)·P(A) + P(B|¬A)·P(¬A)`（全概率公式展开分母）。
- **面试直觉三件套**：先验 `P(A)` → 似然 `P(B|A)` → 后验 `P(A|B)`。**罕见病 / 低基率场景里，即便测试很准，后验仍可能很低**——这就是 base rate fallacy，面试官最爱用它挖你。
- **ML 关联**：Naive Bayes 分类器就是「假设特征条件独立」后对 Bayes 的暴力应用；MLE vs MAP 的 MAP 就是加了先验的 Bayes 视角（后面 W5 会讲）。

### ② 今日练习题（经典诊断测试题 — 2026 高频）
某罕见病人群患病率 **1%**。一个检测：
- 若真有病，检测阳性概率 **99%**（灵敏度）；
- 若没病，检测阴性概率 **99%**（特异度，即假阳率 1%）。
**问：一个人检测阳性，他真的患病的概率是多少？**

**解题思路（自己算出数字，别急着看直觉）**：
1. 定义事件：D = 患病，`P(D)=0.01`，`P(¬D)=0.99`。
2. 阳性 = `+`。已知 `P(+|D)=0.99`，`P(+|¬D)=0.01`。
3. 全概率求分母：`P(+) = P(+|D)P(D) + P(+|¬D)P(¬D)`。
4. Bayes：`P(D|+) = P(+|D)P(D) / P(+)`。
5. **算完你会发现后验只有约 50% 上下**——直觉冲击点：测试准到 99%，阳性却只有一半是真病人，因为**健康人基数太大，假阳性的绝对数量和真阳性相当**。这就是 base rate 的威力。
- **进阶追问（面试官常接的）**：如果做**第二次独立检测也阳性**，后验变多少？（用第一次的后验当新的先验，再套一次 Bayes——体会「证据累积」如何拉高后验）。

### ③ 验收标准
- 独立算出单次阳性的后验数字，并能用「假阳性绝对数量」一句话解释为什么这么低；能口述两次阳性如何用「后验当先验」迭代。

> 来源：[Devinterview – Probability Questions 2026](https://github.com/Devinterview-io/probability-interview-questions) · [TensorTactics – Bayes' Theorem interview](https://www.tensortactics.com/machine-learning-interview-questions/bayes-theorem)

---

## ✅ 今日总验收清单（睡前打勾）
- [ ] Coding：LC 3 AC · ★LC 76 在 35min 内独立 AC + 能解释 `need` 用计数的原因
- [ ] SD：不看稿讲完 two-tower 召回，说出 ≥1 trade-off + ≥1 senior signal 词（cold start / long-tail / distribution shift）
- [ ] Behavioral：AI Search 故事三版本录音，2min 版 "I"≥70%，3 个数字位填满
- [ ] Math：算出诊断测试后验数字 + 能解释 base rate + 迭代 Bayes 思路
- [ ] 复盘 15min：把今天卡壳的点记到「弱项本」（下周弱项重写要用）

## 📌 明日预告（Day 3 · W1D3）
Fast & Slow 指针（LC 141/142/287/234 环检测 & 找重复）· SD 继续 two-tower→引出排序层 MTML 预习 · Behavioral 精修故事 #2（Listing Governance 卖家误杀 conflict）· Math：期望与方差。
