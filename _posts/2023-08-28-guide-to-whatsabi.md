---
layout: post
title: 'WhatsABI 简介'
date: '2023-08-28'
category: tool
permalink: /tool/guide-to-whatsabi
tags: abi whatsabi
---


# WhatsABI 简介
从 EVM 字节码中猜测出 ABI（和其他元数据），即使没有原源代码。[Github](https://github.com/shazow/whatsabi)

## 特点
WhatsABI 在一些重要方面与其他 EVM 分析工具不同：
- 使用 TypeScript 构建，依赖最小化，以便在浏览器中运行并嵌入钱包中
- 所使用的算法仅限于具有较小常数因子的算法，以确保复杂的合同不会导致超时或使用无限内存
- 不依赖于源代码，因此可以与未经验证的合约一起使用。
- 不假设源语言，因此可以适用于除 Solidity（Vyper，甚至手写汇编）之外的源语言
- 开放源代码（MIT 许可证），以便任何人都可以使用它

## 可以做什么
- 从字节码返回选择器
- 从选择器中查找函数签名
- 解决代理合同

## 使用方法
### 安装
```
yarn add @shazow/whatsabi ethers
```

### 使用
```
import { ethers } from "ethers";
import { whatsabi } from "@shazow/whatsabi";

// Or your fav contract address
const address = "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48"; // USDC


let result = await whatsabi.autoload(address, {
  provider: new ethers.getDefaultProvider(),

  // * Optional loaders:
  abiLoader: whatsabi.loaders.defaultABILoader,
  signatureLoader: whatsabi.loaders.defaultSignatureLookup,

  // * Optional hooks:
  // onProgress: (phase: string) => { ... }
  // onError: (phase: string, context: any) => { ... }

  // * Optional settings:
  followProxies: true,
  // enableExperimentalMetadata: false,
});

console.log(result);
```
控制台将打印合约地址、abi 等数据

（完整项目见 [whatsabi](https://github.com/0xdwong/blockchain/tree/main/whatsabi)）

### 其它方法
- `selectorsFromBytecode`:  从合约字节码中获取函数选择器  
    whatsabi.selectorsFromBytecode(code)
- `abiFromBytecode`： 从合约字节码中获取 ABI  
    whatsabi.abiFromBytecode(code)
- `loadFunctions`： 获取函数选择器匹配的函数签名  
    new whatsabi.loaders.OpenChainSignatureLookup().loadFunctions("0x06fdde03")
- `loadEvents`： 获取函数选择器匹配的函数签名  
  new whatsabi.loaders.OpenChainSignatureLookup().loadEvents("0x721c20121297512b72821b97f5326877ea8ecf4bb9948fea5bfcb6453074d37f")

## 注意事项
- 不一定能找到有效的函数选择器
- 有一些参数存在的猜测，不够可靠
- 事件解析有些不稳定
