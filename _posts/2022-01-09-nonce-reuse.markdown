---
layout: post
title:  "惊鸿一瞥：捕猎以太坊黑暗森林中的'那只怪兽'"
date:   2022-01-09 11:15:00
categories: Learning
usemathjax: true
tags:
    - blockchain
---

作为区块链安全爱好者，我对密码学和安全一直都有这强烈的兴趣，上周看了一篇非常不错的[文章](https://www.bertcmiller.com/2021/12/28/glimpse_nonce_reuse.html)，在和作者联系取得同意后，翻译成了中文，与感兴趣的朋友一起分享一下。

上周，以太坊黑暗森林里的一只怪兽终于向我漏出了他的獠牙。在与[tux](https://twitter.com/__tux)交流后，我得知了这只怪兽的存在，于是我部了下“我的诱饵”，16个小时之后它如期上钩。在Etherscan上看到我消失的ETH，仍旧心有余悸。

这个怪兽一直在注视着以太坊网络，试图寻找以太坊交易中那个不为人注意的错误：签名中的K值复用。为了找到这只怪兽，我投下饵料引诱它出现。为了理解我是怎么做的，首先有些关于ECDSA和数字签名的知识需要先介绍一下。

## ECDSA
椭圆曲线的数字签名算法（ECDSA）是支撑当今最大的两个数字货币，Bitcoin 和 Ethereum的密码学基础。就如何在现实中，人们用自己的签名来证明自己，基于ECDSA的数字签名是可以证明这些区块链上的数字资产所有权。每一个数字签名可以提供下边两个方面的证明：
1. 对于私钥的所有权。每把私钥都有与之绑定的一把公钥，这把公钥同时也用来生成在区块链上的一个“地址”。
2. 使用私钥对于一个消息的签名。在区块链的上下文下，这个消息就是交易。

ECDSA之所以有效是因为，我们可以使用私钥非常容易的得到公钥，但是无法（或者说很难）从公钥去推到出私钥。但是如果你得到数字签名，在一些条件下是可以推导出私钥的。下边让我们聊一点技术细节。

如果要完成对某个消息的签名，我们需要使用私钥d，一个随机值K，以及这个消息的哈希h。同时加上椭圆曲线中的G和n, 经过下边的公式计算，我们可以得到这个消息的数字签名r和s。

$$
r = k * G\ mod\ n \\
s = \frac{h+d*r}{k}\ mod\ n\\
$$

r和s一起就是这消息数字签名。

## nonce是什么？
在椭圆加密算法中，计算数字签名的随机K值是非常关键的。k值永远不能暴露，并且一个k值永远只能使用一次。这就是随机k值被称为nonce的原因。下文中也会用nonce在指代k值。如果黑客知道了某个签名的k值，那么它就可以通过以下的公式推算出这个签名的私钥。

$$
d = (s*k - h) * r^{-1}\ mod\ n \\
$$


同样，如果nonce被在两个不同的签名中重复使用，这个签名私钥同样也可以通过以下公式被推算出来。

$$
k = (h_1 - h_2) * (s_1 - s_2)^{-1}\ mod\ n \\
$$

有了nonce就可以通过上边的公式来推算出私钥。

那在实际中我们如何判断有没有nonce重复使用呢？

我们回忆一下ECDSA 数字签名中r的计算过程 $\ r = k * G\ mod\ n$. 这个公式中G和n都是确定的，唯一变量就是k，所以如果用同一个私钥签两个不同消息，在得到的签名中r是一样的，那么就可以判断出有nonce重复使用的问题。

在了解了这些后，我们来看以太坊上的交易，如果一个地址下的两笔交易的签名有相同的r值但是s值不同，那么可以判断这个地址下有nonce重复使用的情况。这就是“那只怪兽”一直的密切的关注点。也是我下边所用的实验方法。

Note：对于普通的用户来说，只要选择靠谱的钱包，你不要关心nonce泄露和重复使用的问题，但是对于区块链开发者来说，这是需要了解和注意的。

## 下饵，引蛇出洞
为了引蛇出洞，找到这只怪兽，我构造两笔交易，这两笔交易的签名使用了相同的nonce，所以他们签名会有相同的r值。我在这个地址上留了点ETH，所以如果有任何人盯着这个地址，那么它就可以通过签名推算出私钥来转走这个地址上的ETH。

为了制作这些诱饵，我必须构造出两笔nonce重复使用的交易，当然你没发使用Metamask来达到这个目的。于是我使用ether.js来达到这个目的。来看下边的示例代码。

```js
var drbg = new HmacDRBG({
      hash: this.hash,
      entropy: bkey,
      nonce: 1, // changed from "nonce"
      pers: options.pers,
      persEnc: options.persEnc || 'utf8',
    });
```
(代码仅供参考示例，别在自己的钱包上试)

在这里我把nonce设为了1！然后我重新生成了一把私钥，并将地址上充了0.04ETH，然后我使用下边的脚本，生成了两笔交易。

```js
const ethers = require("ethers")
  
    async function main(){
      const privateKey = "not-leaking-it-this-way"
      
      let wallet = new ethers.Wallet(privateKey)
      console.log("Using wallet:", wallet.address)
      
      const provider = new ethers.providers.JsonRpcProvider("rpc-endpoint")
      let signer = wallet.connect(provider)
      
      const tx = await signer.sendTransaction({
          to: wallet.address,
          value: ethers.utils.parseEther("0").toString(),
          gasLimit: 45000,
      })
  
      console.log(tx)
  }
  
  main()
```

由于我把nonce设为了1, 所以这两个交易的签名是有相同的r，和不同的s值。写了一个简单的脚本验证了一下：

```s
robert@mbp nonce-reuse-bait % node get_r_s.js
  Transaction 1
  r: 0x340709f674d030dda4aa8794bffb578030870bdfe583e7f41aea136a7ca1ed94
  s: 0x3e5b92d8c5a2a033c9e5eb53ff2946681b28ab82fa5b17d0a9c48d139e78fe2c
  
  Transaction 2
  r: 0x340709f674d030dda4aa8794bffb578030870bdfe583e7f41aea136a7ca1ed94
  s: 0x2c17cf960d1af7602f875afc56d01eddcc67bb03e962877444ed8d4c1287ab7a
```

随着这两笔交易的上链，其签名私钥其实已经暴露了。我已经把饵下好，现在就静静地等待那只怪兽的上钩。


## 惊鸿一瞥，怪兽出没
当发送完这两笔交易以后，我就不断的刷新区块浏览器来观察地址上的ETH是否还依旧存在。但是奇怪的事，一切如常。又过了几个小时，依旧一切如常。我都开始怀疑是不是自己搞错了什么，但是天色已晚，我就先去休息了。

第二天早上一早，我就查看我的账户，它出现了！我地址上的ETH被取的干干净净，我故意留在地址上的0.04个ETH被取走了！
![](https://bertcmiller.com/2021/12/Picture1.png)

那只怪兽终于漏出了獠牙，他注视着以太坊上的交易, 看是不是有交易的签名有相同的r值。当他发现以后就理解推算出他们的私钥，取空他们账户上的所有资产。我有点疑惑的是，为什么过了这么久他才出动？有一种可能性是它一直在默默等待，看这个账户上会不会有更多的资产转进来，他好一次性的都转走。我暴露的一把私钥引出了这只怪兽。

让我们近距离的看看这只怪兽，我不仅仅是唯一的受害者，它同样也取干了别人的资产！
![](https://bertcmiller.com/2021/12/Picture2.png)

这个地址一直有从不同地址下转入的ETH，截止目前这个账户上有价值$3,700的资产。为了验证这些被盗地址是否也是因为nonce重复使用的问题，我写了一个脚本来检查他们历史上交易是否也有相同的r值。我检查这只怪兽（黑客）盗取的第二地址，发现它之前发送的交易也存在r值重复使用的问题。

```s
robert@mbp nonce-reuse-bait % node get-tx-history.js
  
  Same r found!
  r: 0xf0d7b10f398357f7d140ff2be1bea9165d32238360ad0f82911235868be7c6e1
  hash: 0xb5d2454d7380bfa7ac75ec76f15eecb56e60941429153081fe799fb53a7ff901
  r: 0xf0d7b10f398357f7d140ff2be1bea9165d32238360ad0f82911235868be7c6e1
  hash: 0x9e459be7fa9950835a3c2594d3440c684fed05fa8e12e8088cc7776c4afb364c
  
  
  Same r found!
  r: 0x41d43fd626c24e449ac54257eeff271edb438bbabbc9bee3d60a5bd78dc39d6d
  hash: 0x670f66ff71882ae35436cd399adf57805745177b465fdb44a60b31b7c32e4d16
  r: 0x41d43fd626c24e449ac54257eeff271edb438bbabbc9bee3d60a5bd78dc39d6d
  hash: 0x374180005946ef3b1906ee1677f85fa62eb5a834aa0241b4c9c74174bca26a07
```

这进一步证明了我的猜测，这只怪兽紧紧盯着以太坊上的交易，时刻关注着是否有nonce复用的情况。一旦发现了nonce复用，他会慢慢的，悄悄地把这些地址上的资产都取光。

在我分析了这只怪兽攻击的其他地址后，我惊奇的发现有些地址并没有nonce重复使用的问题，事实上在它攻击的20个地址里，有9个存在nonce重复使用的问题，而其他并没有。

那么其他11个地址是什么愿意遭到了攻击呢？老实来说我并不清楚，一种可能性是这只怪兽同时还有其他策略去尝试推算私钥，例如用常见的一些单词，数字来推算私钥。同时还有一些其他的方法来利用nonce生成中的漏洞。但是我并没有从这只怪兽的行为中找到证明。

以太坊黑暗森林里的这只怪兽终于被我找到，但是会不会有或者谁是它的下一个受害者？这依旧是个谜题。

**Aaron简评：私钥生成不随机，nonce生成不随机，nonce重复使用这些问题都会有泄漏的可能性。对于用户来说，选择靠谱的钱包是避免这个问题的最好的方法，尤其推荐使用有真随机数生成器的硬件钱包。对于开发者来说，重视随机数的产生，选择靠谱的cryptographic library。同时使用确定性ECDSA签名算法也是避免这个问题的另一种方式。**