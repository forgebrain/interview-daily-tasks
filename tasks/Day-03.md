# [面试备考] Day 3/56 · W1D3 · 2026-07-03 — Fast & Slow 指针(环检测) + 排序层 MMoE 多任务 + 故事精修(Listing Governance conflict) + 期望与方差

> **Day 3/56 · Week 1 Day 3（新内容日 · 四轨齐发）**
> 今日主题一句话：Day 1 装框架、Day 2 灌第一批料，今天继续加固——Coding 从「窗口」进到「快慢指针」这套**用两个不同速度的指针把 O(n) 空间降成 O(1)** 的核心技巧；SD 从昨天的召回层往下游走一格，进到**排序层的多任务建模（MMoE）**，把「召回→排序」两级漏斗补全；行为打磨你的**第一个 conflict 故事（Listing Governance 卖家误杀）**——这是中国背景最爱回避、也是 Meta/Amazon 必考的维度；数学从条件概率进到**期望与方差 + 期望线性性**。
> 预计总耗时：**2.5–3.5h**（Coding 65min · SD 45min · Behavioral 45min · Math 30min · 复盘 15min）

---

## 🔥 今日先看：2026 面试形式最新观察（今天 Web 检索）
- **快慢指针是"识别度题"**：LC 142 / 287 这类题，面试官心里清楚——**没提前见过 Floyd 判圈，20min 内几乎不可能现场推出来**。所以它考的不是灵光一现，是「你系统刷过 pattern 没有」。Senior 的加分点不在能不能写出来，而在**能不能把"为什么快慢指针相遇点 + 头节点同速再走会撞在环起点"这个数学证明讲清楚**——这是区分「背过」和「懂了」的分水岭。（来源见文末）
- **推荐/视频排序层已是 SD 高频落点**：YouTube/TikTok 这类题，光讲 two-tower 召回已经不够，面试官会追问「排序层怎么同时优化点击、观看时长、点赞、完播」——答案就是今天的 **MMoE 多任务**。2024–2026 的产线文章（YouTube MTL、LinkedIn Feed）几乎都是 MMoE + 各任务独立 gate + side-tower 纠偏这套。
> 来源：[LeetCode Discuss – Floyd 判圈 142 详解](https://leetcode.com/discuss/post/3276344/) · [Yuan Meng – The Annotated Multi-Task Ranker (MMoE 代码级讲解)](https://www.yuan-meng.com/posts/mtml/)

---

## 1️⃣ Coding 轨 — Pattern: Fast & Slow Pointers（快慢指针 / Floyd 判圈）

### ① 今天做什么
- **LC 141. Linked List Cycle**（Easy，热身，10min）→ [leetcode](https://leetcode.com/problems/linked-list-cycle/) · [neetcode](https://neetcode.io/problems/linked-list-cycle-detection)
- **★ LC 142. Linked List Cycle II**（Medium，环起点，必须 25min 内独立 AC + 会讲证明）→ [leetcode](https://leetcode.com/problems/linked-list-cycle-ii/) · [neetcode](https://neetcode.io/problems/linked-list-cycle-detection)
- **LC 287. Find the Duplicate Number**（Medium，快慢指针的"神来之笔"应用，25min）→ [leetcode](https://leetcode.com/problems/find-the-duplicate-number/) · [neetcode](https://neetcode.io/problems/find-duplicate-integer)
- 有余力扫 **LC 234. Palindrome Linked List**（快慢找中点 + 反转后半，读题想 5min，明天可写）→ [LC234](https://leetcode.com/problems/palindrome-linked-list/)

### ② 在计划中的位置 / 为什么现在练
快慢指针是 W1「数组/链表基础」的第三件套，紧接 Two Pointers（Day1）、Sliding Window（Day2）。它的母题是 **Floyd's Tortoise & Hare**：一个指针走 1 步、一个走 2 步。掌握它你同时拿下三类高频题——**判圈、找环起点、把"找重复"伪装成链表环**。141/142 是 Meta/Google 的常青题，287 是把这套思想迁移到数组上的经典变形，面试官爱它因为「一眼看不出是链表题」。

### ③ 关键思路 / senior 要点 / 常见坑
- **LC 141（判圈）**：slow 走 1、fast 走 2，若有环 fast 必追上 slow（相对速度 1，环内必相遇）；fast 到 null 则无环。O(n)/O(1)。**别用 hash set**——那是 O(n) 空间的降级答案，面试要给 O(1)。
- **★ LC 142（找环起点）思路链（think aloud，别直接写）**：
  1. 先 141 判圈，记录相遇点 `meet`。
  2. **关键结论**：把一个指针重置到 `head`，另一个留在 `meet`，两个**同速（各走 1 步）**再走，**相遇处就是环起点**。
  3. **必会的证明（这是 senior signal）**：设头到环起点距离 `a`，环起点到相遇点 `b`，环长 `c`。相遇时 slow 走了 `a+b`，fast 走了 `a+b+kc`（多绕 k 圈），且 fast = 2×slow → `2(a+b)=a+b+kc` → `a+b=kc` → `a=kc-b=(k-1)c+(c-b)`。即**从头走 `a` 步 == 从相遇点走 `c-b`（补满一圈的剩余）步**，两者落点同为环起点。能把这段讲清，面试官会立刻给你上分。
- **LC 287（找重复）"神来之笔"**：数组 `nums[]` 值域 `[1,n]`、长度 `n+1`，把 `i → nums[i]` 看成链表的 `next` 指针。因为有重复值，必然存在两个下标指向同一节点 → **图里必有环**，环起点就是那个重复数字。于是**直接套 142 的两阶段**。约束：不能改数组、O(1) 空间——正好排除排序/hash，逼你用快慢指针。**这题背后是"抽屉原理 + Floyd"的组合，讲出这层映射就是亮点。**
- **Edge cases 清单**：空链表 / 单节点无环 / 单节点自环 / 整个链表就是一个大环 / 142 里 fast 的 `fast.next` 和 `fast.next.next` 都要判空防 NPE / 287 里数组只有 2 个元素。
- **Senior 沟通话术（UMPIRE）**：Understand（问：链表可能有环吗？节点值有约束吗？能改原结构吗？空间要 O(1) 吗？）→ Match（"快慢指针 / Floyd 判圈"）→ Plan（**先口述那段 `a=kc-b` 证明再动手**）→ Implement（边写边说指针推进）→ Review（trace 一个 3 节点带环样例）→ Evaluate（O(n)/O(1)，对比 hash set 的空间劣势）。

### ④ 验收标准（怎样算今天达标）
- LC 141 一次 AC。
- ★ LC 142 在 25min 内独立 AC，**且能不看稿讲完 `a=kc-b` 的环起点证明**。
- LC 287 能说清「数组下标→链表指针」的映射，并解释为什么约束（不改数组 + O(1)）逼出快慢指针解法。

---

## 2️⃣ System Design 轨 — 两级漏斗的下游：排序层多任务建模（MMoE）

### ① 今天做什么
接着 Day 2 的 two-tower **召回层**，今天把两级漏斗的**排序（ranking）层**补上，核心是 **MMoE（Multi-gate Mixture-of-Experts）多任务排序**。仍走七步框架，但重点放在 model + trade-offs 两步。

### ② 在计划中的位置 / 为什么现在练
Day 2 你能讲「召回怎么从 billions 里捞出 hundreds」，但真实推荐/视频题面试官必然追问：「**这几百个候选，你怎么排序**？用户又要点击、又要看得久、又要点赞、又不能标题党骗点击——你一个模型怎么同时优化这些冲突目标？」答案就是 MMoE。把它讲透，W2 的 YouTube/TikTok/Feed 排序题就都通了。你 TikTok 的 AI Search / Visual Search 排序经验是天然弹药。

### ③ clarify 问题清单（开场先问）
- 要优化哪些目标？彼此冲突吗？（点击率 vs 观看时长 vs 完播 vs 举报率）
- 有没有**主目标 + 护栏目标**之分？（如主推观看时长，但举报率不能涨）
- 排序层延迟预算？（通常 < 100ms，候选数几百到几千）
- 有没有 **position/selection bias**（用户更爱点靠前的，反馈被曝光位置污染）？
- 各任务标签的稀疏度？（点赞比点击稀疏得多 → 影响多任务权衡）

### ④ 关键架构要点 + senior signal
- **为什么不用「每个任务训一个独立模型」**：N 个目标训 N 个模型 → 算力/维护成本爆炸，且共享信息浪费。**为什么不用「一个共享底座 + N 个任务头（shared-bottom）」**：任务相关性低时会**负迁移（negative transfer）**——一个任务把共享参数往它那边拽，别的任务变差。
- **MMoE 的解法**：底层放一组**共享专家网络（experts）**，但**每个任务有自己独立的 gate**，gate 学出「这个任务该给哪些专家多少权重」。任务相关就多共享、不相关就各取所需，**优雅地缓解负迁移**——这句是核心卖点。
- **多个预测怎么合成最终分**：各任务出 `p_click / p_watchtime / p_like / p_report`，用一个**加权组合公式**（如 `score = p_click^a · p_watchtime^b · (1 - p_report)^c`）融合；权重靠线上 A/B 调。
- **★ selection/position bias 纠偏（senior signal）**：训练时并联一个 **side-tower / shallow tower** 专门吃「曝光位置」等 bias 特征，让主模型学到「去掉位置影响后的真实兴趣」，serving 时把 bias tower 关掉。**不纠偏，模型会学成"谁在前面推谁"的自我强化**——命中 senior signal 词 **distribution shift / feedback loop**。
- **cold start / long-tail（senior signal）**：新视频无互动信号 → 排序层靠内容特征 + 作者先验兜底；长尾任务标签稀疏 → 任务加权 / 辅助任务蒸馏。

### ⑤ ≥1 个显式 trade-off（今天必须说出口）
"**MMoE（软共享）vs shared-bottom（硬共享）vs 独立模型**：独立模型表达力最强但成本最高、无信息共享；shared-bottom 最省但任务冲突时负迁移严重；MMoE 用「共享专家 + 独立 gate」在**参数效率**和**任务隔离**之间取平衡——代价是 gate 增加了训练不稳定性和调参复杂度。在目标高度冲突（点击 vs 举报）的场景，MMoE 的收益最大。"

### ⑥ senior signal 词今天至少出现一个
**negative transfer**（MMoE 的存在理由）· **selection/position bias & feedback loop**（side-tower 纠偏）· **cold start / long-tail**（新视频 & 稀疏任务）· **distribution shift**（线上目标权重漂移 → 定期重调组合公式）。

### ⑦ 验收标准
- 能在 8–10min 内不看稿讲完：为什么要多任务 → shared-bottom 的负迁移问题 → MMoE（共享专家 + 独立 gate）如何解决 → 多预测如何合成 → position bias 纠偏。
- 至少显式说出 **1 个 trade-off** 和 **1 个 senior signal 词**。

> 来源：[Recommending What Video to Watch Next – YouTube MMoE 多任务排序论文](https://daiwk.github.io/assets/youtube-multitask.pdf) · [Yuan Meng – MMoE 代码级实现讲解](https://www.yuan-meng.com/posts/mtml/) · [ReachSumit – Multi-task Video RecSys 演进综述](https://blog.reachsumit.com/posts/2024/06/multi-task-video-recsys-p1/)

---

## 3️⃣ Behavioral 轨 — 精修故事 #2：Listing Governance 卖家误杀 conflict（运营 vs 合规）

### ① 今天做什么
把故事矩阵里的第二个 hook——**Listing Governance 卖家误杀冲突**——写成 **30s / 2min / 4min 三版本**，用 CARL 骨架，全程盯 **70% "I"**。**这是你的第一个 conflict 故事**，中国背景最容易把冲突淡化成「大家友好讨论后达成一致」——今天必须练**正面呈现分歧 + 你个人如何推动解决**。

### ② 为什么现在练（在计划中的位置）
W1 是搭 8 故事矩阵；W3 会专攻 Conflict + Failure 维度。今天先把这个**天生带冲突张力**的项目立起来——Meta 5 Signals 里 **Conflict** 是独立评分维度，Amazon 的 "Have Backbone; Disagree and Commit" 也直接考它。这个故事的天然张力：**运营团队要"宁可错杀不放过"降低违规，合规/卖家体验要"减少误伤"**——两个都对，你夹在中间。

### ③ CARL 骨架（Context → Action → Result → Learning）
- **Context（一句话交代 stakes + 冲突双方）**："我在 TikTok 做 Listing Governance（商品治理），治理模型上线后**误杀了一批正常卖家的 listing**。运营团队坚持维持高拦截阈值（怕漏放违规），但卖家申诉激增、合规和卖家体验团队施压要我放松——**两边诉求直接冲突**，都拿着数据找我。"
- **Action（必须 70% 是 "I"）**：
  - "**I** 没有简单选边，而是先把误杀样本拉出来做归因，发现 70% 的误杀集中在**低置信度区间**。"
  - "**I** 提出一个**分流方案**：高置信度（模型很确定违规）→ **自动处置**；低置信度 → **进人工审核队列**而不是直接杀。用一条 confidence 分界线把'宁可错杀'和'减少误伤'两个诉求解耦。"
  - "**I** 主动约了运营 + 合规两边 owner，用误杀归因数据 + 分流后的预估拦截率/误杀率**同时给两边算账**，让他们看到这不是零和。"
  - "**I** 推动先小流量 A/B 验证再全量。"
  - （反例别说："we discussed and reached consensus"——冲突被你抹平了，Signal 直接丢分。）
- **Result（3 个必须量化的数字位）**：
  - 🔢 **Technical**：`___`（如：误杀率下降 X%，同时违规漏放率仅上升 X%/基本持平）
  - 🔢 **Business**：`___`（如：卖家申诉量 -X%，卖家留存/GMV 影响 +X%）
  - 🔢 **Scope**：`___`（覆盖 X 万卖家 / X 亿 listing / X 周内落地）
- **Learning**："**I** 学到：面对两个'都对'的诉求，Senior 的价值不是选边，而是**找到能同时满足双方的第三条路（这里是用模型置信度分流）**。后来 **I** 把'高置信自动 + 低置信人审'沉淀成了治理平台的默认范式。"

### ④ 三版本要点
- **30s**：冲突一句（运营 vs 合规）+ 我的关键动作（置信度分流）+ 1 个数字。
- **2min**：完整 CARL，Action 讲 2–3 个「I」动作，Result 给全 3 个数字。
- **4min**：加一个**具体对抗细节**（运营 owner 最初反对的原话 + 我用什么数据说服）+ second-order effect（分流范式被平台复用）。

### ⑤ 易踩坑提醒（中国背景三大坑今天专治「回避 conflict」）
- **不许把冲突包装成"和谐讨论"**：面试官要听到**真实的对立**（谁反对、反对什么），以及**你个人**怎么破局。冲突越具体越可信。
- 录音数 "I" vs "we"，目标 **I ≥ 70%**；「设计分流方案」「找双方算账」这些**你的判断和行动**必须用 "I"。
- 去弱词：`I think / maybe` → `I proposed / I decided / I drove`。
- 3 个数字位今天就填真实量级（尤其 conflict 故事，**误杀率 & 漏放率这对 trade-off 数字最能体现你的判断力**）。

### ⑥ 验收标准
- 三版本各录一遍；2min 版 "I"≥70%；3 数字填满；能不看稿讲 30s 版，且冲突双方的对立点讲得清清楚楚。

---

## 4️⃣ Math & Probability 轨 — 期望与方差 + 期望线性性

### ① 概念精讲
- **期望** `E[X] = Σ x·P(X=x)`（离散）/ `∫ x·f(x)dx`（连续）——分布的"重心"。
- **方差** `Var(X) = E[(X-μ)²] = E[X²] - (E[X])²`（**这个计算公式面试必背**）；标准差 `σ=√Var`。
- **★ 期望线性性（最重要的武器）**：`E[X+Y] = E[X] + E[Y]`，**无论 X、Y 是否独立，永远成立**。这是把"看起来很难的期望"拆成一堆简单指示变量之和的钥匙。
- **方差不满足线性性**：`Var(X+Y) = Var(X) + Var(Y) + 2·Cov(X,Y)`，**只有独立时** `Cov=0`，才有 `Var(X+Y)=Var(X)+Var(Y)`。**这个区别是最爱考的坑**——期望可以随便拆，方差不行。
- **指示随机变量（indicator RV）技巧**：把"数一个数量"的期望写成 `X = Σ Iᵢ`，其中 `Iᵢ∈{0,1}`，则 `E[X]=Σ E[Iᵢ]=Σ P(第 i 件事发生)`。避开复杂联合分布。
- **ML 关联**：bias-variance 分解（W 后半会深讲）、mini-batch 梯度是真梯度的**无偏估计**（`E[batch grad]=true grad`）、A/B 测试里样本均值的方差 `σ²/n` 决定所需样本量。

### ② 今日练习题（指示变量经典题 — 2026 高频）
**帽子问题（Hat-check / 错排期望）**：`n` 个人把帽子交到前台，前台**随机**把 `n` 顶帽子发回（一个随机排列）。**问：拿回自己帽子的人数的期望是多少？**

**解题思路（用期望线性性 + 指示变量，别去数排列）**：
1. 定义指示变量：`Iᵢ = 1` 当第 i 个人拿回自己的帽子，否则 0。总人数 `X = I₁+I₂+...+Iₙ`。
2. `E[Iᵢ] = P(第 i 人拿回自己帽子)`。第 i 顶帽子等概率落到 n 个人手里 → 概率 `= 1/n`。
3. **期望线性性**（这里 Iᵢ 之间**并不独立**，但线性性照样用！）：`E[X] = Σ E[Iᵢ] = n · (1/n) = ?`
4. **算完你会发现答案是一个和 n 无关的常数**——不管 100 人还是 100 万人，期望拿回自己帽子的人数都一样。这个"反直觉的简洁"正是指示变量技巧的魅力。
- **进阶追问（面试官常接）**：这个人数 X 的**方差**是多少？（提示：`Var(X)=Σ Var(Iᵢ) + Σᵢ≠ⱼ Cov(Iᵢ,Iⱼ)`，因为 Iᵢ 不独立，协方差项不能扔——正好考你"方差不满足线性性"这个坑。算出来方差也有个漂亮结果。）

### ③ 验收标准
- 用指示变量 + 期望线性性独立算出帽子问题的期望，并能一句话解释「为什么和 n 无关」；能说清**为什么期望能直接拆而方差不能**（独立性的区别）。

> 来源：[Stanford CS109 – Linearity of Expectation with Indicator RVs](https://web.stanford.edu/class/archive/cs/cs109/cs109.1218/files/student_drive/3.3.pdf) · [Brilliant – Linearity of Expectation](https://brilliant.org/wiki/linearity-of-expectation/)

---

## ✅ 今日总验收清单（睡前打勾）
- [ ] Coding：LC 141 AC · ★LC 142 在 25min 内独立 AC + 会讲 `a=kc-b` 环起点证明 · LC 287 能说清「数组→链表」映射
- [ ] SD：不看稿讲完 MMoE 多任务排序，说出 ≥1 trade-off + ≥1 senior signal 词（negative transfer / position bias / cold start）
- [ ] Behavioral：Listing Governance conflict 故事三版本录音，2min 版 "I"≥70%，3 数字填满，**冲突双方对立点讲清楚（不抹平）**
- [ ] Math：用指示变量算出帽子问题期望 + 能解释「期望可拆、方差不可拆」
- [ ] 复盘 15min：把今天卡壳的点记到「弱项本」（W8 弱项重写要用）

## 📌 明日预告（Day 4 · W1D4）
Merge Intervals（LC 56/57/253/435 区间合并 & 会议室）· SD 把召回+排序拼成完整「视频推荐」端到端串讲（引出 monitoring）· Behavioral 精修故事 #3（Visual Search "kill your own baby" 主动 deprecate）· Math：常见分布（Bernoulli/Binomial/Poisson）起步。
