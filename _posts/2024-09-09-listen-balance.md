---
layout: post
title: '监听以太坊地址余额的常用的方法'
date: '2024-09-09'
category: ethereum
permalink: /ethereum/listen-balance
tags: ethereum evm balance
---

在区块链应用开发中，尤其是涉及以太坊平台的场景中，监听地址余额（ETH 或 token）的变化是一项常见且关键的需求。为了有效监控这些变化，开发者可以选择多种实现方式，每种方式都有其特定的实现机制和适用场景。

本文将详细介绍三种常用的监听以太坊地址余额方法：定期轮询、监听合约事件和扫块。每种方法都将包括实现步骤、优缺点分析以及示例代码。


## 三种常用的方法

1. 定期轮询方法

定期轮询方法是一种通过设置定时任务，按照固定的时间间隔查询以太坊地址上的余额变化的方法。

这种方法涉及重复发送请求到以太坊节点（如通过 JSON RPC 、区块链浏览器 API 或使用 ethers.js 库等），检查目标地址的当前余额，并与上一次记录的余额对比，以确定是否发生变化。

2. 监听合约事件方法

以太坊智能合约中的事件机制允许开发者定义特定的事件并在合约函数执行时触发它们。通过使用库如 ethers.js，外部应用能够订阅并实时监听这些事件。当相关事件被触发，监听器会接收到包含所有必要信息的事件数据。这种方法较之轮询更为高效，因其仅在实际发生变动时才激活监听器。

3. 扫块

扫块是一种更全面的监控方法，它通过检查每个新生成的区块中的所有交易，来识别与特定地址相关的交易和余额变化。


接下来，让我们具体深入每一种方法的实现方式。

## 定期轮询

### 实现步骤

1. 设定一个定时器，按设定时间间隔进行轮询
2. 使用 Web3 库（如 ethers.js）等获取特定地址的最新余额
3. 将获取的余额与之前的余额进行比较，若有变化，则进行相应处理

### 优缺点分析

优点：

- 实现简单，容易理解
- 不依赖智能合约的事件，通用性较好

缺点：

- 对区块链节点或服务有较多的访问请求，可能造成资源浪费
- 余额更新的反应时间依赖于轮询间隔，实时性较差

### 示例代码
使用 ethers.js (v6)获取以太坊余额示例：

```
const { ethers } = require('ethers');
const provider = new ethers.providers.JsonRpcProvider('your_rpc_url');

async function getBalance(address) {
    let balance;
    try {
        balance = await provider.getBalance(address);
    } catch (error) {
        console.error('Error fetching balance:'， error);
    }
    return balance;
}
```

另外，区块链浏览器如 Etherscan 或 BscScan 等，提供了丰富的 API 接口，可以查询交易、地址余额及其它相关数据。

使用 Etherscan API 接口获取以太坊余额示例：

```
const axios = require('axios');
const API_KEY = 'your_api_key_here';

async function getBalance(address) {
    let balance;
    try {
        const url = `https://api.etherscan.io/api?module=account&action=balance&address=${address}&tag=latest&apikey=${API_KEY}`;
        const response = await axios.get(url);
        balance = response.data.result;
        balance = BigInt(balance)
        console.log(`Balance: ${balance} WEI`);
    } catch (error) {
        console.error('Error fetching balance:'， error);
    }
    return balance;
}
```

定期轮询
```
async function checkBalance(address) {
    let lastBalance = BigInt(0);

    setInterval(async () => {
        const currentBalance = await getBalance(address);
        if (currentBalance != lastBalance) {
            console.log(`Balance updated`);
            lastBalance = currentBalance;
        }
    }， 60 * 1000); // 每分钟检查一次
}

