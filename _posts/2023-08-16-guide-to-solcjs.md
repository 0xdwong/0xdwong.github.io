---
layout: post
title: 'solcjs 入门'
date: '2023-08-16'
category: solidity
permalink: /solidity/guide-to-solcjs
tags: solidity solc
---

## 简介
[solc-js](https://github.com/ethereum/solc-js) 是一个 JavaScript 库，为 Solidity 编译器提供绑定。它允许开发人员将 Solidity 智能合约编译为可以部署在以太坊区块链上的字节码。

## 安装
`npm install solc`

## 快速上手
编译合约，将生成 abi 和字节码文件
`solcjs --bin --abi <xxxContract>.sol`

## 高级API
### compile
通过编译器标准格式输入合约数据，生成标准输出格式文件。详情见[编译器标准输入输出](https://solidity.readthedocs.io/en/v0.5.0/using-the-compiler.html#compiler-input-and-output-json-description)  

例子：
```
var solc = require('solc');

var input = {
  language: 'Solidity',
  sources: {
    'test.sol': {
      content: 'contract C { function f() public { } }'
    }
  },
  settings: {
    outputSelection: {
      '*': {
        '*': ['*']
      }
    }
  }
};

var output = JSON.parse(solc.compile(JSON.stringify(input)));

// `output` here contains the JSON output as specified in the documentation
for (var contractName in output.contracts['test.sol']) {
  console.log(
    contractName +
      ': ' +
      output.contracts['test.sol'][contractName].evm.bytecode.object
  );
}
```

这里有更多例子：[solcjs demo](https://github.com/0xdwong/blockchain/tree/main/solidity/solcjs)

## 参考资料
- [solc-js](https://github.com/ethereum/solc-js)
