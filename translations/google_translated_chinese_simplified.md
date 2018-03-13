![image](https://steemit-production-imageproxy-upload.s3.amazonaws.com/DQmaZMBrigYVABUcRUqwJXgMYFyqvmmbUcWi73JxSpWDGa9)

# 'Hertz - An Oscillating USD pegged Algorithm Based Asset' (赫兹 - 振荡美元挂钩的基于算法的资产)

## Preface

I've converted the Latex/PDF Hertz whitepaper to markdown & will produce google-translations of the following post. Please reply if you don't understand anything or if you think any part of the whitepaper is not explained well, missing or incorrect. Thanks!

本文是赫兹白皮书的谷歌翻译降价版本，如果您不明白任何内容，请回复，如果没有任何意义（不好翻译），请提问或甚至提出翻译建议，谢谢！

## Abstract

This paper will introduce Hertz which is an Algorithm Based Asset (ABA) which was created on the Bitshares Decentralized Exchange (BTS DEX). It’s settlement price feed is pegged to 1 USD then predictably modified to oscillate using a sine wave with 14% amplitude and a period of 28 days, thus introducing predictable phases of price feed appreciation and depreciation and a range of $0.86 to $1.14. The development process and lessons learned will also be elaborated upon so as to accelerate the implementation of future ABAs on the BTS DEX.

本文将介绍Hertz，它是在Bitshares分散交换（BTS DEX）上创建的基于算法的资产（ABA）。 它的结算价格饲料固定在1美元，然后可预测地修改为使用幅度为14％，周期为28天的正弦波振荡，从而引入价格饲料升值和折旧的可预测阶段，范围在0.86美元到1.14美元之间。 还将详细阐述发展过程和经验教训，以加速BTS DEX未来的ABA实施。

---

## Introduction

The Bitshares Decentralized Exchange (BTS DEX) provides the ability to create Market Pegged Assets (MPAs) which are price stable cryptocurrency tokens typically pegged to external reference assets and provably backed by (typically more than 175%) collateral in Bitshares.

Bitshares分散交易所（BTS DEX）提供创建市场挂钩资产（MPA）的能力，MPA是价格稳定的加密货币代币，通常与外部参考资产挂钩，并可在Bitshares获得（通常超过175％）抵押品（通常超过175％）。

Confirmation times for transactions on the BTS DEX are typically less than a couple seconds, and the BTS DEX is massively scalable[1]. MPAs can take full advantage of this high performance blockchain network at a small cost to the issuer (asset creation & smartcoin setting update fees), price feed publishers (price feed publish fees) and user (transfer fees).[7]

BTS DEX交易的确认时间通常少于几秒钟，而BTS DEX则具有极大的可扩展性[1]. 对于发行人（资产创建和智能币设置更新费用），价格出版商（价格发布费用）和用户（转账费用），MPA可以充分利用这个高性能区块链网络。[7]

The majority of MPAs created on the BTS DEX are backed by BTS and have external references (layer 1), however there are multiple possible layers of backing collateral for MPAs on the BTS DEX:

在BTS DEX上创建的大多数MPA由BTS支持并具有外部参考（层1），但是对于BTS上的MPA有多个可能的支持抵押层DEX:

| 层 | 参考资产 | 支持抵押品 | 例子 |
|---|---|---|---|
| 0 | 内部 | BTS | N/A |
| 1 | 外部 | BTS | USD, Hertz & Hero |
| 2 | 外部 | 层 1 MPA | XCD |
| n | 外部 | 层 n-1 MPA | N/A |

MPAs are issued in a decentralized manner as they’re borrowed into existence and shorted on the market without introducing counter party risk. If the shorter’s backing collateral falls below the Minimum Collateral Ratio (MCR) then the shorter is force settled, forcing them to buy back their debt at market rates to then close their debt position.[2]

海洋保护区是以分散的方式发行的，因为它们在市场上被借用并短路，而不会引入反方风险。 如果较短的支持抵押品低于最低抵押品比例（MCR），那么较短的部分就是强制结算，迫使他们按市场价回购他们的债务，然后关闭其债务头寸。[2]

MPA holders can trigger a settlement at any time which exchanges their settled MPA tokens for the equivalent backing collateral from the least collateralized short position at the current settlement price rate. This means that even if a shorter has a greater collateral ratio than the MCR, they can be force settled.[2]

MPA持有人可以随时触发结算，并以当前结算价格将最低担保空头头寸的等值支持抵押品交换其已解决的MPA代币。 这意味着即使较短的抵押比率比MCR的抵押比率更高，也可以强制结算。[2]

The settlement rate of an MPA in BTS (or an alternative backing collateral asset) is determined as the median price feed in the set of the latest published price feeds for the MPA in question.[2]

BTS中MPA的结算率（或替代支持抵押资产）被确定为所讨论的MPA的最新公布价格组的中间价格。[2]

Price feeds are regularly provided by price feed publishers such as Witnesses, Committee members or manually configured private price feed publishers. You need to reach out to prospective price feed publishers directly before they will begin publishing price feeds.

价格饲料通常由价格饲料出版商提供，例如证人，委员会成员或手动配置的私人价格饲料出版商。 您需要直接与潜在价格出版商联系，然后才能开始发布价格Feed。

Algorithm Based Assets (ABAs) are pegged to a reference asset (USD in both HERO & Hertz case) then the reference price feed is modified using a publicly available algorithm to produce predictable effects over time. Introducing an algorithm to an MPA does not affect its backing collateral layer; both Hero and Hertz are still layer 1 MPAs, however an ABA can be created at any layer.

基于算法的资产（ABA）与参考资产（以HERO和赫兹两种情况下的美元）挂钩，然后使用公开可用的算法修改参考价格Feed以产生可预测的效果。 向MPA引入算法不会影响其支持层; Hero和Hertz都是第1层MPA，但是可以在任何层创建ABA。

The primary focus of this paper is Hertz and Algorithm Based Assets on the BTS DEX.

本文的主要重点是基于赫兹和基于算法的资产在BTS DEX上。

## Related work

Hero was the first ABA on the BTS DEX, its value is pegged to the USD plus 5% appreciation per year since the FED began printing the USD back in 1913. This translates to an approximate HERO value of $160 in 2018.[6]

Hero是BTS DEX上的第一支ABA，自从FED在1913年开始印刷美元以来，其价值与美元相加，每年增值5％，这意味着2018年的HERO值接近160美元。[6]

Hero was the primary inspiration for Hertz; initially the opposite of HERO was proposed with a goal of depreciating the value x% per year however this was economic policy was perceived as undesirable for long term holders.[4] The Sine wave algorithm was proposed to combine the effects of HERO and its opposite theoretical ABA, resulting in predictable phases of both price feed appreciation and price feed depreciation every 28 days forever.

英雄是赫兹的主要灵感; 最初提出了与HERO相反的目标，即贬值每年x％的目标，然而这是经济政策对于长期持有者而言不可取的。[4] 建议使用正弦波算法，将HERO和其相反的理论ABA的效应结合起来，从而导致价格饲料升值和价格饲料贬值的可预测阶段每28天永远保持不变。

There are no examples of assets which have implemented a similar algorithm to oscillate its value like Hertz in neither the FIAT nor Cryptocurrency markets, so the concept is unique and highly experimental. There are however over the counter financial derivatives such as variance and volatility swaps which may share similar trading behaviour as Hertz.

在菲亚特和加密货币市场都没有实施类似的算法来振荡其价值的资产的例子，所以这个概念是独特的和高度实验性的。 然而，也有非处方金融衍生工具，如方差和波动率掉期交易，可能与赫兹有相似的交易行为。

Unlike variance and volatility swaps, there is no expiration on shorted positions aside from force settlement (either due to under collateralization or being the least collateralized short position upon Hertz asset settlement). The shorter’s ability to close their position depends entirely on them buying back their debt from the open market.

与方差和波动率掉期不同，除了部队结算之外，空头头寸不会到期（无论是由于抵押品下的还是Hertz资产结算时最小的抵押品空头头寸）。 短期收回头寸的能力完全取决于他们从公开市场购回债务。

Unlike established volatility/variance products like the Volatility Index (VIX) and the DJIA Volatility Index (VXD), Bitshares DEX market participants can trade Hertz directly with one another with no middlemen involved in the process. It may be worth implementing such volatility/variance products as MPAs on the BTS DEX in the future, however such proposals are out of scope from this paper.

与波动率指数（VIX）和道琼斯波动率指数（VXD）等既定波动性/波动率产品不同，Bitshares DEX市场参与者可以直接与赫兹进行交易，而无需中间商参与。 未来可能需要在BTS DEX上实施像MPA这样的波动/差异产品，但是这样的提议超出了本文的范围。

## Hertz Overview

Hertz is an Algorithm Based Asset (layer 1 MPA) which is pegged to the current USD value (in BTS) and modified to oscillate using a sine wave algorithm.[3] This section will provide more info on what Hertz is, how it was created and how you could create an alternative implementation if interested.

赫兹是基于算法的资产（第1层MPA），它与当前美元价值（在BTS中）挂钩，并修改为使用正弦波算法进行振荡。[3] 本节将提供关于赫兹是什么的更多信息，它是如何创建的以及如果您感兴趣的话可以创建一个替代实现。

### Price feed scripts

Many price feed publishers have different price feed script solution preferences thus there are three open source multi-asset price feed script repos you’ll need to provide implementations of your ABA for in order to enable the majority of BTS witnesses to publish price feeds.

许多价格出版商具有不同的价格供稿脚本解决方案偏好，因此您需要提供三个开源多资产价格供稿脚本仓库来提供您的ABA实施，以便使大多数BTS证人能够发布价格供稿。

The more price feed scripts you integrate your algorithm into, the more likely you are to recruit a greater quantity of price feed publishers for your ABA on the BTS DEX. Likewise, if your ABA price feed publishers utilize multiple price feed scripts the greater the redundancy (you don’t want one glitch stopping all price feeds).

您将算法集成到更多的价格供稿脚本中，您更有可能在BTS DEX上为您的ABA招募更多的价格供稿发布者。 同样，如果您的ABA价格Feed发布商使用多个价格Feed脚本，则冗余度越高（您不希望发生一次故障就会停止所有价格Feed）。

It’s worthwhile creating a reference price feed script which doesn’t involve the existing price feed script repos, so that if there are delays in integration you’ll have a backup option for witnesses to use.

创建一个参考价格Feed脚本是非常值得的，它不涉及现有的价格Feed脚本仓库，所以如果整合过程中出现延迟，您将有一个供证人使用的备份选项。

#### Current community created price feed scripts:

* Wackou’s BTS\_Tools (supports Hertz) [9]
* Xeroc’s bitshares-pricefeed [10]
* pch957’s btsprice [8]
* python-bitshares based reference price feed scripts [5]

### Hertz algorithm

Hertz is primarily implemented in Python across four different price feed scripts (as mentioned above). It implements a sine wave to provide a predictable oscillation to the settlement price over the course of 28 days.

Hertz主要通过四种不同价格的Feed脚本（如上所述）在Python中实现。 它实施了一个正弦波，在28天的过程中为结算价格提供可预测的振荡。

By implementing an amplitude of 14% a settlement price range of $0.86 to $1.14 is achieved. Note that the settlement price and market trading rates are not the same, it’s likely that market participants will trade above/below the settlement price depending on the ongoing oscillation.

通过实施14％的幅度，实现了0.86美元至1.14美元的结算价格范围。 请注意，结算价格和市场交易汇率并不相同，市场参与者可能会根据持续的波动交易高于/低于结算价格。

Wednesdays were chosen as the primary milestone day as this is mid working week for the UTC time zone.

周三被选为主要里程碑日，因为这是UTC时区的中期工作周。

![image](https://i.imgur.com/bxbD3cS.png)

The above image shows the Hertz settlement price in USD across the 28 day period.

以上图片显示了28天内赫兹结算价格

The following Hertz Python pseudocode will briefly demonstrate how the Hertz algorithm is implemented.

下面的Hertz Python伪码将简要地演示赫兹算法是如何实现的。

``` {frame="single"}
from math import pi, sin

period = 2419200 # 28 days in seconds 几秒钟内
hz_phase = 0.908056 # Time offset 时间偏移
amplitude = 0.14 # 14%

# Bitshares 2.0 genesis block timestamp 生成块时间戳
ref_time = "2015-10-13T14:12:24+00:00"

time_diff = timestamp - (ref_time + hz_phase)

hz_now = ((time_diff/period) % 1) * period

sin_calc = sin(hz_now * ((2*pi)/period))

hertz_value = 1 + (amplitude * sin_calc)
```

Static variables are referenced for the amplitude, reference timestamp, period, phase and reference asset. Only the current time and current ’BTS:USD’ rate (not included in code for simplicity) are dynamic, and only the ’BTS:USD’ rate is an unknown factor for all participants.

静态变量参考振幅，参考时间戳，周期，阶段和参考资产。 只有当前时间和当前的“BTS：USD”汇率（不包括在简单代码中）才是动态的，只有“BTS：USD”汇率对所有参与者来说都是未知因素。

Simply put, we estimate the current point of the sine wave and apply either a positive or negative value to the reference asset value through addition, resulting in a settlement price range of $0.86 to $1.14.

简而言之，我们估计正弦波的当前点，并通过添加对参考资产价值应用正值或负值，导致结算价格范围为0.86美元到1.14美元。

Given the MIT licensed open source licensing of the Hertz price feed scripts, it’s possible for anyone to create a new ABA with alternative static variables.

鉴于麻省理工学院授权的Hertz价格供稿脚本的开源许可证，任何人都可以使用其他静态变量创建新的ABA。

It’s highly advisable to research the impact of changing the variables and finalizing your selection prior to activation on the BTS DEX, as coordinating the modification of these static variables could prove difficult.

研究改变变量和在DEX激活之前完成选择的影响是非常明智的，因为协调这些静态变量的修改可能会很困难。

##### Modifiable static variables 可修改的静态变量:

1. 参考资产（美元，人民币，欧元..）。

2. 幅度（7％，14％，50％..）。

3. 期间（7天，14天，30天..）。

4. 波浪算法（正弦，余弦，组合..）。

5. 阶段（偏移参考/起源时间戳到一周中的特定日子）。

6. 首选时区（默认：UTC）。

7. 参考时间戳（默认：BTS2.0生成时间戳）。

##### Smartcoin settings:

1. 最低抵押比例：200％

2. 最大力量结算量：5％

3. 强制结算的百分比抵消：1％

4. 强制解决延迟：1440分钟

5. 短后盾资产：BTS

#### Surrendered Smartcoin flags/permissions

Unlike many of the current Bitshares committee controlled smartcoins (such as bitUSD), the following flags were permanently disabled in order to improve the decentralization of Hertz.

与目前Bitshares委员会控制的智能币（如bitUSD）不同，以下标志永久停用，以改善赫兹的分散化。

1.  “Require holders to be white-listed”: There will never be a white-list for holders.
1.“要求持有者被列入白名单”：持有者永远不会有白名单。

2.  “Issuer may transfer asset back to himself”: The issuer can never transfer the asset back to themselves.
2.“发行人可将资产转回自己”：发行人永远不能将资产转回自己。

3.  “Issuer must approve all transfers”: Transfers will never require approval.
3.“发行人必须批准所有转账”：转账永远不需要批准。

4.  “Disable confidential transactions”: Confidential transactions (whenever implemented) will always be allowed.
4.“禁用机密交易”：机密交易（无论何时实施）将始终被允许。

You shouldn’t be concerned that assets currently controlled by the committee could be manipulated through the above permissions, as committee members are elected by BTS holders and any such actions are highly transparent on the blockchain. The removal of these permissions simply makes such a small risk entirely impossible to improve user confidence.

您不应该担心委员会目前控制的资产可能通过上述权限操纵，因为委员会成员由BTS持有人选出，任何此类行为在区块链上都非常透明。 这些权限的删除只是使这样一个小风险完全不可能提高用户的信心。

## Predicted effects of Hertz 赫兹的预测效果

Hertz is a highly experimental smartcoin on the BTS DEX, all code is MIT licensed and price feeds are provided in a decentralized manner. This paper does not constitute financial advice. Do your research, consult financial advisors and then acknowledge the risks prior to trading Hertz.

赫兹是BTS DEX上的高度实验型智能币，所有代码都是麻省理工学院许可的，价格反馈是以分散的方式提供的。 本文不构成财务建议。 做你的研究，咨询财务顾问，然后在交易赫兹之前承认风险。

Hertz can experience unpredictable contamination of volatility through USD and BTS value exposure as well as through unpredictable market trading behaviour caused by the sine wave based price feed oscillation.

通过美元和BTS价值暴露以及通过基于正弦波的价格振荡引起的不可预测的市场交易行为，赫兹可能经历不可预测的波动性污染。

With that disclaimer out of the way, this section will detail some of the theorized effects of the Hertz ABA.

有了这个免责声明，本节将详细介绍Hertz ABA的一些理论化效果。

---

![image](https://i.imgur.com/g27KXLE.png)

### Price feed appreciation 价格上涨

The price feed value appreciates from $0.86 on day 21 (previous cycle) to $1.14 on day 7 (next cycle); this price feed appreciation benefits the Hertz holder at the expense of the shorter (their collateral ratio decreases), unless the value of BTS increases more than 28% in this time frame.

价格饲料价格从第21天（前一周期）的0.86美元升至第7天（下一周期）的1.14美元; 除非在这个时间段内BTS的价值上涨超过28％，否则这种价格上涨将有利于Hertz持有者的利益，而其代价是短期的（其抵押品比率下降）。

---

![image](https://i.imgur.com/6pkkgtl.png)

### Price feed depreciation 价格饲料折旧

The price feed value depreciates from $1.14 on day 7 to $0.86 by day 21; this price feed depreciation benefits the shorter (through temporary debt destruction & subsequent collateral ratio increase) unless the value of BTS decreases in value more than 28% in this time frame.

价格饲料价格从第7天的1.14美元贬到第21天的0.86美元; 除非在此时间段内BTS的价值减少超过28％，否则这种价格饲料折旧有利于缩短（通过暂时的债务销毁和随后的抵押品比率增加）。

---

![image](https://steemit-production-imageproxy-upload.s3.amazonaws.com/DQmU5qgVNmuCCqRDCwdFe5gmKsnsiM9JY9XiTcaKZTKqJUd)

### Peak settlement price 高峰结算价格

Day 7 of 28 is the peak settlement price after which the price feeds will begin to depreciate.

28日的第7天是最高结算价，之后价格将开始贬值。

1.  Shorters aiming to maximize benefits from price feed depreciation may produce peak selling pressure.
1.缩短旨在最大限度地从价格饲料折旧中获益的措施可能会产生高峰抛售压力。

2.  Holders producing peak settle pressure as they realize price feed appreciation gains from past hertz cycles.
2.持有者在过去赫兹周期实现价格饲料升值收益时产生高峰稳定压力。

3.  Less buy pressure, as holders will have to wait 28 days (a full cycle) before their Hertz tokens return to this rate, and they will not benefit from price feed appreciation.
3.减少买入压力，因为持有者必须等待28天（一个完整周期），然后赫兹代币恢复到此速度，并且他们不会从价格上涨中受益。

4.  The potential shorter sell wall may provide sell liquidity not currently possible with other MPAs without having an impact on the core BTS trading rate.
4.可能较短的卖出墙可能提供其他MPA目前不可能的卖出流动性，而不会影响核心BTS交易利率。

---

![image](https://steemit-production-imageproxy-upload.s3.amazonaws.com/DQmXWKCQgQwcyEH5z5EYq8bbTAAHQ1GZiJ2Wp3vWHwpiixx)

### Trough settlement price 谷底结算价格

Day 21 of 28 is the trough feed value, Highest theoretical buy pressure however Lowest predicted sell and settle pressure!

28日的第21日是谷物饲料价值，最高理论买入压力然而最低预测的卖出和解决压力！

1.  Shorters selling Hertz during the trough face impending price feed appreciation then settlement at the peak value. This may result in a lack of sell liquidity during the trough which affects shorters ability to buy back debt at trough rates.
1.在谷底期间卖出赫兹的空头面临价格逼近升值，然后达到峰值。 这可能会导致低谷期间缺乏卖出流动性，这会影响空头以低谷收回债务的能力。

2.  Shorters aiming to buy BTS during the trough to settle their debt and realize any potential debt destruction will be a likely source of predictable buy pressure.
2.缩短目标在低谷期间购买BTS以偿还债务，并意识到任何潜在的债务破坏都将成为可预见的购买压力的可能来源。

3.  Holders aiming to buy in the trough to then settle at the peak will additionally be a likely source of predictable buy pressure.
3.持有者想要在低谷中买入，然后稳定在高峰期，这将成为可预测的买入压力的可能来源。

4.  Combined shorter and holder potential buy wall may provide shorters larger trading opportunities than currently possible with traditional MPAs (without impacting BTS core price).
4.短期和持有者的潜在买入墙可能会提供比传统海运保单现有可能更大的交易机会（不会影响BTS核心价格）。

### Long term holders 长期持有人

It’s highly likely that some Hertz holders will hold their tokens long term, especially if they were to buy at the peak and have to wait until subsequent peaks to settle.

很有可能一些赫兹持有者会长期持有他们的代币，特别是如果他们在高峰期买入并且必须等到随后的高点才能结算。

It’s less likely that those who buy in a trough will hold through peaks, unless BTS is more volatile than Hertz and settling would be detrimental.

那些在低谷中买入的人不太可能持有高峰，除非BTS比赫兹更具波动性并且定居将会是有害的。

It’s possible that due to long term holding behaviour, that there may be insufficient liquidity for the shorter to buy back their debt and thus be locked into holding debt without an exit. This is potentially only a risk during the initial startup cycles until sufficient liquidity has developed throughout all phases of the hertz cycle.

由于长期持有的行为可能导致短期回购债务的流动性不足，从而无法退出而被锁定债务。 在初始启动阶段，这可能只是一个风险，直到赫兹循环的所有阶段都有足够的流动性发展。

## Future work 未来的工作

Several future goals are the following:

未来的几个目标如下：

1.  Full price feed script coverage - There is an open bounty for the implementation of Hertz in pch957’s btsprice repo, after which we will have achieved 100% BTS price feed script coverage.
1.完整的价格饲料脚本覆盖率 - 在pch957的btsprice回购中实施Hertz有一个公开的奖励，在此之后，我们将实现100％的BTS价格饲料脚本覆盖率。

2.  Maximizing the quantity of Hertz price feed publishers, preferably more than 20.
2.最大限度地提高赫兹价格出版商的数量，最好超过20。

3.  Creating alternative Hertz ABAs with different static variables.
3.用不同的静态变量创建替代赫兹ABA。

4.  Implementing an alternative calendar system than the 28 day calendar, accounting for leap years and different sized months. Only for an alternative Hertz ABA.
4.实施比28天日历更好的日历系统，考虑闰年和不同大小的月份。 仅限于替代赫兹ABA。

5.  Experiment with layer 0, 2 and n backing collateral ABAs.
5.尝试第0层，第2层和第n层支持附属ABA。

6.  Once fully operational, petition the committee to accept ownership of the Hertz ABA, so as to further improve decentralization.
6.一旦完全运作，请求委员会接受赫兹ABA的所有权，以进一步改善权力下放。

7.  Potentially creating volatility/variance product MPAs, given their popularity in the FIAT markets.
7.由于其在菲亚特市场的普及，潜在创造波动性/差异性产品海洋保护区。

## Lessons learned 得到教训

This section will quickly summarize the lessons learned during the implementation of Hertz over the course of the last year.

本部分将快速总结去年赫兹实施过程中吸取的经验教训。

1.  Communication with price feed publishers is key. You need to reach out to price feed publishers directly and help troubleshoot any issues they encounter when running your price feed scripts. It can take several months to establish a large set of price feed publishers whom are all publishing accurate price feeds.
1.与价格出版商的沟通是关键。您需要直接与定价供稿发布商联系，并帮助解决运行定价供稿脚本时遇到的任何问题。可能需要几个月的时间才能建立一套大量的价格Feed发布商，他们都会发布准确的价格Feed。

2.  You must thoroughly test the price feed scripts prior to requesting price feed publishers use them, this will save you time troubleshooting and will reduce potentially frustrating price feed publishers.
2.请求定价供应发布商使用它们之前，您必须全面测试定价供稿脚本，这将为您节省时间进行故障排除，并且可以减少价格可能会受到损害的定价供应发布商。

3.  Integrating your algorithm into existing price feed scripts can take several weeks per pull request since the repository owners are very busy individuals.
3.将您的算法集成到现有价格Feed脚本中可能需要几个星期的时间，因为存储库所有者是非常繁忙的个人。

4.  Hertz static variables must be thoroughly considered prior to integration into price feed scripts. Initially the amplitude was 50%before being reduced to 33% then finally 14%; be careful not to create too volatile an ABA! Use the spreadsheet hertz calculator to gauge static variable impact upon collateral ratios throughout a full cycle.
4.集成到价格供稿脚本之前，必须充分考虑赫兹静态变量。起初幅度为50％，然后降至33％，最后为14％;小心不要造成太易变的ABA！使用电子表格赫兹计算器在整个周期内测量对抵押比率的静态变量影响。

5.  Whilst using the testnet for testing your ABA is free, there are complexities (TEST doesn’t have a value, there’s no reference bitUSD asset) which can impede testing ABAs on testnet.
5.尽管使用testnet测试您的ABA是免费的，但是复杂性（TEST没有价值，没有参考bitUSD资产）会妨碍在testnet上测试ABA。

6.  You can easily test price feed publishing in production without any problems if you set the minimum price feed publisher quantity to a value far greater than the current price feed publisher count.
6.如果您将最低价格Feed发布商数量设置为远大于当前价格Feed发布商数量的值，则可以轻松测试生产中的价格Feed发布，而不会产生任何问题。

## Conclusion 结论

It’s possible to introduce predictable velocity to an MPA on the BTS DEX by applying a sine wave based oscillation to an USD pegged MPA resulting in phases of price feed appreciation and depreciation; such effects introduced by Hertz are highly unique and require further observation once active on the BTS DEX.

通过对美元挂钩的MPA应用基于正弦波的振荡，可以在BTS DEX的MPA上引入可预测的速度，从而导致价格升值和贬值阶段; Hertz引入的这种效果非常独特，需要在DEX上激活一次才能进一步观察。

Algorithm Based Assets like Hertz are cheap to implement, decentralized, highly collateralized and free from counter-party risk. Hopefully this paper will aid the creators of subsequent Hertz derivatives by encouraging a more streamlined development experience & Algorithm Based Assets as a result will gain more attention within BTS and the greater cryptocurrency community in the future.

基于算法的资产像赫兹这样的资产实施起来便宜，分散，高度抵押并且没有反方风险。 希望本文将通过鼓励更加简化的开发经验和基于算法的资产来帮助后续Hertz衍生产品的创造者，因此将在BTS和未来更大的加密货币社区中获得更多关注。

## References

[1] Cryptonomex Bitshares foundation. Bitshares - Industrial Performance and Scalability, 2018. URL https://bitshares.org/technology/industrial-performance-and-scalability/.

[2] cryptonomex Bitshares foundation. DEX Margin Mechanics, 2018. URL http://docs.bitshares.org/bitshares/user/dex-margin-mechanics.html.

[3] Customminer. Hertz technical documentation, 2018. URL https://sites.google.com/view/hertz-aba/.

[4] customminer. Nemesis bitsharestalk thread, 2018. URL https://bitsharestalk.org/index.php?topic=24158.0.

[5] grctest customminer. Reference Hertz Python price feed script, 2018. URL https://github.com/BTS-CM/scripts/blob/master/hertz-feed.py.

[6] Cryptonomex‘ Hero. Hero ABA details, 2018. URL http://open-explorer.io/#/assets/HERO.

[7] Bitshares commitee Oxarbitrage. Current Bitshares fees, 2018. URL http://open-explorer.io/#/fees.

[8] pch957. pch957’s btsprice Bitshares price feed scripts, 2018. URL https://github.com/pch957/btsprice.

[9] Wackou. Wackou’s BTS tools, 2018. URL https://github.com/wackou/bts_tools/

[10] xeroc. Xeroc’s bitshares-pricefeed repo, 2018. URL https://github.com/xeroc/bitshares-pricefeed/.
