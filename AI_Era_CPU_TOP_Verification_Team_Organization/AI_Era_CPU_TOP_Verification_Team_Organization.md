# AI时代CPU TOP验证团队的组织演变与组建方法

## 1. 目的与结论

本文从蒙古军十人队、Brooks的外科手术团队和传统CPU TOP验证团队出发，讨论LLM与Coding Agent普及后，CPU TOP验证团队应如何组织。

这三种组织形式背景不同，10人也并非普遍适用的最佳规模，但它们体现了几项共同的组织原则：

- 核心技术决策需要明确责任人。
- 大系统应拆成可独立闭环的小型责任单元。
- 成员以主专业为基础，同时具备通用能力。
- 稀缺专家和平台能力在更高层共享。
- 扩大规模应依靠分层和接口，而不是扩大扁平团队。

AI改变的是执行能力，不是最终责任。AI时代更合适的组织形态，是由稳定的人类责任层与弹性的Agent执行层协同工作：

> 人类负责规格解释与裁决、微架构约束、风险评估及最终签核；Agent负责代码检索与生成、测试开发、回归执行、失败分类和文档整理。

本文的“CPU TOP团队”指单个高性能CPU Core集成层的专职验证团队，不包含全部微架构Block DV、Cluster/SoC验证、Physical Design、DFT、编译器和软件团队。

---

## 2. 两种小团队原型

### 2.1 蒙古军十人队

蒙古军采用十、百、千、万的十进制军政组织。十人队（Arban）是基层责任单元，由十夫长逐级接收命令、组织行动并维持纪律。

实际运作特点：

- **多技能而非固定分工**：普通士兵都是骑射兼备的轻骑兵，侦察、警戒、袭扰、追击可动态分配，不像罗马军团有固定的步兵/骑兵/工兵编制。
- **稀缺专业上提共享**：攻城工匠、信使、医官不在每个十人队配置，而在千人队或万人队层面集中调配。
- **怯薛（Keshig）作为人才流转池**：各千人队优秀子弟选入可汗亲卫军轮值，训练后派回担任指挥官，兼顾护卫、储备和跨单元知识共享。
- **战术弹性**：十人队是最小战术单元，实战中常以百人队为机动单位。“曼古歹”（假退诱敌）等需要多个百人队协同的战术，依赖十进制指挥链实现大范围协同。

与CPU团队的类比边界：

| 蒙古十人队 | CPU验证组织 |
| --- | --- |
| 十夫长 | DV Lead或Domain Owner |
| 多技能骑兵 | 具备端到端能力的T型工程师 |
| 动态任务分工 | 按feature和bug动态分工 |
| 上级共享稀缺专家 | 共享Formal、Emulation、性能和EDA平台 |
| 基层单位逐级组合 | 多个微架构Block通过接口组成CPU组织 |

真正值得借鉴的是“小型责任单元＋分层指挥”，而不是数字10本身。

### 2.2 Brooks的外科手术团队

Brooks在《人月神话》中指出，复杂系统开发不能按人月线性换算：

> 有效产能 = 人数 × 个人产能 − 沟通成本 − 培训成本 − 集成成本 − 资源争用

人数增加会带来组合式沟通成本。理论上的两两沟通路径为：

$$
\text{沟通路径} = \frac{N(N-1)}{2}
$$

| 人数 | 潜在沟通路径 |
| ---: | ---: |
| 4 | 6 |
| 5 | 10 |
| 6 | 15 |
| 8 | 28 |
| 10 | 45 |
| 15 | 105 |
| 20 | 190 |

该公式不是生产率模型，但能说明高耦合任务扩大扁平团队的代价。

Brooks描述的团队，重点角色可映射如下：

| Brooks式角色 | CPU TOP验证角色 |
| --- | --- |
| Surgeon | DV Lead/验证架构师，掌握总体策略和签核标准 |
| Co-pilot | 副Lead或环境架构师，理解全部环境和验证计划 |
| Specialists | ISA、MMU、Cache、异常、Debug/RAS等领域Owner |
| Toolsmith | 回归、覆盖率、自动化、性能与仿真基础设施负责人 |

核心并非“一个英雄带若干助手”，而是由一名主刀保持概念完整性，一名副手提供全局挑战和备份，其他专业角色放大核心产出。

---

## 3. 传统CPU TOP验证团队

### 3.1 CPU TOP验证范围

CPU TOP通常负责：

