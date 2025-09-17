# 挖矿

全节点：

轻节点：

不必一直在线

只保存区块块头

不保存全部交易，只保存与自己相关的交易

无法检测网上发布的区块的正确性

可以验证挖矿难度

只能检测哪个是最长链，不知道那个是最长合法链

Memoryless Process Free

在有新区块发布时，停止当前挖矿。

挖矿设备

ASIC（Application Specific Integrated Chip）芯片

挖矿专用芯片，甚至只针对某一种Mining Puzzle。

Merge Mining

Alternative Mining Puzzle(Resistance  ASIC)

矿池：

一个全节点，驱动很多矿机

miner只负责挖矿，矿池负责其他的全节点工作。

解决收入不稳定的问题，挖矿收益分配。

平均分配？大锅饭

工作量证明

Share

Almost Valid Block

收款地址为矿主地址，矿工不能“偷矿”

# 矿池算力过强的风险

51%分叉攻击

Boycott攻击，封锁境遇：

只要发现某个区块被打包，就立即分叉

# 比特币脚本

这一节不好理解，应该再看一遍

再看：

input 和 output很反直觉的地方

![TX](C:\Users\jingl\AppData\Roaming\Typora\typora-user-images\TX.png)

vout中的output script规定“下一个花这笔钱的人需要满足什么条件”。以P2PK（Pay to Public Key）为例：

output script提供收款人公钥和CHECKSIG验证方法。这时交易已然完成，只是收到币的一方在想要花出BTC时，需要在input script中提供自己的签名。input script和output script拼在一起运行，验证通过，则证明你有权花这笔来源UTXO。

vin中记录币来源txid及属于那个tx的第几个vout便于定位查找。

无论何种交易脚本，验证环节发生在收款人想要使用这笔BTC时。

RedeemScript（赎回脚本）支持多重签名验证

在进行交易验证时，先验证赎回脚本的正确性，再反序列化赎回脚本，验证公钥和签名是否对应。

![multiSig](C:\Users\jingl\AppData\Roaming\Typora\typora-user-images\multiSig.png)

以购物场景为例，用户想要购买，必须知道收款方的多重签名参数和公钥信息，用户承担了主要的复杂度。

而P2SH（Pay to Script ）复杂度由收款方承担。

![P2SH](C:\Users\jingl\AppData\Roaming\Typora\typora-user-images\P2SH.png)

# 分叉

硬分叉：

在原有功能上进行扩展，造成“新区块承认老区块，而老区块不承认新区块”比如ETH和ETC。

软分叉：

利用原有功能可以自定义的部分，使得“老区块承认新区块，而新区块不承认老区块”，比如P2SH，老区块只能进行第一步的赎回脚本正确性验证，而新区块可以进行第二步反序列化赎回脚本，进行公钥验证。最终倒逼所有区块承认该更新。

# 问答

OP_RETURN

写在output script中，写入不需要进行验证，同时也没有人能够花这笔钱。

# BitCoin的匿名性

Pseudo Nymity

Privacy Anonymity

137 5995 592

未名湖（Unnamed Lake）

比特币公私钥不可变，容易被追踪分析出交易情况。（关联输入，输出地址）尤其在比特币兑换现实货币时，难免泄露匿名性。

关于老师在论述“去标识化并不等于匿名”时引用的例子，实际上是这篇论文《Unique in the shopping mall: On the reidentifiability of credit card metadata》。

Silk Road覆灭

TOR洋葱网络

提升BitCoin安全性的措施

应用层面：coin mixing

网络层面：多路径转发网络

零知识证明：

证明者向验证者证明一个陈述是正确的，而不需要透露除该“陈述是正确的”之外的任何信息。

同态隐藏：

![homomorphicHiding](C:\Users\jingl\AppData\Roaming\Typora\typora-user-images\homomorphicHiding.png)

![blindSignature](C:\Users\jingl\AppData\Roaming\Typora\typora-user-images\blindSignature.png)

![zeroCoinAndZeroCash](C:\Users\jingl\AppData\Roaming\Typora\typora-user-images\zeroCoinAndZeroCash.png)

# BTC的思考

1，哈希指针

哈希指针，本质上是哈希，存储在键值对里，常见数据库为LevelDB。

2，区块恋

拆分私钥保存的弊端，使得对加密的破解变得容易。

推荐使用多重签名

3，比特币为什么能够绕过分布式系统中的不可能结论

实现与理论有差距

“不要被学术界的思维，限制了头脑，不要被程序员的思维，限制了想象力”

4，BTC的稀缺性

一个有限总量的物品，不适合作为货币

5，量子计算冲击
