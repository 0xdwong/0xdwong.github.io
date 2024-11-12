---
layout: post
title: '瞬态存储：Solidity 中的高效临时数据解决方案'
date: '2024-11-12'
category: eips
permalink: /eips/1153
tags: eips transient storage
---

## 概述

瞬态存储（Transient Storage）是 Solidity 0.8.24 版本中新引入的一种数据位置类型。它提供了一种在单个交易执行期间临时存储数据的机制，这些数据会在交易结束后自动清除。该特性通过 [EIP-1153](https://eips.ethereum.org/EIPS/eip-1153) 提案实现，是对现有数据位置（内存、存储、调用数据）的补充。

## 瞬态存储的应用场景与优势

瞬态存储的需求主要源于以下几点：

1. **高昂的 Gas 成本**：在以太坊中，永久存储（Storage）的 `SSTORE` 操作成本极高，首次写入至少需要 20,000 gas。这使得处理临时数据变得不经济。

2. **状态膨胀**：永久存储的数据会不断累积，导致区块链状态的增长，增加了节点的存储负担。

3. **清理成本**：删除不再需要的存储数据需要额外支付 gas，这进一步增加了管理存储的复杂性和成本。

4. **临时性需求**：许多应用场景只需要临时存储数据。

瞬态存储填补了以太坊现有存储机制的空缺，为开发者提供了一个经济高效的临时数据存储方案，尤其适合那些数据只需在单个交易内有效且需要频繁读写的场景。

以下是关于瞬态存储（Transient Storage）、存储（Storage）、内存（Memory）和调用数据（Calldata）的比较：

| 特性           | 瞬态存储                 | 存储                          | 内存                 | 调用数据                  |
|----------------|------------------------|------------------------------|----------------------|-------------------------|
| **存储位置**   | 临时存储                  | 永久存储                      | 临时存储              | 只读存储                  |
| **Gas 成本**   | 较低（约 100 gas/操作）   | 高昂（首次写入至少 20,000 gas） | 较低                  | 较低                     |
| **作用域**     | 跨函数调用可用            | 跨交易可用                     | 仅限于单个函数调用      | 仅限于函数参数             |
| **状态保持**   | 可以保持状态              | 永久保持状态                   | 不可保持状态           | 不可保持状态               |
| **清理成本**   | 无需清理                 | 需要额外支付 gas                | 无需清理              | 无需清理                  |
| **大小限制**   | 无明显限制                | 受链上状态限制                 | 有限的内存空间          | 有限的输入参数大小         |
| **适用场景**   | 临时数据和计算            | 永久性数据存储                  | 临时数据处理           | 函数参数传递              |


## 瞬态存储实战指南

可以通过以下方式使用瞬态存储：

1. 使用 `tstore` 和 `tload` 汇编指令：

```solidity
pragma solidity ^0.8.24;
contract TransientStorageExample {
    function example() public {
        assembly {
            // 存储值
            tstore(0x01, 100)
            
            // 其它操作

            // 读取值
            let value := tload(0x01)
        }
    }
}
```

2. 使用 `transient` 关键字（在 Solidity 0.8.28 及之后的版本中）：

```solidity
pragma solidity ^0.8.28;

contract TransientStorageExample {
    uint transient tempValue;

    function example() public {
        tempValue = 100;

        // 其它操作

        return tempValue;
    }
}
```

3. 一个使用瞬态存储实现的重入锁：

```solidity
pragma solidity ^0.8.28;

contract TReentrant {
    mapping(address => bool) claimed;
    bool transient locked;

    modifier nonReentrant {
        require(!locked, "Reentrancy attempt");

        locked = true;

        _;

        locked = false;
    }

    function claim() nonReentrant public {
        require(!claimed[msg.sender], "Already claimed");

        // 其它操作

        claimed[msg.sender] = true;
    }
}
```

4. 错误使用瞬态存储的例子：

```solidity
pragma solidity ^0.8.28;

contract TMultiplier {
    uint public transient multiplier;

    function setMultiplier(uint mul) external {
        multiplier = mul;
    }

    function multiply(uint value) external view returns (uint) {
        return value * multiplier;
    }
}
```

```
setMultiplier(100);
multiply(1); // 返回 0，而不是 100
multiply(2); // 返回 0，而不是 200
```

如果该示例使用内存或存储来存储乘数，它将是完全可组合的。无论是将交易拆分为单独的交易还是以某种方式将它们组合在一起，都没有关系。总是会得到相同的结果：在 `multiplier` 设置为 `100` 后，后续调用将分别返回 `100` 和 `200`。这使得可以将来自多个交易的调用批量处理在一起以减少 gas 费用。

瞬态存储可能会破坏这样的用例，因为可组合性不再是理所当然的。在这个例子中，如果调用不是在同一交易中执行的，则 `multiplier` 将被重置，后续对函数 `multiply` 的调用将始终返回 `0`。

## 总结

瞬态存储的数据在当前交易执行期间有效，交易结束后会自动清除，从而确保其临时性。这种存储方式的写入和读取成本显著低于传统存储，能够有效节省交易的 Gas 消耗，特别适合处理临时数据。通过在合适的场景中应用瞬态存储，开发者能够显著提升合约的 Gas 效率，从而为用户节省成本并提高交易的整体性能。

## 参考
- [EIP-1153 瞬态存储操作码](https://eips.ethereum.org/EIPS/eip-1153)