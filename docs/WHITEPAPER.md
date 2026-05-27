# DSL-PROT
### Deterministic Sovereign Ledger Protocol · 确定性主权账本协议

[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)]()
[![Architecture: Hardware-Gated](https://img.shields.io/badge/Architecture-Hardware--Gated-darkred.svg)]()
[![Status: Specification-Draft](https://img.shields.io/badge/Status-Specification--Draft-blue.svg)]()
[![Paradigm: Flow-Driven Minting](https://img.shields.io/badge/Paradigm-Flow--Driven%20Minting-darkgreen.svg)]()
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20404842.svg)](https://doi.org/10.5281/zenodo.20404842)
---

> *A monetary system is not a political instrument. It is a state machine.*  
> *货币系统不是政治工具，它是一台状态机。*

---

## Abstract · 核心摘要

DSL-PROT 定义了一种从零开始的范式重构：**货币不是被印出来的，它是从真实经济流动中涌现出来的。**

本协议彻底剥离传统"主观信用 + 债务驱动"的印钞发行逻辑，将货币重新定义为：

> **跨越物理空气间隙的确定性数字权利流水（Deterministic Digital Rights Transit）**

通过三项核心机制，DSL-PROT 在物理逻辑层根绝了传统货币体系的三大痼疾：

| 痼疾 | 传统体系 | DSL-PROT |
|---|---|---|
| 通货膨胀 | 央行盲目增发，购买力被动稀释 | 增量仅由真实流转水印密度涌现 |
| 金融腐败 | 现金天然匿名，离线不可追踪 | 任何实物化行为永久锁死在硬件账本 |
| 金融食利 | 银行凭借信用创造无中生有 | 硬件门禁拒绝一切无水印背书的增发 |

---

## 1. Core Architecture Topology · 核心架构拓扑

```
┌─────────────────────────────────────────────────┐
│           SOVEREIGN LEDGER CORE                 │
│         物理逻辑锁死 · Append-Only              │
│   Hardware-Gatekeeper · 单向递增拓扑结构        │
└────────────────────┬────────────────────────────┘
                     │
         ┌───────────┴───────────┐
         ▼                       ▼
┌────────────────┐     ┌──────────────────────┐
│  生活账户       │     │  大额交易账户          │
│  Daily Account │     │  High-Value Account  │
│  高频·低额·     │     │  低频·高额·           │
│  轻量级水印     │     │  强硬件门禁审计        │
└────────────────┘     └──────────┬───────────┘
                                  │
                                  ▼ (提款请求 · Withdrawal Request)
                     ┌────────────────────────────┐
                     │   物理渲染终端 (新型ATM)     │
                     │   Physical Render Terminal  │
                     │   边缘节点 · Physical        │
                     │   Extension Layer           │
                     └────────────┬───────────────┘
                                  │
                                  ▼
                     ┌────────────────────────────┐
                     │  一次性PUF实物凭证           │
                     │  One-Time Physical Token    │
                     │  时间戳 · 数字签名 · 微观PUF │
                     └────────────────────────────┘
```

### 1.1 架构哲学：为何ATM属于Physical Extension Layer

ATM渲染终端不是独立的边缘计算节点，而是**主链物理主权的末梢延伸**。

它的唯一功能是：将数字账本中已经锁定的权利，在物理维度渲染为一次性通行证。这张纸不是货币本体，它是**数字权利跨越空气间隙的临时物理渡桥**，完成使命后即消亡。

将其归类为 Physical Extension Layer 而非独立 Edge Node 的原因：

- 渲染行为本身即是主链状态转移的一部分（资金同步进入 Escrow 锁定状态）
- ATM 芯片的 PUF 根密钥由主链硬件节点签发，物理上不可脱离主链独立运作
- 终端被劫持或物理破坏时，主链硬件门禁自动触发该节点失效

---

## 2. State Machine Specification · 状态机完整规范

货币在本系统中的存在形态，严格服从以下状态机约束：

```
[ACTIVE · 活跃数字状态]
       │
       │ 提款指令 (Withdrawal Command)
       ▼
[ESCROW · 在途锁定状态]
       │  同步渲染 PUF 实物凭证 + 时效窗口 τ = 24h 开始倒计时
       │
       ▼
   ~~物理空气间隙~~
   (Physical Air Gap Transit)
       │
       ├──────────────────────────────────────────────────────┐
       │ 商户联网扫描核销                                      │ 超过 24h 未核销
       ▼                                                      ▼
[LIQUIDATED · 核销完成]                            [DISPUTED · 争议缓冲池]
  资金注入商户 ACTIVE 账户                            资金保持 ESCROW 锁定
  凭证 ID → EXHAUSTED (永久作废)                      开启双层时效窗口
                                                              │
                                          ┌───────────────────┴───────────────────┐
                                          │ 72小时内                              │ 7天满
                                          ▼                                       ▼
                                 [RENEWAL · 续期协议]                  [ARBITRATION · 仲裁分叉]
                                   上传地理/物理灾害上下文               扫描凭证物理老化特征
                                   系统自动验证并重开窗口                读取微粒衰减·折痕熵增
                                                                                  │
                                                              ┌───────────────────┴───────────────────┐
                                                              │ 确认真实持有                          │ 欺诈/双方沉默
                                                              ▼                                       ▼
                                                    资金强制划转商户账户                    永久封禁 · 资金冻结入主权托管
```

### 2.1 货币增量涌现方程

单位时间 Δt 内系统活跃财富增量 ΔM，由流转水印密度与核销销毁率协同决定：

```
ΔM(t) = α · V_watermark(t) - β · D_burn(t)

V_watermark : 单位时间内由真实生产消费触发的有效流水水印密度
D_burn      : 物理凭证单次核销后的枯竭销毁率
α, β        : 系统校准参数（由历史流水拓扑图自适应调整）
```

**核心约束：ΔM 不由任何行政意志决定，只由已发生的真实流动涌现。**

---

## 3. Physical Token Specification · 一次性物理凭证规范

### 3.1 PUF渲染流程

ATM终端在接收提款指令瞬间，执行以下不可逆合成：

```
输入参数：
  T_absolute   = 绝对时间戳（精度 ≤ 1ms）
  σ_user       = 用户数字签名
  φ_medium     = 介质微观物理特征采样（随机纤维几何·光敏微粒分布）

输出：
  PUF_hash = H(T_absolute || σ_user || φ_medium)

性质：
  · 不可逆：时间窗口关闭后，相同参数组合物理上无法再现
  · 不可克隆：φ_medium 依赖介质随机物理态，扫描即破坏原态
  · 不可重放：T_absolute 具有全局唯一性
```

### 3.2 强制单次核销约束

> **PROTOCOL CONSTRAINT:**  
> 物理凭证不具备二次流通合法性。  
> 商户接收凭证后须在 τ = 24h 内完成核销。  
> 核销后凭证 ID 在主链永久标记为 `EXHAUSTED`，任何重复提交均被硬件门禁拒绝。

### 3.3 热力学仲裁协议

当不可抗力导致核销超时，系统**拒绝引入第三方预言机（Oracle）**，转而以物理世界的客观规律自证：

> 时间流逝在物理介质上留下的熵增痕迹（粒子衰减、折痕分布），是宇宙底层的确定性规则。  
> 任何权力都无法对其进行重放攻击（Replay Attack）或恶意篡改。  
> **物质本身即预言机。**

---

## 4. Boundary Defense Grid · 边界防御矩阵

面对外部泡沫经济体的渗透攻击，边境清算节点（Border Escrow Node）构建物理不兼容壁垒：

| 攻击向量 | 传统体系脆弱点 | DSL-PROT 物理防御根基 |
|---|---|---|
| 热钱·虚空购买力冲击 | 汇率随市场情绪被动波动 | 边境隔离舱仅承兑与真实商品交割流水对等的"流转水印当量"，虚空购买力在关卡前自动蒸发 |
| 高杠杆金融衍生品渗透 | 监管套利，虚构信用放大风险 | 硬件门禁写保护：合约名义价值超出双方历史流水背书总量时，芯片寄存器物理拒绝写入 |
| 汇率做空·狙击战 | 预期可被操纵，信心可被摧毁 | 涌现式动态定价：汇率是历史已发生流水的数学函数，不存在"预期缝隙"供攻击者着力 |
| 康蒂隆效应·货币源头收割 | 离印钞机最近者优先享受新增购买力 | 增量从流动本身涌现，没有任何人比任何人更靠近源头 |

### 4.1 边境翻译机逻辑

```
外部货币进入边境清算舱
        │
        ▼
唯一问题：这笔钱背后对应多少真实物质交付？
        │
        ▼
剥离泡沫溢价部分 → 只承认实物对应的流转水印当量
        │
        ▼
按水印当量换算后注入内部账本
```

> 热钱的本质是没有实物对应的数字。  
> 在这个翻译关卡前，它自动失去虚空购买力。

---

## 5. Architectural Philosophy · 架构哲学

### 5.1 银行的物种级变异

传统商业银行依赖两根柱子：**物理现金垄断** 与 **信息不对称套利**。

在 DSL-PROT 体系中，这两根柱子同时被拆掉：

```
传统体系权力结构：
  中央银行 → 商业银行 → 市场
  （货币从上而下注入，离源头越近越占优势）

DSL-PROT 权力结构：
  市场流转 → 流水水印 → 增量自涌现
  （货币从流动本身产生，没有人拥有特权源头）
```

银行退化（或进化）为三种新物种：

1. **硬件信任节点运营商** — 保管物理信任而非保管钱，依赖热力学定律与密码学硬逻辑
2. **流动性精算师** — 对"未来流水水印置信度"进行风险定价，不创造货币
3. **数字主权托管商** — 纯服务业，管理账户密钥与继承权限，不碰货币本体

### 5.2 根本护城河

> 外部泡沫经济体的核心武器是"主观预期"。  
> 索罗斯式金融攻击需要找到"市场相信未来会怎样"的缝隙。  
>  
> DSL-PROT 的根本防御不是更强的管制，而是**从设计上消灭可供投机的主观性空间**。  
>  
> 外部打来的是主观信用炮弹。这堵墙是物理事实砌的。**炮弹穿不透。**

---

## 6. Contributing · 参与共建

欢迎硬件安全架构师、密码学者、分布式系统工程师与货币理论研究者参与共建。

本仓接收针对以下模块的技术提案（PR）：

- `/hardware/puf-render/` — 物理不可克隆函数现场渲染算法优化
- `/core/state-machine/` — 双账户隔离与争议缓冲池状态转换逻辑
- `/core/watermark-ledger/` — 流水水印密度计算与增量涌现方程校准
- `/border/escrow-node/` — 边境流转水印当量换算精度提升
- `/spec/thermodynamic-arbitration.md` — 物理熵增特征读取算法

**提案要求：**
> 不要用解释的口吻，要用**定义标准**的口吻。  
> 所有提案须附带状态转移约束方程或硬件逻辑描述。  
> 拒绝接受任何引入第三方可信度来源（Oracle）的设计。

---

## Citation · 引用

If you reference this specification, please cite the archived version:

> SiliconForge / maomaoati-coder. (2026). *DSL-PROT: Deterministic Sovereign Ledger Protocol v1.0*. Zenodo. https://doi.org/10.5281/zenodo.20404842


## 7. License · 许可协议

本规范文档以 **MIT License** 开源发布。

---

*DSL-PROT — 让货币回归它本来应该是的东西：一台运行在物理规则上的确定性状态机。*

*Make money what it always should have been: a deterministic state machine running on physical law.*
