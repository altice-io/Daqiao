# DaQiao

## 1 Background and Goals

Polkadot is a heterogeneous multi-chain architecture, which aims to be an extensible heterogeneous multi-chain framework. Under the basic function of ensuring security and transmission, through the incentive mechanism of untrusted nodes, it weakens the endogenous binding relationship and realizes the interconnection and interoperability between different blockchain systems.

Although blockchain have experienced 10 years of development, the existing public chain infrastructure is still immature, and the mainstream public blockchain are completely open, which is not suitable for the application scenario of commercial companies. Therefore, many practical blockchain business landed in the form of alliance blockchain. At present, there are many valuable data in various consortium blockchain, forming different data islands.

1. Open the cross-chain communication between the existing alliance chain and the mainstream open chain;

2. Ensuring data privacy between different chains, ensuring data availability and invisibility, thus protecting the interests of the alliance itself;

Based on the above background, our Altice. IO team's contest content is mainly through substrate to get through **Fabric** and **Eth**. Subsequently, we will continue to implement cross-chain privacy protection, and promote the existing alliance chain ecosystem access to Polkadot ecosystem, and ultimately build the bottom of the Internet's credit value.

## 2 Integral Design

![图片]()

## 3 Detailed Design

The whole architecture consists of three modules: runtime, endorser and qrml. Among them, runtime is the main logic of chain operation, endorser is the main logic of bridge, and qrml is the dependent runtime lib. In DaQiao, it mainly relies on a token protocol of erc20. The detailed design of each module is shown below.

### 3.1 Runtime
#### Structure

` PledgeInfo `is the details of a pledge, which is used to describe the basic information of a pledge.
```
// PledgeInfo Describe the details of the pledge record.
pub struct PledgeInfo <U>{
  // ChainId of the third party chain
  chain_id: ChainId,
  // Txid in the third party chain
  ext_txid: ExtTxID,
  // Account of DaQiao
  account_id: U,
  // The amount of money pledged
  pledge_amount: u128,
  // Can be withdraw
  can_withdraw: bool,
  // The withdraw history of pledge history
  withdraw_history: Vec<ExtTxID>
}
```

#### Storage
Runtime mainly stores `ChainToken'and `PledgeRecords', and `ChainToken' stores the external chain information registered with DaQiao. ` PledgeRecords `stores the details of all pledges.

```
    // Third party chain Id => TokenId
    ChainToken get(chain_token): map ChainId => Option<TokenId<T>>;
    
    // Third party chain TxId => PledgeInfo
    PledgeRecords get(pledge_records): map ExtTxID => PledgeInfo<T::AccountId>;
```

### Interface

Runtime has three main interfaces, namely `register', `pledge', `withdraw'.

** register** is the interface between the external chain and DaQiao.
```
    pub fn register(origin, chain_id: ChainId, token_id: TokenId<T>) -> Result {
      Self::_register(origin, chain_id, token_id)
    }
    
```

** pledge** External Chain Trading Interface to DaQiao Pledge.
```
    pub fn pledge(origin, chain_id: ChainId, ext_txid: ExtTxID) -> Result {
      Self::_pledge(origin, chain_id, ext_txid)
    }
```
The main process of pledge is as follows:
![图片]()

** withdraw** is the interface to revoke the pledge of external chain.
```
    pub fn withdraw(origin, chain_id: ChainId, ext_txid: ExtTxID, ext_address: Vec<u8>,value: T::TokenBalance) -> Result {
      Self::_withdraw(origin, chain_id, ext_txid, ext_address, value)
    }

```
The main process of revocation is as follows:
![图片]()

## 4 RoadMap






启动双节点

./target/debug/daqiao --chain local \
--base-path /tmp/alice \
--alice \
--node-key 0000000000000000000000000000000000000000000000000000000000000001 \
--telemetry-url ws://telemetry.polkadot.io:1024 \
--validator \
--eth-chain ropsten \
--eth-base-path ${DATA_PATH_0}

./target/debug/daqiao --chain local \
--base-path /tmp/bob \
--bootnodes /ip4/127.0.0.1/tcp/30333/p2p/QmRpheLN4JWdAnY7HGJfWFNbfkQCb6tFf4vvA6hgjMZKrR \
--bob \
--telemetry-url ws://telemetry.polkadot.io:1024 \
--validator \
--eth-chain ropsten \
--eth-base-path ${DATA_PATH_1}
