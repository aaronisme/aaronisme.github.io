---
layout: post
title:  "如何妥善备份你的以太坊钱包?"
date:   2022-02-08 02:15:00
categories: Thinking
tags:
    - blockchain
---

## 新世界大门

当你打开数字货币新世界大门时，你需要学会一项在这世界生存的技能， **如何妥善的备份你的钱包** 。

在过去的世界中，当你把密码弄丢时，你仅需要向服务商提交忘记密码的申请，稍过一会，你就会收到一封邮件，拿起键盘，输入你的新密码。这过程就像魔法一样，你重新获得账户的支配权。

这么理所当然的功能，在新世界中，你再也见不着踪影。

这是你看到数字货币诸多不方便的一面，也是它令人着迷的另一面。因为这是人类历史上，第一次通过技术彻底、纯粹地保障「私有财产神圣不可侵犯」。而这一切，都建立在你如何妥善地保管你的私钥的基础上。

私钥，即财富。

## 钱包生成机制

在数字货币世界中，你的钱包由私钥，公钥构成。在学会保管钱包前，你需要明白私钥与公钥的生成机制:  非对称加密算法。

在 1976 年以前，所有的加密方式都是同一种模式：
1. 甲方选择一种加密规则，对信息进行加密；
2. 乙方使用同一种规则，对信息进行解谜；

由于加密与解密皆为同一种规则，被称为「对称加密算法」。此加密算法的最大弱点就是甲乙双方都需要了解解密规则，而保存和传递解密规则的过程存在极高的安全风险。

直到 1977 年，Ron Rivest、Adi Shamir 和 Leonard Adleman 设计了一种非对称加密算法，此算法以他们三人名字命名，被称为「RSA 算法」。

![]({{url}}/resources/img/32107/9a76c94c97b9456788fca0fe996182d9.png)

以上图为例，解释非对称加密模式的流程：
1. Bob 与 Alice 通过非对称算法生成各自的私钥和公钥（公钥可以通过私钥推导）；
2. Bob 想给 Alice 发送一份加密信息；
3. Bob 用 Alice 的公钥对信息进行加密；
4. 加密的信息仅能通过 Alice 的私钥解密；

当前数字货币（比特币、以太币等）采用的是「椭圆曲线算法」，椭圆曲线算法同样也是非对称算法，相比起 RSA 算法有更多的优势，比如安全性能高、计算量小、存储空间占用小、带宽要求低等。

每一个钱包账户包含一份密钥对，即私钥与公钥。私钥（k）是一个数字，通常是随机选出的。有了私钥，我们就可以使用椭圆曲线乘法这个单向加密函数生成一个公钥（K）。有了公钥（K），我们就可以使用一个单向加密哈希函数生成该账户地址（A）。

当你发生交易时，每笔交易都需要一个有效的签名才会被存储在区块链。只有有效的私钥才能产生有效的数字签名，因此拥有钱包账户的私钥就拥有了该账户的支配权。

## 钱包形态

在了解钱包的生成机制后，我们很快就明白一点，我们备份钱包，就是备份私钥，但因保管方式不同，所表现的形态也不一样。

目前常见的私钥形态：
1. Private Key
2. Keystore && Password
3. Mnemonic Seed

### Private Key

Private Key 就是一份随机生成的 256 位二进制数字，你甚至可以用硬币、铅笔和纸来随机生成你的私钥：掷硬币 256 次，用纸和笔记录正反面并转换为 0 和 1，随机得到的 256 位二进制数字可作为私钥。这 256 位二进制数字，就是私钥原始的状态。

### Keystore && Password

在以太坊官方钱包中，私钥与公钥将会以加密（创建钱包时设置的密码，请务必记住！）的方式保存为一份 JSON 文件，存储在 `/Users/yourname/Library/Ethereum/keystore` 中。 这份 JSON 文件就是 keystore，所以你需要同时备份 keystore 和对应的 password。

### Mnemonic code