checkBalance('your_ethereum_address_here');
```


## 监听合约事件

### 以太坊事件机制简介

在智能合约中，开发者可以定义事件并在合约的某些函数中触发这些事件。这些事件随着区块的挖出被记录在区块链上，外部应用可以监听这些事件。

### 实现步骤

1. 找到合约中定义的余额变化相关的事件
2. 使用 ethers.js 等库监听这些事件
3. 处理捕获到的事件数据

### 优缺点分析

优点：

- 实时性强，事件发生后立刻触发监听
- 网络负载小，不需要频繁查询

缺点：

- 不适用于监控 ETH 原生代币余额
- 只适用于有相应的事件输出的合约
- 需要理解合约内的事件逻辑
- 丢失事件风险: 如果监听服务在某段时间内离线或出现故障，可能会丢失在这段时间内产生的事件。这意味着应用可能会遗漏关键的状态更新

### 示例代码 (使用 ethers.js v6)

```
const { ethers } = require('ethers');
const provider = new ethers.JsonRpcProvider('your_rpc_url');
const contractAddress = 'your_contract_address';
const abi = [
    "event Transfer(address indexed from， address indexed to， uint256 value)"
]; // ERC20 合约部分 ABI


const contract = new ethers.Contract(contractAddress， abi， provider);

contract.on('Transfer'， (from， to， value， event) => {
    console.log("Transfer event detected!");
    console.log("From:"， from);
    console.log("To:"， to);
    console.log("Value:"， value); 
    console.log("Hash:"， event.log.transactionHash);
    console.log("\n");

    // 进一步处理
});
```

## 扫块

### 实现步骤

1. 连接到以太坊节点: 通过 JSON RPC 或 WebSocket 等接口连接到以太坊节点。
2. 获取最新区块号: 定期通过节点 API 获取当前的最高区块号。
3. 检索新区块: 当检测到新区块时，获取这些区块的详细信息，包括内部的所有交易。
4. 分析交易数据: 遍历所有交易，检查交易详情，识别涉及指定监控地址的交易。
5. 更新地址余额(如有必要): 如果交易影响了指定的监控地址，更新内部记录的余额数据。

### 优缺点分析

优点:

- 全面监控: 可以捕获所有相关的交易。
- 无遗漏: 比事件监听更可靠，即使在节点连接不稳定时，稍后也可以通过重新扫描错过的区块来更新数据。
- 灵活性高: 可以根据具体的需要分析任何类型的交易或者合约调用。

缺点:

- 资源消耗大: 需要处理每个区块中的所有数据，对计算和存储资源的要求较高。
- 实现复杂: 相较于其他方法，实现扫块功能需要更多的编码工作并且复杂度较高。
- 延迟性: 虽然可以通过减小扫描间隔来提高响应速度，但总体上仍然存在一定的延迟。


### 示例代码 (使用 ethers.js v6)
```
const { ethers } = require('ethers');

// 监控地址
const ADDRESS_TO_MONITOR = '';

// 初始化 provider
const provider = new ethers.JsonRpcProvider('your_rpc_url');

async function scanBlocks() {
    let lastCheckedBlock = await provider.getBlockNumber();

    setInterval(async () => {
        const currentBlockNumber = await provider.getBlockNumber();

        if (currentBlockNumber > lastCheckedBlock) {
            for (let i = lastCheckedBlock + 1; i <= currentBlockNumber; i++) {
                const block = await provider.getBlock(i， true);

                block.prefetchedTransactions.forEach(tx => {
                    // 检查交易中的发送或接收地址

                    if (tx.from === ADDRESS_TO_MONITOR || tx.to === ADDRESS_TO_MONITOR) {
                        console.log(`检测到相关交易: ${tx.hash}`);
                        // 进一步处理
                    }
                });
            }
            lastCheckedBlock = currentBlockNumber;
        }
    }， 12 * 1000); // 每 12 秒检查一次
}

scanBlocks();
```


## 总结

在开发与以太坊区块链交互的应用时，监控地址余额的变化是一项基础而重要的功能。不同的监控方法适合不同的应用场景：

- 定期轮询适合对实时性要求不高的场景，实现简单，但可能导致较高的网络与计算资源消耗。
- 监听合约事件提供了较高的实时性和效率，适合对智能合约交互频繁的应用，但其不适用于监控 ETH 原生代币余额，并且受限于合约设计是否提供了必要的事件输出。
- 扫块提供了一种监控每个区块和其中的交易来追踪地址余额变化的方法。

开发者应根据应用的具体需求和资源状况选择最合适的方法，或者在合适的情况下结合使用这些方法，以达到最好的效果和性能。