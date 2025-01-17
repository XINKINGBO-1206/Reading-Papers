# Dynamic Credentials and Ciphertext Delegation for Attribute-Based Encryption

用于属性加密的动态凭证和密文委托

## 0. TODOs

1. 关于身份基加密，我们需要深入了解。

## 1. 这篇论文解决了什么问题，使用了什么方法？

在云存储场合，用户的权限和被加密的数据都可能改变，这要求我们设计的基于属性密码学的云存储方案能动态地适应这样的变化。

**问题描述**：怎样动态地撤销用户私钥，更新被加密的数据？

**目标分解**：为了解决问题，作者做了二方面工作，分别是可撤销存储（Revocable Storage）和保护新加密的数据（Protecting Newly Encrypted Data）。针对可撤销存储，我们需要借助第三方，即存储服务提供方做密文处理，我们的目标是在不访问敏感数据的前提下撤销原有加密并重新加密以使指定的用户不能再访问数据。针对保护新加密的数据，如果我们撤销了用户访问数据的权限，那么用户将无法访问新加密的数据，这实际上要求我们重新设计ABE方案，使得存储服务的提供方可以通过更新密文来禁止指定用户解密他曾经能解密的数据，同时，新产生的数据不受影响，这跟传统ABE中，新用户可以解密全体能解密的历史数据，新数据可以被全体能解密的用户解密存在不同。

**解决方案**：作者为了解决上述问题，分别设计了密文委托程序和分段密钥生成的方案。前者允许存储服务提供方通过重新加密即可获得更具限制性的策略；后者通过修改现有ABE方案，针对密钥策略和密文策略构建了新方案。

## 2. 可撤销ABE框架

论文实现了一个可撤销ABE框架，它的核心技术包括可撤销存储和保护新加密数据。

### 2.1. 可撤销存储

作者在这部分描述了加密存储的常见应用情形，企业数据库存储着敏感信息，员工是流动的。明文数据库被攻破的代价高昂，企业数据库必然存储密文。企业数据库需要更新密文来应对员工离职，直接思路是通过员工跟企业数据库交互完成密文更新，使离职员工的密钥失效，这样的交互是不安全的。

为了实现可撤销存储，作者提出了概念——密文委托（ciphertext delegation）。**密文委托仅使用公开信息，把原始密文访问策略*P*变成更严格的*P'*，这个变更不会导致密文长度发生变化。**

### 2.2. 保护新加密的数据

为了防止已经离职的员工使用原密钥解密离职后新增的密文，Boldyreva等人已经提出了身份基加密的概念，事实上，身份基加密不能用于可撤销密文存储。

论文从头开始构造了用在企业数据库的ABE方案，提出了新概念——分段密钥生成。**分段密钥生成是指用户会通过调用多个密钥生成函数分别生成密钥的各部分，组合生成密钥。在生成密钥的过程中，用户可以看到已经生成的密钥片段。**在密文解密过程中，当且仅当用户的密钥全部被允许解密时，解密成功。

把用户身份和可撤销密文存储结合起来，这是论文的首创工作。