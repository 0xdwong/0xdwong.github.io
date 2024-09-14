---
layout: post
title: '使用 Safe SDK 创建多签钱包'
date: '2024-09-14'
category: wallet
permalink: /wallet/safe-wallet-sdk
tags: wallet safe gnosis-safe
---

## Safe 简介

[Safe](https://app.safe.global/)（前身为 Gnosis Safe）是一个建立在以太坊网络上的智能合约钱包平台，专注于提供安全、灵活的数字资产管理解决方案。
Safe 广泛应用于个人资产管理、DAO 治理、企业财务等领域，是目前以太坊生态中最受信赖的多签钱包解决方案之一。

更多关于 Safe 多签钱包的介绍见[这里](./2024-09-13-intro-to-safe-wallet.md)。

现在，让我们深入了解如何使用 Safe SDK 来创建和使用多签钱包。

## 使用教程

Safe 提供了功能强大的 JavaScript SDK，让开发者能够轻松集成和使用 Safe 的功能。

以下是使用 JavaScript SDK 与钱包交互的详细示例代码:

### 准备工作

首先，确保你的开发环境中已安装 Node.js 和 npm/yarn。然后，安装必要的依赖:

```bash
npm install ethers @safe-global/protocol-kit @safe-global/api-kit
# 或者
yarn add ethers @safe-global/protocol-kit @safe-global/api-kit
```

### 部署一个多签钱包
```javascript
const { SafeFactory } = require('@safe-global/protocol-kit');

const safeFactory = await SafeFactory.init({
  provider: '<provider url>', // 替换为实际的 RPC url
  signer: '<sender private key>', // 私钥，用于部署多签钱包合约，该私钥对应的账户不一定是多签的签名者
})

// 创建一个 2-3 多签钱包
const safeAccountConfig = {
  owners: [
    '<signer address 1>', // 替换签名者地址
    '<signer address 2>', // 替换签名者地址
    '<signer address 3>', // 替换签名者地址
  ],
  threshold: 2, 
}

// 部署多签钱包合约
const safeWallet = await safeFactory.deploySafe({ safeAccountConfig });

const safeAddress = await safeWallet.getAddress();

console.log('Safe 钱包已部署');
console.log(`https://app.safe.global/sep:${safeAddress}`); // 以 sepolia 为例
```

### 读取多签钱包相关信息
```javascript
  const Safe = require('@safe-global/protocol-kit').default;

  const safeWallet = await Safe.init({
      provider: '<provider url>', // 替换为实际的 RPC url
      safeAddress: '<safeAddress>', //替换为实际的多签钱包地址
  })

  const threshold = await safeWallet.getThreshold();
  const owners = await safeWallet.getOwners();

  console.log(`Threshold: ${threshold}`); // 阈值（最小签名数量）
  console.log(`Owners: ${owners.join(', ')}`); // 签名者
```

### 发起一笔 ETH 转账多签交易

（记得先给该多签钱包发送足够的 ETH）
```javascript
const signerPrivateKey = "0x..."; // 替换为多签钱包签名者之一的私钥

// 转账 0.01 ether
const safeTransactionData = {
  to: '<receiver>', // 替换为收款人地址
  data: '0x',
  value: ethers.parseUnits('0.01', 'ether').toString()
}

// 创建多签交易
const safeTransaction = await safeWallet.createTransaction({ 'transactions': [safeTransactionData] });

// 获取这笔交易的哈希值
const safeTxHash = await safeWallet.getTransactionHash(safeTransaction);

// 对交易哈希值进行签名
const senderSignature = await safeWallet.signHash(safeTxHash);

const apiKit = new SafeApiKit({
  chainId: await safeWallet.getChainId(),
})

// 提交交易到 Safe 服务（这样，其他签名者可以在 Safe 网站中看到待签名的交易）
await apiKit.proposeTransaction({
  'safeAddress': '<safe address>', // 替换为实际的多签钱包地址
  'safeTransactionData': safeTransaction.data,
  'safeTxHash': safeTxHash,
  'senderAddress': new Wallet(signerPrivateKey).address,
  'senderSignature': senderSignature.data,
})
```

### 确认交易

```javascript
// ... (省略前面的代码)
const transaction = (await apiKit.getPendingTransactions(safeAddress)).results[0];

if (!transaction) return;

const safeTxHash = transaction.safeTxHash;

const signature = await safeWallet.signHash(safeTxHash);
const response = await apiKit.confirmTransaction(safeTxHash, signature.data);
console.log('已确认交易:\n', response);
```

### 执行交易

```javascript
// ... (省略前面的代码)
if (transaction.confirmations.length !== transaction.confirmationsRequired) {
  console.log('未达到签名确认数');
  return;
}

const safeTransaction = await apiKit.getTransaction(transaction.safeTxHash);
const executeTxResponse = await safeWallet.executeTransaction(safeTransaction);
const receipt = await executeTxResponse.transactionResponse?.wait();

console.log('交易已执行，hash：', receipt.hash);
```

以上完整代码示例见：[demos](https://github.com/0xdwong/web3-practice/blob/main/src/gnosis_safe/README.md)


## 结语
本文详细介绍了使用 Safe JavaScript SDK 创建多签钱包和执行交易等基本步骤。随着对 Safe 的深入探索，其高级特性如模块化扩展、批量交易等将为你开启更广阔的应用空间，助你从容应对各种复杂的资产管理需求。

如果你想进一步学习和探索 Safe 的功能，可以参考以下资源：

- [Safe官方文档](https://docs.safe.global/)
- [Safe{Core} SDK](https://docs.safe.global/sdk/overview)