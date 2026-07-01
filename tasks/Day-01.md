# [面试备考] Day 1/56 · W1D1 · 2026-07-01 — 起步日：Two Pointers + 系统设计七步框架 + 故事矩阵搭建 + 概率计数基础

> **Day 1/56 · Week 1 Day 1（新内容日 · 四轨齐发）**
> 今日主题一句话：打地基——把「结构化解题 / 系统设计七步 / CARL 行为故事 / 概率语言」四套操作系统在第一天就装上，后面 55 天都在这套框架上加料。
> 预计总耗时：**2.5–3.5h**（Coding 70min · SD 40min · Behavioral 45min · Math 30min · 复盘 15min）

---

## 🎯 今天的元目标（Day 1 特有）
第一天不是刷量，是**装框架**。你已有 5 个 TikTok 项目和扎实工程能力，缺的是把它们按美国 Senior/Staff 面试的「话术协议」重新编码。今天四轨各交付一个**可复用模板**：UMPIRE（coding）、七步（SD）、CARL + 故事矩阵（behavioral）、概率语言（math）。这四张纸后面每天都会用到。

---

## 1️⃣ Coding 轨 — Pattern: Two Pointers（对撞指针）

### ① 今天做什么
- **LC 167. Two Sum II – Input Array Is Sorted**（Easy，热身，15min）→ [leetcode](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) · [neetcode](https://neetcode.io/problems/two-integer-sum-ii)
- **★ LC 15. 3Sum**（Medium，必须 30min 内独立 AC）→ [leetcode](https://leetcode.com/problems/3sum/) · [neetcode](https://neetcode.io/problems/three-integer-sum)
- 有余力再看 **LC 11. Container With Most Water**（贪心式对撞指针，读题想 5min 即可）→ [neetcode](https://neetcode.io/problems/max-water-container)

### ② 在计划中的位置 / 为什么现在练
Two Pointers 是 W1「数组基础四件套」（Two Pointers / Sliding Window / Fast&Slow / Merge Intervals）的第一件，也是最能训练「brute-force → 用结构换复杂度」思维的入门 pattern。3Sum 是 Meta / Google 电话轮和 onsite 的**高频常青题**，几乎是 Senior coding 轮的「握手题」——面得不干净会直接掉 signal。

### ③ 关键思路 / senior 要点 / 常见坑
- **167**：数组已排序 → 左右指针，`sum<target` 左移右、`sum>target` 右移左。O(n)/O(1)。别用哈希（那是 LC 1 无序版的解）——面试官给「sorted」就是在暗示你用双指针。
- **3Sum 思路链（think aloud，别直接写）**：
  1. brute force 三重循环 O(n³) → 先说出来，再说「我要降到 O(n²)」。
  2. **先排序**（O(n log n)），固定 `i`，在 `i+1..n-1` 上跑对撞双指针找 `-nums[i]`。
  3. **去重是这题的真正考点**：`i` 跳过重复（`nums[i]==nums[i-1] continue`）；找到一组解后 `left`/`right` 各自跳过重复值。
  4. 剪枝：`nums[i]>0` 直接 break（排序后后面都是正数，三数和不可能为 0）。
- 复杂度：时间 O(n²)，空间 O(1)（不算输出/排序栈）。
- **Edge cases 清单**：空数组 / 长度<3 / 全 0（`[0,0,0]` 应返回 `[[0,0,0]]` 一组）/ 全正或全负 / 大量重复值 / 含重复三元组必须去重。
- **Senior 沟通话术（UMPIRE，装进肌肉记忆）**：
  - **U**nderstand：复述题意 + 问 clarify（数组能改吗？要下标还是值？重复三元组算不算？数值范围/溢出？）
  - **M**atch：「这匹配 Two Pointers on sorted array 模式」
  - **P**lan：口述 brute→optimal + 复杂度，**写代码前**先讲去重策略
  - **I**mplement：边写边说，变量名可读
  - **R**eview：跑一个含重复的小样例 `[-1,0,1,2,-1,-4]` 手动 trace
  - **E**valuate：复杂度 + 若要 4Sum / kSum 如何推广

### ④ 验收标准（怎样算今天达标）
- [ ] 167 一次 AC，能口述为何 sorted → 双指针而非哈希。
- [ ] **3Sum 在 30min 内独立写出并通过去重测试**（`[-1,0,1,2,-1,-4]` → `[[-1,-1,2],[-1,0,1]]`）。
- [ ] 能用 UMPIRE 六步把 3Sum 「讲」一遍（录 2min 音频自听，是否有 think-aloud）。
- [ ] 若卡在去重 → 在下方笔记标记「弱项：3Sum 去重」，W8 会重抽。

---

## 2️⃣ System Design 轨 — 七步框架 + 双层指标 + Two-Tower 基础

### ① 今天做什么
不设计具体系统，先**背熟七步框架**和**开场万能模板**，然后用 Two-Tower（双塔召回）作为第一个「零件」熟悉一遍。

**七步框架（每道 ML SD 都套这个）**：
`clarify → metrics(业务层 + ML代理层) → data → model → architecture → trade-offs → monitoring`

**开场万能模板（onsite 一字不差地说出来）**：
> "在深入之前，我会先澄清需求和约束，然后定义**业务指标和 ML 代理指标两层**，再依次讲 data、model、架构、关键 trade-off，最后是上线后的 monitoring。我会按 clarify→metrics→data→model→arch→trade-offs→monitoring 这七步展开，你随时可以打断我。"

### ② 在计划中的位置 / 为什么现在练
W1 是整个 SD 轨的「协议层」。Senior/Staff 的 SD 轮，**结构化 + 主动 clarify + 双层指标**本身就是 signal——很多中国背景候选人一上来就冲模型细节，跳过 metrics 和 trade-off，直接被判「缺乏 senior 全局观」。今天把框架焊死，W2 起才好往里填推荐/广告/LLM 系统。

### ③ 关键思路 / senior 要点
- **Clarify 问题清单（开口必问）**：用户规模 / QPS / 延迟预算（p99）/ 新鲜度要求 / 是 retrieval 还是 ranking / 冷启动比例 / 训练数据可得性 / 业务目标（点击？停留时长？留存？）。
- **双层指标（Day 1 核心概念，务必内化）**：
  - **业务层**：DAU、留存、观看时长、GMV、CTR（真正 KPI）。
  - **ML 代理层**：离线 AUC / NDCG@k / Recall@k、在线 A/B 的 CTR lift（可快速迭代的代理）。
  - Senior 话术：「我会用代理指标做离线迭代，但**最终以业务指标的 A/B 结果为准**，并警惕代理与业务指标脱钩（proxy-metric divergence）。」
- **Two-Tower 基础**：user tower + item tower 各自出 embedding，**独立编码 → dot product / ANN 检索**（HNSW/IVF-PQ）。核心优势：item embedding 可离线预计算、新 item 免全量重训；核心局限：user/item 特征在最后点积前**不交互**，抓不到细粒度条件偏好 → 所以是**召回层**，精排交给能做特征交叉的 ranking 模型。
- **多级漏斗心智图**（W2 会展开）：millions → (two-tower 召回) → hundreds → (L1/L2 排序) → dozens → (重排 + 业务护栏)。
- **≥1 个显式 trade-off**：Two-Tower（双塔独立、可 ANN、快但表达弱）vs 单塔交叉模型（表达强但无法预计算、召回阶段算不起）→ 所以召回用双塔、精排用交叉。

### ④ 验收标准
- [ ] **不看稿背出七步 + 开场模板**（录音，≤40s 说完开场）。
- [ ] 能对任意「设计 X 推荐」题当场列出 ≥5 个 clarify 问题。
- [ ] 能讲清双层指标，并举一个「代理指标涨、业务指标不涨」的例子（如 CTR 涨但停留时长跌 = clickbait）。
- [ ] 出现至少一个 senior signal 词：**cold start / long-tail / distribution shift**（今天先在 Two-Tower 里点到「新 item 冷启动」）。

---

## 3️⃣ Behavioral 轨 — 搭故事矩阵 + CARL 框架

### ① 今天做什么
把 5 个 TikTok 项目**映射成 8 个行为故事**，每个故事写 **30s / 2min / 4min 三个版本**的骨架（今天先写骨架 + 数字位，不用全文）。

**5 项目 → 8 故事 的 hook（直接用）**：
| # | 项目 | 主打维度 | 一句话 hook |
|---|------|---------|------------|
| 1 | AI Search | Cross-team / Influence without authority | 无职权推动 3 个团队同步上线同一 ranking signal |
| 2 | AI Search | Results / Ambiguity | 需求模糊下定义指标并交付搜索质量提升 |
| 3 | Listing Governance | Conflict | 卖家误杀：运营 vs 合规冲突 → high-confidence 自动处置 + low-confidence 转人工队列 |
| 4 | Visual Search | Growth / "Kill your own baby" | 主动 deprecate 自己力推的 single-vector index，改 multi-vector |
| 5 | Face Recognition | Ethics / Judgment | demographic fairness 审计后，主动建议从 1:N 收窄到 1:1 verification |
| 6 | OCR | Leadership / Mentorship | 一人项目 → 平台化，让 5 个团队复用（API + 文档 + onboarding）|
| 7 | Listing Gov / OCR | Failure（真实损失 + learning）| 选一个真出过线上问题的，别包装成 success |
| 8 | 任意 | Communication / Data-driven decision | 用数据说服 leadership 改方向 |

### ② 在计划中的位置 / 为什么现在练
W1 是行为轨地基。你的**三大中国背景坑**从第一天就要盯：① "we" 太多（目标 **I ≥ 70%**）② 缺业务量化 ③ 回避 conflict（故事 3、7 必须正面练，W3 深挖）。故事矩阵是后 7 周所有行为演练的弹药库，今天不搭好，后面无米下锅。

### ③ CARL 骨架 + 关键要点
**CARL**（比 STAR 多了 Learning，Meta「Growth」信号最吃这个）：
- **C**ontext：背景 + 你的**具体角色**（1–2 句，别铺垫太长）
- **A**ction：**你个人**做了什么（70% 用 "I"；"we decided" → "I proposed and drove"）
- **R**esult：**3 个数字**——technical（如召回率 +X%）+ business（如误杀率 -Y% / GMV +$Z）+ scope（影响 N 个团队 / M 个用户 / QPS）
- **L**earning：学到什么 + 之后如何应用（体现 self-awareness）
- **反应链训练**：Decode（听懂考的是哪个维度）→ Select（选矩阵里最贴的故事）→ Deliver（按时长版本讲）

### ④ 验收标准
- [ ] 8 个故事的表格填完，每个故事**至少标出 3 个数字位**（哪怕先填 `[召回率 +__%]` 占位，之后回忆真实数据补上）。
- [ ] 挑故事 1（AI Search cross-team）写出 **2min 版全文**，并自查：I/we 比例、是否有 3 个数字、是否回避了冲突。
- [ ] 录音讲故事 1，回放数 "I" vs "we" 次数（目标 I ≥ 70%）。
- [ ] **易踩坑提醒**：Result 只说「上线了/效果好」= 无效；必须有可量化的 business 影响。离职原因今天不碰（W4 专练，禁区：politics / TikTok ban / Chinese ownership）。

---

## 4️⃣ Math & Probability 轨 — 概率基础与计数

### ① 今日概念精讲：样本空间、事件、古典概型与计数
- **古典概型**：等可能样本空间下 `P(A) = |A| / |S|`。面试里 80% 的「脑筋急转弯」概率题本质是**把 |A| 和 |S| 数对**。
- **计数三件套**：
  - 乘法原理（分步）、加法原理（分类）
  - 排列 `P(n,k) = n!/(n−k)!`（有序）
  - 组合 `C(n,k) = n!/(k!(n−k)!)`（无序）
- **补集技巧**：「至少一个」几乎总是用 `P(至少一个) = 1 − P(一个都没有)`（Day 1 必须形成条件反射）。
- **为什么现在练**：Math 轨 8 周轮两遍，第一站先把「概率语言 + 计数」这层地基打好，后面 Bayes / 期望 / 分布 / A-B 测试都建在它上面。ML 面里的概率题（Meta/Google 常考）多是伪装的计数题。

### ② 练习题（给思路，鼓励自解，先别看数字答案）
> **题**：一个房间里有 23 个人（经典生日问题）。假设一年 365 天、生日均匀独立。求「**至少有两个人生日相同**」的概率大约是多少？直觉上你觉得该很小，实际呢？

**解题思路（自己算出数字）**：
1. 用**补集**：`P(至少两人同生日) = 1 − P(23 人生日两两不同)`。
2. `P(全不同) = 365/365 × 364/365 × 363/365 × … × 343/365`（23 项连乘）。
3. 即 `∏_{k=0}^{22} (365−k)/365`。
4. 估算或用对数近似，看结果是否**超过 50%**——这就是「生日悖论」反直觉的点：为什么 23 人就过半？（提示：关键是「**对数**」——23 人有 C(23,2)=253 个「配对」，而非 23 个。）
5. **面试延伸**：面试官常追问「要多少人才能让概率 ≥ 99%？」——用同样连乘逼近。

### ④ 验收标准
- [ ] 独立算出生日问题的近似数值，并能解释「253 个配对」这个直觉。
- [ ] 能背出补集技巧的触发词：题目出现「**至少 / at least**」→ 立刻想补集。
- [ ] 能区分何时用排列（有序）何时用组合（无序）。

---

## 📚 今日扩展资源（2026 最新，WebSearch 补充）
- **Meta MLE 面试结构（2026）**：终轮 = Coding(45min) + AI-assisted Coding(60min，新增！) + Behavioral(45min) + ML Design。ML/SWE 双 expert 打分，coding bar 接近纯 SWE 轮。〔来源：[Exponent – Meta MLE Guide 2026](https://www.tryexponent.com/guides/meta-machine-learning-engineer-interview)〕
  - ⚠️ **新趋势**：Meta 已加入 **AI-assisted coding 轮**（允许/要求用 AI 辅助写码，考你如何 prompt、审查、debug AI 产物）——W6/W7 会专门练。
- **Two-Tower 深入**：user/item 双塔独立编码 → ANN 召回，多级漏斗 millions→hundreds→dozens。〔来源：[Shaped – Two-Tower Deep Dive](https://www.shaped.ai/blog/the-two-tower-model-for-recommendation-systems-a-deep-dive) · [Google Cloud – Two-Tower Retrieval](https://docs.cloud.google.com/architecture/implement-two-tower-retrieval-large-scale-candidate-generation)〕
- **Meta 五大行为信号（2026 确认）**：Conflict / Results / Ambiguity / Growth / Communication；官方 tip 明确要求「用 I 不用 we」。〔来源：[interviewing.io – How Meta evaluates behavioral](https://interviewing.io/blog/how-software-engineering-behavioral-interviews-are-evaluated-meta) · [interviewhelp.io – Meta signals](https://www.interviewhelp.io/blog/posts/what_are_the_actual_behavioral_signals_for_meta/)〕
- **Coding pattern 参考**：[NeetCode 150 – Two Pointers 分组](https://neetcode.io/practice)

---

## ✅ Day 1 收尾自检（勾完再睡）
- [ ] Coding：3Sum 30min 内 AC + UMPIRE 讲一遍
- [ ] SD：七步 + 开场模板不看稿背出 + 双层指标能举例
- [ ] Behavioral：8 故事矩阵表填完 + 故事 1 的 2min 版 + 录音查 I/we
- [ ] Math：生日问题自算 + 「at least → 补集」条件反射
- [ ] **弱项记录**（写在这条下面，W8 重抽会用）：______________________

> 明日预告（Day 2 / W1D2）：**Sliding Window**（LC 3 无重复最长子串 / LC 76 最小覆盖子串）+ SD 继续「指标与数据」深化 + Behavioral「Impact 维度」起步 + Math「条件概率与 Bayes」。