- ISA、System Register、ISS/Cosim
- IEX、VEX（FP/SIMD/SVE）、cross-block bypass、mixed-type scheduling
- Exception、Interrupt、Privilege、Virtualization
- MMU、Cache、Coherence、Memory Ordering
- OOO cross-block recovery、resource hazard、liveness
- Debug、Trace、PMU、RAS、power、reset、boot
- Random instruction methodology、shared environment、reference、coverage、regression、sign-off

在Block DV和公共环境基本就绪的前提下，CPU TOP核心团队通常为8～10人：人数过少难以覆盖关键领域并形成备份，人数过多则会增加协作冲突、重复建设和评审成本。

### 3.2 微架构小组

微架构小组是围绕一个微架构责任域建立的小型跨职能团队，对uArch定义、RTL、Block DV和TOP集成交付端到端负责。

业界通常根据功能边界和状态耦合关系划分微架构小组：

| 微架构小组 | 主要范围 |
| --- | --- |
| Frontend | Fetch、BTB、Branch Prediction、I-Cache |
| Decode/Rename/Dispatch | RAT、ROB Allocation、Resource Check、Dispatch |
| Schedule/Execute | Issue、Wakeup/Select、ALU/FPU/SIMD |
| Retire/Exception | Commit、Flush、Precise Exception、Privileged State |
| LSU/MMU | Load/Store、TLB、Address Translation、Memory Ordering |
| Cache/Coherence | L1/L2、Snoop、Coherence Interface |
| Cross-cutting | Debug、RAS、Security、Low Power、Performance Events |

每个微架构小组至少需要一名RTL Owner和一名独立DV Owner。

### 3.3 传统团队的核心角色

一个10人左右的典型组织可以这样划分：

- **1人**：DV Lead，负责验证计划、风险、覆盖率与流片签核。
- **2～3人**：指令流、异常、中断、特权态、虚拟化。
- **2人**：MMU、Cache、内存一致性及顺序模型。
- **1～2人**：Debug、Trace、PMU、RAS、安全。
- **1～2人**：随机激励、参考模型、Cosim。
- **1人**：回归、覆盖率、自动化和性能场景。

Formal、Emulation、性能模型、CPU Architect和EDA计算平台可以共享，但必须有明确接口和固定投入。

---

## 4. LLM与Loop Engineering带来的变化

### 4.1 从代码稀缺转向可信判断稀缺

Codex、Claude Code和内部Agent已经能够：

- 搜索和理解大型代码库。
- 生成或修改SystemVerilog、UVM、SVA、脚本和文档。
- 运行lint、编译、测试和回归。
- 根据失败结果继续定位、修正和重跑。
- 聚类失败、生成debug package并提出根因候选。

新的瓶颈因此转向：

- 规格和微架构合同是否完整。
- 任务能否定义可验证的完成条件。
- Reference Model、checker和评分函数是否可信。
- Agent产物能否及时review和集成。
- Coverage增长是否代表风险真实下降。
- 最终责任和sign-off是否清晰。

AI可以承担外科手术团队中的Co-pilot、Editor、Program Clerk、Toolsmith、Tester和部分实现工作，但不能可靠替代Surgeon、Domain Owner和sign-off责任人。

### 4.2 受控的Loop Engineering

Loop Engineering不是“循环到测试变绿”，而是：

```text
明确任务合同
  -> Agent执行
  -> 确定性工具检查
  -> 失败分类和证据提取
  -> 受约束修正
  -> 独立review
  -> Human Owner签核
```

任务合同至少应包含：

```text
Authoritative Sources
Scope and Configuration
Allowed / Forbidden Changes
Architectural and uArch Invariants
Tests / Checker / Reference
Coverage Goal
Stop and Escalation Conditions
Definition of Done
```

当多个Agent并行生成代码和测试时，主要风险不是代码风格差异，而是规格理解、验证假设和签核标准发生偏移。为保证验证环境的一致性，每个Agent任务都应明确权威规格及版本、目标配置、架构与微架构不变量、允许修改范围、Reference Model与Checker以及验收标准。Domain Owner负责本领域Verification Intent和任务边界，DV Lead负责跨领域规则、规格冲突与sign-off标准的统一裁决。Agent可以并行执行，验证责任和最终判断仍由Human Owner承担。

### 4.3 AI能力分级

| 等级 | 能力 | 组织影响 |
| --- | --- | --- |
| L1 Assistant | 查询、摘要、代码和测试初稿 | 提升个人效率 |
| L2 Workflow | 自动编译、回归、triage和报告 | 减少重复平台劳动 |
| L3 Closed Loop | 在确定性gate内自动修复和重跑 | 减少部分执行工作 |
| L4 Governed Multi-Agent | 多Agent分工、独立检查、审计和成本控制 | 改变团队并行方式 |

