# BTC实现

Nakamoto Satoshi

BitCoin采用Transaction-Based Ledger（基于交易总账）

UTXO: Unspent Transaction Output

Total Inputs = Total Outputs

Transaction Fee，拥有记账权者对发布的区块上的记录交易“收税”。

每隔10min，产生一个新区快。每4年出块奖励减半。每2016个区块调整一次难度，保证出块时间在10min左右。 

Account-Based Ledger（基于账户总账）

关于难度升级：Bitcoin的C实现中这样记载。

```c
class CBlockHeader
{
public:
    // header
    int32_t nVersion;
    uint256 hashPrevBlock;
    uint256 hashMerkleRoot;
    uint32_t nTime;
    uint32_t nBits;
    uint32_t nNonce;

    CBlockHeader()
    {
        SetNull();
    }

    SERIALIZE_METHODS(CBlockHeader, obj) { READWRITE(obj.nVersion, obj.hashPrevBlock, obj.hashMerkleRoot, obj.nTime, obj.nBits, obj.nNonce); }

    void SetNull()
    {
        nVersion = 0;
        hashPrevBlock.SetNull();
        hashMerkleRoot.SetNull();
        nTime = 0;
        nBits = 0;
        nNonce = 0;
    }

    bool IsNull() const
    {
        return (nBits == 0);
    }

    uint256 GetHash() const;

    NodeSeconds Time() const
    {
        return NodeSeconds{std::chrono::seconds{nTime}};
    }

    int64_t GetBlockTime() const
    {
        return (int64_t)nTime;
    }
};
```

其中nNonce代表要计算的某个数，但是只有32位，这时往往通过铸币交易coinBase的来源哈希值一定的位数，从而改变MerkleTreeHashingRoot来增加求解难度。挖矿时，先解出（控制变量法？）

交易信息中的输入和输出。输入其实是上一次交易的输出，揭示币的来源。

## 挖矿的概率分析：

Bernoulli Trial:A Random Experiment With Binary Outcome.

Bernoulli Process:A Sequence of Independent Bernoulli Trials.

Memoryless无记忆，前面的实验结果对后面无影响。

Poisson Process：Exponential Distribution出块时间服从指数分布。

Probability Density & Time to Next Block成反比例

挖矿公平性的保证

![image-20250909160406825](https://github.com/kair998/BlockChain/blob/main/DemoImage/Fairness.png)

常见攻击方式；

1，伪造转账攻击：不被诚实节点承认，继续在正常区块上拓展链。

2，分叉攻击：在最长链上分叉，在延伸出的两个节点上进行“花两次”操作，使某条交易作废。对于交易的进行可以根据之后的一些区块综合验证，默认Six Confrimation。对于已有区块进行回滚篡改的话，恶意节点的算力竞争很难超过诚实节点。

3，Selfish Mining Attack：挖到区块后，先不发布，依靠算力优势继续挖出下一个区块，在一定程度上减少竞争，但是有风险。

注意：不能跳跃挖矿，在不确定的前链的情况下，不能直接挖。换言之，区块的组装必须要知道前一区块的一些信息。否则无法组装出合法区块。

# BTC网络

Application Layer:BitCoin BlockChain

Network Layer:P2P Overlay NetWork

不同于一般的P2P体系，BitCoinP2P没有超级节点，只需要知道一个种子节点就能平等地连接整个BitCoin网络。

Simple，Robust，But not efficient

Flooding，一个节点第一次收到某些消息时，把他传播给所有邻居节点。同时记录已经收到过该消息，下次再接收到相同消息时就不接收。

BitCoin中邻居节点的选择是随机的。

Simple，Robust，But not efficient

每一笔交易信息都被广播到比特币网络中。

比特币交易通过“输入”（Input）引用先前的未花费交易输出（Unspent Transaction Output, UTXO）。

**每一笔交易**都包含对之前某笔交易输出的引用（即输入），每个交易的输入都指向一个具体的UTXO。

一个UTXO只能被花费一次。如果两笔交易尝试花费同一个UTXO，那么第二笔交易会被认为是双重支付。

# 挖矿难度

H（Block Header）<= target

SHA-256

为什么需要保持出块时间的稳定？

BTC中出块时间是10min，如果出块时间无限减少，导致同时间分叉过多，有利于恶意节点以较低的成本发动分叉攻击（51%，回滚）。

ETH中出块时间稳定在15s，其有独特的共识协议GHOST，对于orphan block也会给予一定的出块奖励（uncle reward），但它也会进行挖矿难度调整。

BTC每新产生2016个区块（大约2周）调整一次Target（出块难度）。

![](https://github.com/kair998/BlockChain/blob/main/DemoImage/MiningTarget.png)

BTC区块BlockHeader中的nBits就是Target的编码形式。

能否通过缩减占位而表义相同？

![image-20250913115502966](https://github.com/kair998/BlockChain/blob/main/DemoImage/MineDifficulty.png)

挖矿难度与目标阈值成反比