Mnemonic code 由 [BIP 39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 提案提出，目的是通过随机生成 12 ~ 24 个容易记住的单词，单词序列通过 PBKDF2 与 HMAC-SHA512 函数创建出随机种子，该种子通过 BIP-0032 提案的方式生成确定性钱包。

BIP 39 定义助记码创建过程如下：
1. 创造一个 128 到 256 位的随机顺序（熵）。 
2. 提出 SHA256 哈希前几位，就可以创造一个随机序列的校验和。 
3. 把校验和加在随机顺序的后面。 
4. 把顺序分解成 11 位的不同集合，并用这些集合去和一个预先已经定义的 2048个单词字典做对应。 
5. 生成一个 12 至 24 个单词的助记码。

所以当你记住 12 ~ 24 个助记码后，就相当于记住私钥。助记码要比私钥更方便记忆和保管。目前支持助记码的钱包有 [imToken](https://token.im/)  和 jaxx 。

## 钱包备份方式

因为钱包的形态多样（本质一样），所以备份的方式也同样多点，但最终的目的： **防盗，防丢，分散风险** 。

- 防盗：分离备份，假如 keystore 或密码被盗，但对应的密码 和 keystore 依然安全；

- 防丢：多处备份，降低丢失所有对应的 keystore && password 、助记码、私钥等等风险；

- 分散风险：将资金适当分散，降低损失程度，同时采取多重签名方式，提取超过限制金额，需要多把私钥授权；

下面为大家介绍常见的备份方式：
1. 多处和分离备份 keystore && password
2. 纸钱包
3. 脑钱包
4. **多重签名**

### 多处和分离备份 keystore && password

1. 打开以太坊官方钱包，在菜单栏中选择 ACCOUNTS -> BACKUP -> ACCOUNTS，你会看到一个 keystore 文件夹，在里面保存你创建过的钱包账户，以 UTC--2016-08-16....... 格式命名的 JSON 文件，这就是你的 keystore 文件。
2. 将 keystore 文件放置多处安全的位置，如离线的 USB 以及你信任的云存储服务商。
3. keystone 对应的 password，你应该采用强密码，同样多处且与 keystore 分离备份。

### 纸钱包备份

纸钱包实质就是将 keystore 或 私钥以纸质化形式保存，一般为二维码形式。

你可以通过命令行的方式
```
cat /Users/yourname/Library/Ethereum/keystore/<key_file> | qrencode -o keystore.png
```

也可以到 [MyEtherWallet: Open Source JavaScript Client-Side Ether Wallet](https://www.myetherwallet.com/) 离线提交你的  keystore 或 私钥，就可以直接打印对应的二维码纸钱包。

### 脑钱包

我们所说的脑钱包并不是由用户自身输入自定义的词句生成私钥（因为这并不安全），而是通过 [BIP 39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 提案的方式生成足够随机的，可记忆的助记码。这是一个方案，但不是一个非常好的方案，因为人类的大脑并不总是靠谱。

### 多重签名

多重签名是一个不错的选择，它的优势是当你需要提取超过限制的金额时，需要多把私钥同时授权，同时提升防盗，防丢的安全性。

在以太坊官方钱包中，你可以在 Wallet Contracts 下方中选择 Add Wallet Contract，前提是你用来创建 Wallet Contract 的 account 有不少于 0.02 ETH，足以支付交易所需的费用。

![]({{url}}/resources/img/32107/a9e46934a9eb4fb9a6157ef5fc2589eb.jpg)


当你选择 MULTISIGNATURE WALLET CONTRACT ，将会看到如下提示：

> “This is a joint account controlled by X owners. You can send up to Y ether per day. Any transaction over that daily limit requires the confirmation of Z owners.”

X 代表此钱包合约由多少账户控制
Y 代表在单个账户授权情况下，每日可提款的上限
Z 代表突破提款上限，需要多少账户授权

默认我们采取 X = 3 ，Z =2 的方式，钱包合约由三个账户管理，需突破取款上限需要两个账户同时授权。

采取多重签名的机制后，你可以多处且分离的方式保管你的 keystore 和 password，提升防盗，防丢的安全性。

关于更多多重签名的详情可看官方文档： [Account Management — Ethereal Homestead 0.1 documentation](http://ethdocs.org/en/latest/account-management.html#creating-a-multi-signature-wallet-in-mist)

### 结语

不管你用任何方式备份钱包，达到 **防盗，防丢，分散风险** 的目的即可。

#### 参考：

- [Account Management — Ethereum Homestead 0.1 documentation](http://ethdocs.org/en/latest/account-management.html)
- [Technical background of version 1 Bitcoin addresses - Bitcoin Wiki](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses)
- [GitHub - bitcoinbook/bitcoinbook: Mastering Bitcoin - Unlocking digital currencies](https://github.com/bitcoinbook/bitcoinbook)
- [wallets - What is the recommended way to safely store Ether? - Ethereum Stack Exchange](http://ethereum.stackexchange.com/questions/1239/what-is-the-recommended-way-to-safely-store-ether)
- [accounts - How to backup mist wallets? - Ethereal Stack Exchange](http://ethereum.stackexchange.com/questions/946/how-to-backup-mist-wallets)
- [security - Is there a way to generate ethereum paper wallets? - Ethereum Stack Exchange](http://ethereum.stackexchange.com/questions/103/is-there-a-way-to-generate-ethereum-paper-wallets?rq=1)
- [contract design - How can I create a multi signature address on Ethereal? - Ethereal Stack Exchange](http://ethereum.stackexchange.com/questions/6/how-can-i-create-a-multisignature-address-on-ethereum)
- [Do you need to backup ETH Wallet Contracts? - Support - DAOhub.org](https://forum.daohub.org/t/do-you-need-to-backup-eth-wallet-contracts/887/3)

----

作者：阿树

本文于一年前首发于 [EthFans 论坛](https://ethfans.org/topics/595)，现重发于文章板块。