### 4.4 AI最容易替代的工作

AI主要消除的是工具操作、信息整理和重复编码这些“偶然复杂度”；CPU架构状态空间、并发行为、规格歧义和签核责任这些“本质复杂度”仍然需要人承担。

| 工作 | 自动化程度 |
| --- | --- |
| 失败聚类、日志摘要 | 高 |
| 波形初定位、相似失败检索 | 高 |
| UVM/SVA/脚本样板代码 | 高 |
| 回归选择、资源调度 | 高 |
| 覆盖率报表、重复测试识别 | 高 |
| 规格生成测试初稿 | 中 |
| 约束与覆盖点初稿 | 中 |
| Coverage hole测试建议 | 中 |
| 跨模块根因判断 | 低 |
| 规格错误与歧义识别 | 低 |
| 内存顺序、异常风险分析 | 低 |
| Coverage waiver、流片签核 | 很低 |

---

## 5. AI时代CPU TOP验证团队

### 5.1 稳定的人类责任层＋弹性的Agent执行层

不建议建立“Codex组”“Claude组”或独立Prompt组。工具会变化，人的岗位应由技术责任和sign-off边界定义。

```text
Human Decision Layer
└─ DV Lead / Surgeon

Human Domain Ownership Layer
├─ Architecture / Exception / Virtualization Owner
├─ Memory System / VMSA / Coherence Owner
├─ OOO / Recovery / uArch Stress Owner
└─ Debug / RAS / Power / System Feature Owner

Elastic Agent Execution Layer
├─ Co-pilot / Eval Agent
├─ Codebase Explorer Agents
├─ RTL/UVM/SVA Implementation Agents
├─ Test and Coverage Agents
├─ Regression and Triage Agents
└─ Review and Documentation Agents
```

Agent是临时执行单元，Human Owner是稳定责任单元。

### 5.2 与传统团队的角色差异

核心角色与3.3节传统团队一致，AI时代的主要变化是：

- 传统的Co-pilot和Platform职责由Agent Execution Layer承担。
- 各Domain Owner职责范围不变，但工作方式从“自己写代码和测试”转向“定义任务合同、review Agent产出、sign-off”。

每个Domain Owner只有一人，存在单点人员风险。建议每个Domain Owner指定一名Shadow Reviewer（由另一个域的Owner兼任），通过交叉review形成最低限度的知识冗余。

### 5.3 Agent并行与Review带宽

- 复杂跨模块任务：每个Owner同时监管1～2个活跃Agent任务。
- 边界清晰的小任务：同时监管3～5个。
- Agent输出等待review超过一个工作日，应停止增加并行任务。
- 公共组件的并行修改应使用隔离工作区和明确合并顺序。

这些数字只是运行起点，应依据返工率、review延迟和回归吞吐调整。

### 5.4 与微架构Block的接口

每个Block向CPU TOP交付：

```text
uArch Contract and Interface Invariants
Block vPlan and Coverage
Assertions and Checker Qualification
Stress / Recovery Scenarios
Known Limitations / Waivers
Failure Signatures
```

CPU TOP团队负责检查跨Block不变量、构造跨域场景、路由TOP failure，并把跨域缺陷反馈到uArch contract和Block vPlan。

---

## 6. 运行与演进

### 6.1 基本运行规则

- 每个任务只有一个明确Owner和一个可验证的Definition of Done。
- 每个Agent任务解决一个边界清晰的问题，保持小Diff。
- 记录模型、任务模板、commit、seed、配置和工具版本。
- 保存第一失败证据，不能只保留最终绿色结果。
- 公共checker和reference变更必须附带negative test或mutation evidence。
- Known failure和waiver由AI提议、人类批准后进入知识库。

### 6.2 衡量指标

不应以代码行数、测试数量、Agent数量、Token消耗或原始coverage百分比衡量团队。

| 类别 | 建议指标 |
| --- | --- |
| 交付 | 已关闭的验证需求和风险项 |
| 缺陷 | 有效RTL bug、首次发现阶段、后级/硅后逃逸缺陷 |
| Debug | First failure到root cause的平均时间 |
| Coverage | 有效hole关闭时间和mutation detection rate |
| AI质量 | Agent产物接受率、返工率、回退率和错误归因率 |
| 流动效率 | Human Review等待时间、Agent WIP和合并冲突率 |
| 成本 | 每个已关闭需求的Token、仿真、Formal和许可证成本 |
| 组织韧性 | 关键岗位备份率、知识接管时间和单点人员风险 |

