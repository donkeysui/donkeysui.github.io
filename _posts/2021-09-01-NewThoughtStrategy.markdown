---
author: donkey sui
comments: true
date: 2022-04-07 11:00:00+00:00
layout: post
title: Some mimics
subtitle:
categories:
- strategies
---

## 过小的Bid-Ask Spread
对于做市商业务，与传统市场的不同点在于加密货币的spread收益实在太小，以国内期货市场的甲醇为例，在此时此刻甲醇期货的ticksize大概是2bp，而币安BTC正向合约的ticksize大概是0.0024bp，如此悬殊的ticksize差距，就使得所谓的"capture spread"变得不可能，如果用传统市场上那一套inventory的思路来做加密货币高频，那会失败的很彻底，因为在ticksize+rebate上赚的钱是不可能cover informed对手盘损失的。 因此做市业务一定要把重心从提供流动性上转移，要做点别的事情来赚到流动性的钱，而不是capture spread。

## 过大的数据量
在国内的二级市场从业者中，很难想象数字货币的交易数据到底多到了什么程度：24h开盘，逐tick推送quotes & trades。和国内市场不足十小时的开盘时间，一秒四跳是没办法比的。数据多有好处、也有坏处。好处就是数据多暴露出来的信息就多，就更容易让有能力挖掘其中信息的人获益，坏处就是从技术层面上获取和存储、转移数据的难度会变大，而且鉴于目前交易所的基建之差、与交易所之间不统一的数据接口，也给交易者增添了很多麻烦：要考虑服务端的稳定性、要处理本地数据繁多复杂的ETL问题。 这种蛮荒，站在2022年来看，一定是好事情。

## 有效性?
市场的有效性是高是低？这问题一度困惑我，在我现在的视角来看，这个市场的有效性呈现出很奇怪的模式，就是简单的无风险套利策略填充了底层的大量有效性，但这种填充又不是那么彻底：仍然有很多很简单的策略能够在市场上每天赚到大量的钱。而另一方面，传统市场的CTA策略和事件驱动策略表现不是那么良好（来自同行的经验分享）。因为spread小，所以使用best ask以及best bid进行mid-price改良版建模是个几乎没意义的事，因为如果只用best bid和best ask建模，改良版的mid-price只能在mid-price以及weighted-mid-price之间，但就算是把imbalance拉满，wmp和mp的差别顶多也就是ticksize/2, 近似可以认为是没有。 用last trade和多档深度、结合Roll's Model做个综合性的fair price建模可能有点用？
