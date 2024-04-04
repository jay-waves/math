# A

AES: 目前广泛使用的对称加密标准, 见 [AES](密码学/分组密码/SP-结构/AES.md)

# B

# C

CBC: 一种对称密码的链接工作模式, 用于加密长明文, 有迭代反馈机制, 见[链接工作模式](密码学/分组密码/链接工作模式.md) .

CPA: Chosen Plaintext Attack, 选择明文攻击.

CCA: Chosen Ciphertext Attack, 选择密文攻击. 分为 CCA1 和 CCA2 两种.

CCA1: Lunchtime Attack,在解密查询开始前, 就要选择所有要测试的密文, 之后不能再根据反馈重新选择.

CCA2: Adaptive Chosen Ciphertext Attack, 攻击者基于先前解密查询的结果来选择后续的密文, 也就是根据已知信息调整策略. 比 CCA1 更强.

CA: Certificate Authority, 用于颁发和管理密钥/数字证书的第三方机构

Completeness: 完备性. 逻辑系统是完备的, 如果它能证明该逻辑下的**所有**真命题.

# D

DES: 早期对称加密标准, 现由于安全性已被 AES 取代. 见 [DES](密码学/分组密码/Feistel-结构/DES.md)

Digital Certificate: 数字证书, 用于验证公钥拥有者身份, 见 [密钥分发与管理](密码学/安全协议/密钥分发与管理.md)

DLP: Dsicreate Logarithm Problem, 离散对数问题, 见 [欧拉定理](代数/数论/欧拉定理.md)

DH: Diffie-Hellman 密钥交换, 两实体在非安全信道交换秘密的算法, 见 [Diffie-Hellman协议](密码学/公钥密码/DiffieHellman-密钥交换.md)

# E

ElGamal: 一种基于 DLP 困难性的公钥密码算法, 包括加密和签名. 见 [ElGamal-协议](密码学/公钥密码/ElGamal-协议.md)

# F

FAC: Factorization, 因数分解. 通常指 RSA 基于的大整数分解困难问题.

# G


# H

HMAC: 基于密码学杂凑函数的消息认证码 (MAC), 见 [HMAC](密码学/消息摘要/消息认证码/HMAC.md)

# I

IV: Initializtion vector, 初始化向量, 用于在迭代型密码算法中提供初始随机性.

# J

# K

# L

# M

MIPS: Million Instructions Per Second, 用于衡量 CPU 每秒执行指令的速度.

MAC: Message Authentication Code, 消息认证码, 用于验证消息的完整性和来源真实性, 见 [MAC](密码学/消息摘要/消息认证码/MAC.md)

MD5: Message Digest Algorithm 5, 广泛使用的散列函数, 由于安全问题已被淘汰, 见 [MD5](密码学/消息摘要/MD5.md).

# N

Nonce: Number Once, 用于密码算法和安全协议的一次性数字, 用于防范重放攻击.

# O

# P

PRNG: Pseudo-Random Number Generator, 伪随机数生成器, 见 [流密码](密码学/流密码与伪随机数/流密码.md)

PKI: Public Key Infrastructure, 公钥基础设置, 见 [密钥分发与管理](密码学/安全协议/密钥分发与管理.md)

# Q

OSI: 一种安全框架, 定义了安全攻击/安全机制/安全服务, 见 [安全模型](obsidian://open?vault=Code&file=Security%2F%E5%AE%89%E5%85%A8%E6%A8%A1%E5%9E%8B)

# R

RSA: 目前广泛使用的公钥加密算法, 见 [RSA](密码学/公钥密码/RSA/RSA.md)

# S

SHA: Secure Hash Algorithm, 密码学哈希函数 (消息摘要函数, 杂凑函数, 散列函数)

SM4: 中国标准对称密码算法, 见 [SM4](密码学/分组密码/Feistel-结构/SM4.md)

SM2: 中国标准公钥密码算法, 基于 ECC, 包括: 加密算法, 数字签名, 密钥协商算法. 见 [SM2](密码学/公钥密码/ECC/SM2.md) 

Soundness: 健全性, 一个逻辑系统是健全的, 当它所有推导都仅产生真实结论; 即不会错误地证明假命题为真.

# T

# U

# V

# W

# X

# Y

# Z