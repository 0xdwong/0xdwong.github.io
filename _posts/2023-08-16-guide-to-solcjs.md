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
```
npm install solc
```

## 使用
### 查看版本号
`solc --version` 或者 `solc -V`

### 编译合约  
运行以下命令，将生成 abi 和字节码文件  
`solc --bin --abi xxx.sol`

更多参数选项参考下表：

| 参数 |解释 |
| -- |-- |
|  -V, --version                        |  输出版本号  |
|  --version                            |  显示版本并退出  |
|  --optimize                           |  启用字节码优化器（默认值：false） |
|  --optimize-runs <optimize-runs>      |  运行次数大致指定了部署的代码中每个操作码在合约的整个生命周期中将被执行的频率。较低的值将更多地优化初始部署成本，而较高的值将更多地优化高频使用  |
|  --bin                                |  合约二进制数据（以十六进制表示）  |
|  --abi                                |  合约ABI  |
|  --standard-json                      |  开启标准JSON输入/输出模式  |
|  --base-path <path>                   |  项目的根目录。导入回调将尝试将所有导入路径解释为相对于此目录的路径  |
|  --include-path <path...>             |  可用于导入回调的额外源目录。当使用一个包管理器来安装库时，可以使用此选项来指定包被安装的目录。可以多次使用以提供多个位置。  |
|  -o, --output-dir <output-directory>  |  合约输出目录  |
|  -p, --pretty-json                    |  对所有JSON输出进行格式化（默认值：false）  |
|  -v, --verbose                        |  更详细的控制台输出（默认值：false）  |
|  -h, --help                           |  显示命令的帮助信息  |

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