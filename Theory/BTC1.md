9/3

# 1，比特币的密码学原理

Crypto-Current

哈希（CryptoGraphic Hash Function）

哈希碰撞: x!=y f(x)=f(y)

brute-force蛮力求解

Collision Resistance（碰撞抗性）-massive digest（防篡改校验）

MD5可被人为制造哈希碰撞

Hiding哈希值不会泄露输入信息

x hashing obtain h(x), h(x) can not access x. 

Collision Resistance&Hiding

Digital Commitment(Digtal Equivalent of a Sealed Envelop)数字承诺

Puzzle Friendly（没有捷径，只能靠大量测试）Proof of Work

挖矿 Nonce

Difficult to Solve, But easy to verify.

BitCoin-SHA256(Secure Hash Algorithm)

签名

Bitcoin Account Manage:

Public Key & Private Key

对称加密Symmetric Encryption缺点：密钥分发

Asymmetric Encryption Algorithm非对称加密

**加密信息用接受者的公钥加密**

明文公钥加密，本地私钥解密。

公钥账号，私钥密码。

用私钥对交易进行**签名**，别人可以用公钥验证这是你进行的操作。

具体而言，就是先将转账消息用哈希函数压缩成定长摘要（Digest）。再用私钥对这个哈希值进行一系列数学运算（椭圆曲线点乘+模运算），得到一个签名，通常是一对数（r,s）。再把消息和签名一起广播出去。

验证时，别人收到你的**消息+签名+公钥**他们以同样的方法对消息做哈希，再把digest，签名（r,s），你的公钥放进ECDSA验证公式。验证公式能算出一个值，如果和签名中的一部分（比如r）匹配，就说明签名确实是由你的私钥生成的，如果消息被篡改，哈希就不一样，验证也会失效。

钥的产生 A Good Source of Randomness

Signatrue Confirm Hashing Massiveness

# 2，BitCoin DataStructure

BlockChain is a Linked-List using HashPointers and Block.

Genesis Block--------------Most Recently Block

![image-20250903223321458](D:\Backend\blockchain\DemoImage\BlockChain01.png)

hash指针的计算方法：前面的区块及其hash指针整体取哈希(Tample-evident log)。防篡改，检测出对区块链任意部分的修改。

牵一发而动全身，只需保持最后的HashPointer或近期/靠后的链。

Merkle Tree

Binary Tree

![image-20250904121951482](D:\Backend\blockchain\DemoImage\MerkleTree01.png)

Root Hash

HashPointer

Data Blocks Record Bitcoin Transaction Infomation.

Block Header Block Body

Merkle Proof

![image-20250904123213469](D:\Backend\blockchain\DemoImage\MerkleTree02.png)

交易验证：为了验证（部分交易），向全节点请求未改变的节点（Merkle Tree）的哈希值，与已知的tx交易一起从下往上逐层计算哈希值，最终和轻节点带有的Merkle Root（Block Header）值进行比对。

全节点，轻节点

Proof of MemberShip(Proof of Inclusion)

Proof of Non-Membership

Sorted Merkle Tree不存在证明

Sorted Hashing Transaction一定能得出待证明不存在的区块在哪两个区块之间， 这两个区块逐层进行Merkle Proof，最终结果与Block Header相同，证明该2区块相邻，证明待证明区块不存在。

在无环数据结构中，都能用hash指针代替普通指针。

# 3，BitCoin Consensus Protocol

花两次攻击Double Spending Attack

BitCoins Scripts完成对交易的检验

通过BlockChain防范花两次攻击，哈希指针

铸币交易CoinBase Tx

交易信息：输入（币来源，转账人公钥）+输出（收款人公钥）

转账人公钥需和币来源输出指定的公钥相同。

![image-20250904223946686](D:\Backend\blockchain\DemoImage\Transaction01.png)

这样的区块构成Merkle Tree，每个区块分为Block Header和Block Body两部分。

Block Header：Version，Protocol，Hashpointer of Previous Block Header，Merkle Tree Root Hash（保证Block Body无法被篡改），Target（挖矿难度目标阈值），Nonce（随机数）。

Block Body：交易信息

全节点（Full Vaildating Node）和轻节点（Light Weight Node）。轻节点只保留Block Header信息，无法完成独立验证。

哪些交易该被写入下一个区块。账本内容应当取得分布式的共识（Distributed Consensus）。比如Distributed Hash Table。

Impossiblity Result：FLP

在一个异步系统中，即使只有一个错误成员，也无法达成共识。

Asynchronous System（网络传输时延没有上限）

CAP Theorem（CAP 定理）

Consistency（一致） Availability（可用） Partition（分治） 

任何一个分布式系统中，以上三个性质最多满足两个。

Paxos协议保证Consistency。

## Consensus in BitCoin

Poll Basing基于投票的协议

Membership Hyperledger联盟链协议

Fabric

Sybil Attack（女巫攻击）：不断生成有投票权的账户，超过总账户数的一半，就能操纵投票。

计算力投票：矿工自行组装区块计算Nouce值，成功解出则拥有组装新区块的权利。每成功组装一个新区块，会从铸币交易中获得一定的比特币奖励，从一开始的每新组装一个新区块奖励50BTC，此后每新组装21万个区块，后组装一个区块的奖励减半，截至2024年，每区块奖励已降为3.125BTC。

新区块必须添加在Longest Vaild Chain，避免分支攻击（Forking Attack）。

如果恰好同时发布了两个新区块，产生等长的分叉链，之后根据算力与运气的差异，率先在某分支完成新区块组装的被认可为最长合法链，同时放弃不继续延展的区块，称为（Orphan Block）。在被放弃的区块/链上进行的交易不会被认可。

BTC的协议本质上就是认可用算力争夺铸币权，算力就是投票权。这样也能避免通过创建大量账号获得投票多数的“女巫攻击”。

9/5
