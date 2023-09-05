---
layout: post
title: 'sol2uml 简介'
date: '2023-09-05'
category: tool
permalink: /tool/guide-to-sol2uml
tags: solidity
---

sol2uml 是一款用于 Solidity 智能合约可视化的工具。支持从命令行界面生成合约存储图和合约类图（UML）等。

## 主要功能
- 生成智能合约类图（UML）
- 生成智能合约存储布局图
- 将类 Etherscan 的浏览器上的 Solidity 文件扁平化(Flatten)
- 在类似 Etherscan 的浏览器上对比不同的(已验证)合约


## 如何使用
安装： `npm install -g sol2uml`  
运行命令：`sol2uml [command] <options>`


command 选项：

| 命令       | 备注 |
| :------   | :------ |
| class     | 生成 UML 类图 |
| storage   | 可视化展示合约存储槽 |
| flatten   | 扁平化 Solidity 文件 |
| diff      | 对比合约，可以是地址(浏览器上已验证的合约)或者本地文件 |


options 选项：

| 命令 | 备注 |
| :------ | :------ |
| -f, --outputFormat <value>                     | 输出文件格式（"svg"(默认), "png", "dot", "all"）|
| -o, --outputFileName <value>                   | 输出文件名|
| -i, --ignoreFilesOrFolders <filesOrFolders>    | 忽略文件，","隔开|
| -n, --network <network>                        | 区块链网络（"mainnet"(默认), "goerli", "sepolia", "polygon", "arbitrum", "bsc", "optimism", "gnosis", "base"等）
| -k, --apiKey <key>                             | 区块链浏览器的KEY |
| -bc, --backColor <color>                       | Canvas 背景颜色 (默认白色；"none" 则是透明)|
| -v, --verbose                                  | 使用调试语句运行 (默认: false)|

更多选项见：[https://github.com/naddison36/sol2uml#command-line-interface-cli](https://github.com/naddison36/sol2uml#command-line-interface-cli)

## 举个例子
1. 生成 UML 类图  
    命令：`class [options] <fileFolderAddress>`  
    eg：`sol2uml class 0xd8cbccf15f92865c400a2c1896b7f06e45ea5616 -n goerli -o ./files/uml.svg` 
    ![预览](https://raw.githubusercontent.com/0xdwong/blockchain/1e32b734f1f7d589f009a4c4ffc0272589b84135/sol2uml/files/uml.svg)

2. 生成合约存储布局图  
    命令：`storage [options] <fileFolderAddress>`  
    eg：`sol2uml storage 0xd8cbccf15f92865c400a2c1896b7f06e45ea5616 -n goerli -o ./files/storage.svg`
    ![预览](https://raw.githubusercontent.com/0xdwong/blockchain/1e32b734f1f7d589f009a4c4ffc0272589b84135/sol2uml/files/storage.svg)

3. 合约扁平化  
    命令：`flatten <contractAddress>`  
    eg：`sol2uml flatten 0xd8cbccf15f92865c400a2c1896b7f06e45ea5616 -n goerli -o ./files/flatten.sol`  
    生成文件见：[flatten.sol](https://raw.githubusercontent.com/0xdwong/blockchain/1e32b734f1f7d589f009a4c4ffc0272589b84135/sol2uml/files/flatten.sol)

4. 对比合，输出异同点  
    命令：`diff [options] <addressA> <fileFoldersAddress>`  
    eg：`sol2uml diff 0xd8cbccf15f92865c400a2c1896b7f06e45ea5616 0xbdf39ad789ba094aa57acb8ba3ba903e43fc0d00 -n goerli`


## 参考
- [sol2uml GitHub](https://github.com/naddison36/sol2uml)
