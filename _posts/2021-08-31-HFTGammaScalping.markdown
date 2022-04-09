author: donkey sui
comments: true
date: 2022-08-31 11:00:00+00:00
layout: post
title: 高频Gamma Scalping现实可行性
subtitle:
categories:

- strategies

---

## Gamma Scalping

Gamma Scalping，刮毛，薅羊毛。用动态Delta 对冲挣Gamma 的钱。高抛低吸是交易者追求的，但在实际操作过程中做成了追涨杀跌。Gamma Scalping指的就是在持有正Gamma头寸的情况下进行高抛低吸，可以看作是一种用时间换空间的行为。
$$
\Gamma = \frac{d\Delta}{dS}
$$
__传统方式的Gamma Scalping__

在传统的、书本式对Gamma Scalping的介绍中，一般偏向于

- 采用按照**固定时间间隔**进行对冲的方式。
  - 选取一段特定的时间长度
  - 在每个interval的开始和结束检查自己的delta头寸
  - 如delta的绝对值超出了自己的容忍区间，就进行一次对冲。

收益情况见该公式：
$$
\Delta PNL = \frac{1}{2} S^2\Gamma * ((\frac{\Delta S}{S})^2 - \sigma_i^2 \Delta t)
$$


## 引入手续费的情况

现实世界中的交易手续费是所有高频策略都不能忽视的，因为正Gamma头寸的对冲方式是高抛低吸，故我们使用

__定义每次对冲的网格距离为__i_m
$$
i_m = \frac{\Delta S}{S}
$$
每次对冲交易一次，即计算一次maker手续费，结果如下。
$$
Gamma PNL = \frac{1}{2}\Delta S^2 \Gamma - \frac{\Delta S^2\Gamma f}{i_m} \\ =\Delta S^2 \Gamma(\frac{1}{2} - \frac{f}{i_m})
$$
我们容易发现，手续费支出与**佣金费率成正比**、与**网格距离成反比**。这一点是符合直觉的，因为佣金费率和手续费必然是正比线性关系，而交易的越频繁，手续费的支出就越难覆盖Gamma部分的收益。

我们容易发现，当f与i_m的比值大于0.5时，无论如何波动，我们都无法获取到任何正收益了。

当下式成立时，可以获得正收益：
$$
(1-\frac{2f}{i_m})(\frac{\Delta S}{S})^2 > \sigma^2_i \Delta t
$$
也可以写作：
$$
(1-\frac{2f}{i_m})\sigma_r^2 > \sigma^2_i
$$

### Crypto Market Maker Rebate

在如今的Crypto市场中，做市者（广义的以挂单形式交易的人）容易拿到高额的rebate，

- Binance USDT contract: 0.3bps  — 0.5bps
- OKX USDT contract: 1bps
- Bybit USDT contract: 2.5bps

可以发现，当交易所提供rebate，且对冲网格距离极小时，1 - 2f/im项就会变得极大，这允许交易者在实际波动率不大的情况下，仍然存在获得正收益的可能性。

__一些限制__

- DollarGamma > LotSize / TickSize
- MakerFee < 0

__可能遇到的问题__

- 如何选取ATM option获得正Γ
- Gamma Bleeding
- Pin Risk

### 执行策略

<img src="/assets/2021-08-31/trading_pseudo.png" width="800" height="750" />