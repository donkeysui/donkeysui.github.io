---
layout: post
title:  "高频因子是怎么回事 —— 正确的、错误的、与瞎编的"
date:   2022-09-26 00:25:39 +0000
categories: jekyll update
---

本文主要描述我在过去一年里对微观结构的错误研究（指被实证推翻的直觉、假设或道听途说来的流言）。写这篇文章的直接原因是我在过去一段时间里发现了一些新的结论，导致我对过去的一些做法产生怀疑、并一点点通过实证证伪（做减法），筛选出了暂时比较好用的方法，而这些被证伪的方法论我很长时间内都不会再用了，所以这种单纯的分享不会给我个人带来负面后果。

而被（暂时）证伪的方法论和结论并不是完全没有价值的，至少能给圈外对高频交易感兴趣的人提供厕上读物。

## 数据结构
本人目前使用的数据结构只有orderbook snapshot与逐笔trade，orderbook数据共记录十档（ask price 1-10, bid price 1-10, ap1-10 & bp 1-10 for short），trade就没什么可说的，包含交易价格、交易量、主动买入/卖出方向、时间戳。

## 正文呓语
想要通过大力出奇迹得到靠谱模型的尝试大概率结局是发现自己浪费了粮食和电、与自己并不精彩的青春。我对此事的理解是，如果你想从一段充满噪音的价格序列中识别出某种规律，那么你总是能找到一个让你血液冲头、心飘会所的“规律”。而这种规律大概率是p-value hacking产物。

那我多加几个测试集，做K-Fold检验行不行呢？ 当然可以，我们先抛开价格的时序性问题，即不能用明天的数据训练来预测昨天的数据（因为这在不能穿越时空的情况下根本不可行，所以可能会产生无意义的结果），有时间机器的大私募可以无视这个问题。加测试集的结果可能会是更低频率触发p-value hacking，导致你浪费了更多的粮食、电与并不精彩的青春。

波动率小的时候我就挂的近一点，波动率大的时候我就挂的远一点，所谓做市，就是把波动率小的单子挂的近近的、波动率大的挂的远远的。这纯粹是理想主义者的空想与错误归纳，事实上就如同明末蜀地的农民心想他妈的这帮人逃什么难呢，所谓明末生存法则，不过就是李闯王来了我藏粮食嗯造、大西王来了我尽数交粮，这衣民在轮回了几百个存档之后觉得他妈的老子真是天下第一聪明，明末生存都让老子玩明白了。但更可能发生的情况是，李闯王来了你藏粮食嗯造没问题，但是当大西王来的时候，不仅大西圣帝要尽数收走你的钱粮，还要顺手夹爆你的头。

但不是所有的流民都能认识到大西王到底有多残暴，对于有的人来说，既然在闯王来的时候我嗯造的粮食能cover丢条命的风险，那我就得过且过这么过着呗，带着老婆孩子润那是麻烦上天的狗屁倒灶。

在微观的挂单交易情况下，挂单做多和挂单做空不是完全相反的，这就如同亢米主义中主张的：“越南京族无厂者与美利坚爱尔兰裔五厂者是一家人，五厂者是没有祖国的”，相反地，挂单做多和吃单做多之间的差别可能超乎一般人的想象，尽管民族主义者主张无论吃单挂单，能做多的都是一家人。挂单做多和挂单做空之间的相似性甚至比挂单做多和吃单做多要紧密的多。我的经验是，对于market maker问题，要抓住主要矛盾，判断该挂还是不该挂比判断该多还是该空要重要得多。

在maker-taker结构的市场中（尤其是给maker提供rebate的），手续费一定要作为spread的一部分被考虑，比如计算fair price和total variation的时候，在手续费显著高于ticksize时，mid price的变化就开始“失真”，在这种情况下，tob的挂单信息本身就是价格的“一部分”。对于这种tob的imbalance能预测未来的“价格变化”这件事，其实质是imbalance本身就是“价格”，而如果只把mid price当成真正的价格去预测，那无疑会丢掉很多信息/掺入很多杂音。但是人的心理是很复杂的，不是所有的人都能接受“价格”不是实实在在看得到的float64，而是一个高维矩阵这件事的。对于有的人来说，让他接受他妈的price不是一个float64这一观念比让他相信资本主义是个骗局这事还难。

