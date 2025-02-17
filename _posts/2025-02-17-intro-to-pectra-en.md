---
layout: post
title: 'Introduction to the Next Major Ethereum Upgrade: Pectra'
date: '2025-02-17'
category: eips
permalink: /eips/intro-to-pectra-en
tags: ethereum eips pectra
---

The Pectra upgrade is the next major milestone for the Ethereum network, expected to go live on the Ethereum mainnet in April 2025. This upgrade consists of two main parts: the Prague execution layer upgrade and the Electra protocol layer upgrade.

Unlike previous major upgrades, Pectra does not have a prominent main goal but focuses on multiple technical improvements and optimizations. This contrasts with the Dencun upgrade (which significantly reduced L2 fees) or the Shapella upgrade (which allowed the withdrawal of staked ETH, completing the final step in Ethereum's transition to Proof of Stake (PoS)).

## Related EIPs for the Pectra Upgrade

- [EIP-2537](https://eips.ethereum.org/EIPS/eip-2537): Precompiles for BLS12-381 curve operations
- [EIP-2935](https://eips.ethereum.org/EIPS/eip-2935): Save historical block hashes in state
- [EIP-6110](https://eips.ethereum.org/EIPS/eip-6110): On-chain validator deposits
- [EIP-7002](https://eips.ethereum.org/EIPS/eip-7002): Triggerable execution layer exits
- [EIP-7251](https://eips.ethereum.org/EIPS/eip-7251): Increase maximum effective balance
- [EIP-7549](https://eips.ethereum.org/EIPS/eip-7549): Remove committee index from attestations
- [EIP-7623](https://eips.ethereum.org/EIPS/eip-7623): Increase calldata cost
- [EIP-7685](https://eips.ethereum.org/EIPS/eip-7685): General execution layer requests
- [EIP-7691](https://eips.ethereum.org/EIPS/eip-7691): Increase blob throughput
- [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702): Set EOA account code
- [EIP-7840](https://eips.ethereum.org/EIPS/eip-7840): Add blob schedule to EL configuration

## Key EIP Introductions

### EIP-2537: Precompiles for BLS12-381 Curve Operations

This proposal introduces precompiled operations on the BLS12-381 curve, significantly improving the efficiency of operations such as BLS signature verification. Compared to the existing BN254 precompiles, BLS12-381 offers higher security (over 120 bits, while BN254 is only 80 bits). This improvement includes not only basic curve operations but also integrates multi-exponentiation operations, laying the foundation for efficient aggregation of public keys and signatures.

### EIP-2935: Save Historical Block Hashes in State

This proposal suggests storing the hashes of the most recent 8192 blocks in a system contract, primarily to support stateless client execution. In this way, stateless clients can more easily obtain the necessary historical information while maintaining compatibility with the existing BLOCKHASH opcode. This not only simplifies the storage mechanism for block hash history but also provides new ways to access historical data.

### EIP-6110: On-Chain Validator Deposits

This proposal integrates the process of validator deposits directly into the block structure of the Ethereum execution layer. This change shifts the responsibility for deposit inclusion and verification from the consensus layer to the execution layer, eliminating the need for the consensus layer to vote on deposits (or eth1data). By analyzing contract log events of deposit transactions to generate deposit lists, this approach not only improves the security and efficiency of deposit processing but also enhances the user experience. Additionally, it simplifies client software design, reducing overall system complexity.

### EIP-7002: Triggerable Execution Layer Exits

This proposal introduces a new mechanism that allows validators to trigger withdrawal and exit operations by submitting a withdrawal credential through the execution layer (0x01). The specific implementation is to attach a withdrawal message to the execution layer block, which is then processed by the consensus layer. This method provides validators with more flexible exit options while maintaining system security and consistency.

### EIP-7251: Increase Maximum Effective Balance

This proposal aims to increase the maximum effective balance (MAX_EFFECTIVE_BALANCE) for Ethereum validators while maintaining the minimum staking balance at 32 ETH. This change has multiple benefits:

1. Allows large node operators to consolidate into fewer validators, improving operational efficiency.
2. Provides small stakers with the opportunity to earn compound rewards, increasing the attractiveness of staking.
3. Offers more flexible staking options, attracting more participants.
4. Reduces redundant validators in the network, decreasing the number of P2P messages.
5. Reduces the memory footprint of BeaconState, improving system efficiency.
6. Further optimizes the liquidity of the entire Ethereum network in conjunction with enhanced partial withdrawal mechanisms in the execution layer.

### EIP-7549: Remove Committee Index from Attestations

This proposal suggests removing the index field of the committee from the signed attestation message to enable aggregation of the same consensus votes. The main goal of this change is to improve the efficiency of Casper FFG clients by reducing the average number of pairings required to verify consensus rules. While all types of clients can benefit from this improvement, it may bring the most significant performance boost for ZK circuits that need to prove Casper FFG consensus.

### EIP-7623: Increase Calldata Cost

This proposal primarily targets the cost of calldata in transactions to reduce the maximum block size and alleviate the problem of excessively large block sizes. It introduces a new "floor price mechanism" (controlled by the `TOTAL_COST_FLOOR_PER_TOKEN` parameter) to increase the gas cost per byte for transactions with excessive data, thereby suppressing the maximum data size of a single transaction; however, if a transaction relies mostly on EVM execution rather than transmitting large amounts of data, the current low fees are maintained.

This proposal brings the following impacts to the network upgrade: reducing the maximum block size; ensuring that the cost for ordinary users remains unchanged.

### EIP-7685: General Execution Layer Requests

This proposal defines a general framework for storing and processing requests triggered by smart contracts. The specific implementation is to add a field in both the execution header and body to store request information, thereby exposing these requests to the consensus layer, allowing it to process each request. The design of this mechanism is mainly to address the increasing demand for validator control by smart contracts, providing a foundation for more complex on-chain interactions in the future.

### EIP-7691: Increase Blob Throughput

This proposal aims to increase the number of blobs that can be included in a block from the current 3/6 (target/maximum) to 6/9, further enhancing the ability of L1 to provide data to L2. It is based on previous test results of large blocks and large blobs, increasing the target and maximum blob numbers to improve Ethereum's overall throughput. This proposal mainly focuses on simplifying client implementation and testing work while leaving room for further scaling solutions in the future.

### EIP-7702: Set EOA Account Code

Proposed by Vitalik Buterin and others, EIP-7702 aims to optimize Ethereum's account abstraction. This proposal introduces a new transaction type that allows externally owned accounts (EOA) to set account code through an authorization mechanism. This improvement supports several new features:

1. Batch operations: Allows EOAs to perform multiple operations in the same transaction, improving efficiency.
2. Sponsored transactions: Facilitates third-party payment of transaction fees.
3. Permission downgrades: Enhances the security and flexibility of accounts.

By adopting the new transaction structure, this proposal not only enhances the functionality and usability of EOAs but also provides good compatibility and scalability for future account abstraction technologies.

### EIP-7840: Add Blob Schedule to EL Configuration

This proposal suggests adding a new object called blobSchedule to the client configuration file. This object lists the target and maximum blob numbers for each block for each fork. The main motivation is to ensure the ability to dynamically adjust the target and maximum blob numbers for each block, as well as the blob base fee update coefficient, thereby avoiding complex handshakes through the engine API.

## Conclusion

Although the Pectra upgrade does not have a prominent main goal, it will further enhance the functionality, security, and efficiency of the Ethereum network through a series of technical improvements and optimizations.

## References

- [EIP-7600: Pectra Hard Fork Metadata](https://eips.ethereum.org/EIPS/eip-7600)