### 6.3 演进路线

| 阶段 | 重点 |
| --- | --- |
| 建立基线 | 固化vPlan、责任矩阵、回归吞吐、debug时间和逃逸缺陷 |
| AI Assistant | 用于搜索、文档、测试初稿和log摘要，所有修改由人review |
| Workflow Automation | 自动回归、failure signature、triage、debug package和审计 |
| 受控Loop Engineering | 只让边界清晰、Reference Model确定的任务进入闭环 |
| 组织调整 | 依据真实质量和产能数据调整岗位 |

组织调整应以多个真实里程碑为依据，确认closure周期缩短、有效bug增加、返工和逃逸缺陷没有恶化，而不是因购买AI工具立即减员。

---

## 7. 团队组建的主要考量

| 维度 | 关键问题 |
| --- | --- |
| 产品复杂度 | 是否为新微架构；是否包含宽发射OOO、SMT、复杂一致性、虚拟化、RAS和安全扩展 |
| 任务耦合度 | 工作能否独立定义输入、输出和完成条件；是否频繁修改公共环境和checker |
| 责任与独立性 | 谁定义验证架构、拥有Reference Model、裁决规格、批准waiver并最终sign-off |
| 专业覆盖与备份 | 关键领域是否有Owner；是否至少两人理解总体环境；知识能否被接管 |
| 平台和AI成熟度 | UVM、ISS、回归、coverage、failure signature和Agent闭环是否稳定可信 |
| 生命周期与带宽 | 当前处于架构、开发还是closure阶段；新增人员或Agent是否超过review吞吐 |
| 资源与治理 | 仿真、Formal、许可证、Token和存储是否受控；IP、权限、网络和审计是否合规 |
| 人才梯队 | 初级人员能否通过Feature ownership、debug和review成长为未来Owner |

小团队能否精而强，也取决于外围系统。CPU TOP核心团队必须得到微架构Block、Formal、Emulation、架构、性能和EDA平台的明确支持。

---

## 8. 结论

蒙古十人队强调分层指挥和基层责任，Brooks强调概念完整性，传统CPU TOP团队强调专业覆盖与协作带宽。这些原则在AI时代仍然适用，而Agent提供了可按需扩展的执行能力。因此，合理的CPU TOP组织应当：

- 由Lead负责总体策略与sign-off，并承担最终责任。
- 由Domain Owner对架构、微架构、ISA和System Feature端到端负责。
- 通过明确的Block-to-TOP交付规范，衔接RTL设计、Block DV与CPU TOP集成验证。
- 让Agent承担Co-pilot式全局review、Platform和高吞吐执行。

AI应减少重复劳动、无效等待和信息搬运，同时保留人类的独立判断、微架构理解与验证责任，并持续建设人才梯队。

---

## 参考资料

1. Frederick P. Brooks Jr., *The Mythical Man-Month*, Chapter 3, “The Surgical Team”. <https://www.oreilly.com/library/view/mythical-man-month-the/0201835959/ch03.xhtml>
2. Frederick P. Brooks Jr., “No Silver Bullet: Essence and Accidents of Software Engineering”. <https://www.cs.unc.edu/techreports/86-020.pdf>
3. Igor de Rachewiltz, *The Secret History of the Mongols*. <https://sourcebooks.web.fordham.edu/basis/The%20Secret%20History%20of%20the%20Mongols_%20A%20Mongolian%20Epic%20Chronicle%20of.pdf>
4. Encyclopaedia Iranica, “Army ii. Islamic, to the Mongol period”. <https://www.iranicaonline.org/articles/army-ii/>
5. OpenXiangShan, “Backend overview”. <https://docs.xiangshan.cc/projects/design/en/kunminghu-v3/backend/>
6. RISCV-BOOM Documentation. <https://docs.boom-core.org/en/latest/>
7. Siemens EDA, “2024 Wilson Research Group IC/ASIC Functional Verification Trend Report”. <https://resources.sw.siemens.com/en-US/white-paper-2024-wilson-research-group-ic-asic-functional-verification-trend-report/>
8. Siemens EDA, “Applications of AI/ML in Functional Verification”. <https://blogs.sw.siemens.com/eda-support/2024/12/12/applications-of-ai-ml-in-functional-verification/>
9. Mark Chen et al., “Evaluating Large Language Models Trained on Code”, arXiv:2107.03374, 2021. <https://arxiv.org/abs/2107.03374>
10. Anthropic, “Building effective agents”, 2024. <https://www.anthropic.com/engineering/building-effective-agents>