关于maker-taker结构市场中的“手续费”问题，在很多P宗的文章里，手续费和返佣是一样多的，也就是假设韭菜/庄家的手续费全部给了做市商，这显然是不可能的，有相当大的一部分钱要被拿去润滑不同的人，taker fee > maker fee是必然的。在计算real spread(也就是加上手续费的spread）时就要考虑到底用taker fee还是用maker fee，还是选个参数把两者融合起来。

预测涨跌和赚钱之间的距离随着交易频率的提高会变得越来越远，在小时、日级别上的价格预测基本上就等于躺床收米，但在毫秒、秒级别上的“价格”预测就容易出现测不准的情况，即你预测你的、我亏我的。

从经验上来讲，对于高频下的预测问题，预测该不该下单比预测往哪边下单更重要。

人的判断与愿望是没有办法分离的，人总会偏向于自己的愿望做出判断，如果人想要做到不自欺，让自己的预期更贴近于真实情况，往往要想明白自己的愿望是什么，然后在自己的判断中减去一部分愿望的成分。

人是未必能够意识到自己赚的钱是怎么来的，但是人天生有归纳的心理倾向。如果一个人赚了钱，那么他总要找个单一理由来让自己感到安心：我天天印钱，首先和我每天早起晚睡的自律情况有关、其次就是我是个很聪明的人、我是人精、人尖子、我是四海之皇、是东方的明珠、大家都想不出这么优秀的策略和做法、我的策略跑起来时，我必让它显为圣、显为大、在多种标的上赚钱，到时候他们就知道我是驴可汗/芭比球/Marcoz/其他。

接上条，人未必能意识到自己的alpha是哪里来的。市场的淘汰机制和自然界的淘汰机制是有相似之处的，雄性智人未必能显式地意识到有洞就插是最优策略，也未必能意识到吃烧烤会比吃生肉更有利（减少寄生虫、酯类有机物促进大脑发育等）。但是最后爱吃烧烤的智人淘汰掉了不爱吃烧烤的智人，至于爱吃烧烤这件事，完全是随机进化出来的，和人的主观能动性没半毛钱关系。在复盘阶段，智人会发明一堆理论来证明自身的存在合理性，比如是上帝/安拉/腾格里/女娲/湿婆按照自己的形象制造了人，人是万物的灵长，但其实现在的智人存在的理由可能只是源自爱吃烧烤、爱插大洞与贪生怕死。而策略也是一样的，人未必能找到自身真正的alpha来源——也许根本就不可能找到，但这不影响智人遍布全球烧烤大洞嗯造。

得过且过的产业中不容许有本体论式的推断和总结。

一个所谓Quant到底是坐在电脑前面研究数据、借此来洞悉市场（至少是市场的一个部分）、对市场上的其他参与者形成事实上的经济奴役关系，还是浪费粮食和电、拿着工资骗老板、骗投资人、骗自己这两件事之间是完全没有办法区分的。

## 高频因子（有用的、没用的与瞎编的）

1. 高频因子基本上就是这么一回事：用orderbook和trades的静态信息与时序信息进行组合，写成一个函数，该函数接受orderbook和trades的窗口信息，然后返回一个float64作为特征值，然后可以拿这个特征值与目标值target计算pearson相关性（初级的做法），我看有些同行还用permutation test来做，但permutation test有其本身的问题，就是permutation test忽视了连续因子值的相似性。
2. 比如对于一个因子A，假设该因子遵守0到99的均匀分布，且该因子有如下性质：A的整数部分是奇数时与目标正相关、相反时是负相关。这个因子是我编造出来借以说明的极端情况，我们可以试想如果拿这个因子做pearson corr检验，结果一定会很不好看，相关性会压得很低，p-value可能也会很高，但在上帝视角下这个因子可能是个非常好的因子，但如果没有合适的检验方法，我们可能一辈子都没办法真正“发现”这个因子。
3. 关于第一点中所说的连续因子值的相似性，我想表达的是这样的一种概念：与因子A相比，我们假设另一个因子B与A同分布，但B的性质是小于50时正相关，但大于50之后马上变成负相关，显然B因子也很难通过简单的pearson corr检验发现，且如果将A和B这样的因子直接丢进线性回归里嗯算，那基本上是不会有什么作用的。说回连续因子值的相似性问题，B因子的性质显然是比A因子要好得多的，如果任取一段连续的因子值，B取到性质相同的片段概率比A大得多。这就使得B因子更容易被非线性的学习器所学会，而A可能陷入学习器不努力学不会/学习器太努力过拟合的困境。
4. 关于上面所说的B因子，我是可以举出一个例子的，比如任意一边挂单在一段时间内的总挂撤单量问题，我曾经在某instrument上观察过这样的一种现象：买方挂单陡增时更可能伴随“价格”上升，买方挂单少量减少时可能伴随“价格”下降，而买方挂单大量撤退时又反过来倾向于“价格上升”。
5. 大量堆因子尝试提高metrics可能是LSTM流派的变体，在多年里我们一直能观察到有行外人提问LSTM对价格的预测能力，然后评论区一突开啊，满脸是“要预测return而不是price，这根本没用”。我认为预测价格应该是每一个学过LSTM的人的第一直觉，如果一个人学了LSTM去预测价格、那他不会有什么收获，如果一个人学了LSTM而想不到拿这个去预测价格，那他压根就没有脑子。有不少同行仍然在堆因子，试图越来越准确的预测“价格”的变化，这一做法看起来是符合直觉的：即我把各种各样的市场维度都整理好，一股脑喂给学习器，然后让学习器努力，我躺着收米，学习器比我聪明得多，能发现市场中各种各样的隐藏规律，根据弱学习器原理（还是定理来着，忘记了），对于集成模型，我只要弱特征嗯喂，学习器就会越来越强，非常好工作，爱来自昌南。我对这种做法的合理性存疑，我不能说这种方法一定不行，这超出了我的判断能力。
6. 微观结构中微不足道的改变带来的可能是翻天覆地的结果变化，很多条件的变化都是高度非线性的，比如如果做市商挂单一秒就撤单、与挂单两秒才撤单可能产生完全不同的PNL分布情况。
7. 静态的因子信息太少了，如果想深入去做，最好从时序上去下功夫。（这是我的空想，我对此条不负责任）

## 我因子库中的部分因子

这些因子基本都没什么用，只是偶尔有几个我觉得比较好，但我也不会特别点出来。

至于这些因子都是用来干什么的，反正不是用来预测涨跌的。

```python
def inf_ratio(depth=None, trade=None, n=100):
    quasi = trade.p.diff().abs().rolling(n).sum().fillna(10)
    dif = trade.p.diff(n).abs().fillna(10)
    return quasi / (dif + quasi)
 
def depth_price_range(depth=None, trade=None, n=100):
    return (depth.ap1.rolling(n).max() / depth.ap1.rolling(n).min() - 1).fillna(0)

@nb.jit
def get_age(prices):
    last_value = prices[-1]
    age = 0
    for i in range(2, len(prices)):
        if prices[-i] != last_value:
            return age
        age += 1
    return age

def (ask/bid)_age(depth, trade, n=100):
        bp1 = depth['bp1']
        bp1_changes = bp1.rolling(n).apply(get_age, engine='numba', raw=True).fillna(0)
        return bp1_changes

def arrive_rate(depth, trade, n=300):
    res = trade['ts'].diff(n).fillna(0) / n
    return res

def cofi(depth, trade):
    a = depth['bv1']*np.where(depth['bp1'].diff()>=0,1,0)
    b = depth['bv1'].shift()*np.where(depth['bp1'].diff()<=0,1,0)
    c = depth['av1']*np.where(depth['ap1'].diff()<=0,1,0)
    d = depth['av1'].shift()*np.where(depth['ap1'].diff()>=0,1,0)
    return (a - b - c + d).fillna(0)

def bp_rank(depth, trade, n=100):
    return ((depth.bp1.rolling(n).rank())/n*2 - 1).fillna(0)

def ap_rank(depth, trade, n=100):
    return ((depth.ap1.rolling(n).rank())/n*2 - 1).fillna(0)

def price_impact(depth, trade, n=10):
    ask, bid, ask_v, bid_v = 0, 0, 0, 0
    for i in range(1, n+1):
        ask += depth[f'ap{i}'] * depth[f'av{i}']
        bid += depth[f'bp{i}'] * depth[f'bv{i}']
        ask_v += depth[f'av{i}']
        bid_v += depth[f'bv{i}']
    ask /= ask_v
    bid /= bid_v
    return pd.Series(-(depth['ap1'] - ask)/depth['ap1'] - (depth['bp1'] - bid)/depth['bp1'], name="price_impact")

def depth_price_skew(depth, trade):
    prices = ["bp5", "bp4", "bp3", "bp2", "bp1", "ap1", "ap2", "ap3", "ap4", "ap5"]
    return depth[prices].skew(axis=1)

def depth_price_kurt(depth, trade):
    prices = ["bp5", "bp4", "bp3", "bp2", "bp1", "ap1", "ap2", "ap3", "ap4", "ap5"]
    return depth[prices].kurt(axis=1)

def rolling_return(depth, trade, n=100):
    mp = ((depth.ap1 + depth.bp1)/2)
    return (mp.diff(n) / mp).fillna(0)

def buy_increasing(depth, trade, n=100):
    v = trade.v.copy()
    v[v<0] = 0
    return np.log1p(((v.rolling(2*n).sum() + 1) / (v.rolling(n).sum() + 1)).fillna(1))

def sell_increasing(depth, trade, n=100):
    v = trade.v.copy()
    v[v>0] = 0
    return np.log1p(((v.rolling(2*n).sum() - 1) / (v.rolling(n).sum() - 1)).fillna(1))

@nb.jit
def first_location_of_maximum(x):
    max_value=max(x)#一个for 循环
    for loc in range(len(x)):
        if x[loc]==max_value:
            return loc+1
        
def price_idxmax(depth, trade, n=20):
    return depth['ap1'].rolling(n).apply(first_location_of_maximum,engine='numba',raw=True).fillna(0)

@nb.jit
def mean_second_derivative_centra(x):
    sum_value=0
    for i in range(len(x)-5):
        sum_value+=(x[i+5]-2*x[i+3]+x[i])/2
    return sum_value/(2*(len(x)-5))

def center_deri_two(depth, trade, n=20):
    return depth['ap1'].rolling(n).apply(mean_second_derivative_centra,engine='numba',raw=True).fillna(0)

def quasi(depth, trade, n=100):
    return depth.ap1.diff(1).abs().rolling(n).sum().fillna(0)

@trade_to_depth
def last_range(depth, trade, n=100):
    return trade.p.diff(1).abs().rolling(n).sum().fillna(0)

@trade_to_depth
def arrive_rate(depth, trade, n=100):
    return (trade.ts.shift(n) - trade.ts).fillna(0)

@trade_to_depth
def avg_trade_volume(depth, trade, n=100):
    return (trade.v[::-1].abs().rolling(n).sum().shift(-n+1)).fillna(0)[::-1]

def avg_spread(depth, trade, n=200):
    return (depth.ap1 - depth.bp1).rolling(n).mean().fillna(0)

def avg_turnover(depth, trade, n=500):
    return depth[['av1', 'av2', 'av3', 'av4', ....., 'av10', 'bv1', 'bv2', 'bv3', 'bv4', ....., 'bv10']].sum(axis=1)

@trade_to_depth
def abs_volume_kurt(depth, trade, n=500):
    return trade.v.abs().rolling(n).kurt().fillna(0)

@trade_to_depth
def abs_volume_skew(depth, trade, n=500):
    return trade.v.abs().rolling(n).skew().fillna(0)

@trade_to_depth
def volume_kurt(depth, trade, n=500):
    return trade.v.rolling(n).kurt().fillna(0)

@trade_to_depth
def volume_skew(depth, trade, n=500):
    return trade.v.rolling(n).skew().fillna(0)

@trade_to_depth
def price_kurt(depth, trade, n=500):
    return trade.p.rolling(n).kurt().fillna(0)

@trade_to_depth
def price_skew(depth, trade, n=500):
    return trade.p.rolling(n).skew().abs().fillna(0)

def bv_divide_tn(depth, trade, n=10):
    bvs = depth.bv1 + depth.bv2 +  ... + depth.bv10
    @trade_to_depth
    def volume(depth, trade, n):
        return trade.v
    v = volume(depth=depth, trade=trade, n=n)
    v[v>0] = 0
    return (v.rolling(n).sum() / bvs).fillna(0)

def av_divide_tn(depth, trade, n=10):
    avs = depth.av1 + depth.av2 +  ... + depth.av10
    @trade_to_depth
    def volume(depth, trade, n):
        return trade.v
    v = volume(depth=depth, trade=trade, n=n)
    v[v<0] = 0
    return (v.rolling(n).sum() / avs).fillna(0)

def weighted_price_to_mid(depth, trade, levels=10, alpha=1):
    def get_columns(name, levels):
        return [name+str(i) for i in range(1, levels+1)]
    avs = depth[get_columns("av", levels)]
    bvs =  depth[get_columns("bv", levels)]
    aps = depth[get_columns("ap", levels)]
    bps =  depth[get_columns("bp", levels)]
    mp = (depth['ap1'] + depth['bp1'])/2
    return (avs.values * aps.values + bvs.values * bps.values).sum(axis=1) / (avs.values + bvs.values).sum(axis=1) - mp
   
@nb.njit
def _ask_withdraws_volume(l, n, levels=10):
    withdraws = 0
    for price_index in range(2,2+4*levels, 4):
        now_p = n[price_index]
        for price_last_index in range(2,2+4*levels,4):
            if l[price_last_index] == now_p:
                withdraws -= min(n[price_index+1] - l[price_last_index + 1], 0)
        
    return withdraws

@nb.njit
def _bid_withdraws_volume(l, n, levels=10):
    withdraws = 0
    for price_index in range(0,4*levels, 4):
        now_p = n[price_index]
        for price_last_index in range(0,4*levels,4):
            if l[price_last_index] == now_p:
                withdraws -= min(n[price_index+1] - l[price_last_index + 1], 0)
        
    return withdraws

def ask_withdraws(depth, trade):
    ob_values = depth.values
    flows = np.zeros(len(ob_values))
    for i in range(1, len(ob_values)):
        flows[i] = _ask_withdraws_volume(ob_values[i-1], ob_values[i])
    return pd.Series(flows)

def bid_withdraws(depth, trade):
    ob_values = depth.values
    flows = np.zeros(len(ob_values))
    for i in range(1, len(ob_values)):
        flows[i] = _bid_withdraws_volume(ob_values[i-1], ob_values[i])
    return pd.Series(flows)
```

