RPC接口
----
描述怎样去调用eosd的HTTP RPC.

内容列表
---
- [配置](#配置)
- [链接口](#链接口)
    - [get_info](#get_info)
    - [get_block](#get_block)
    - [get_account](#get_account)
    - [get_code](#get_code)
    - [get_table_rows](#get_table_rows)
    - [abi_json_to_bin](#get_json_to_bin)
    - [abi_bin_to_json](#get_bin_to_json)
    - [push_transaction](#push_transaction)
    - [push_transactions](#push_transactions)
    - [get_required_keys](#get_required_keys)
 
- [钱包接口](#钱包接口)
    - [wallet_create](#wallet_create)
    - [wallet_open](#wallet_open)
    - [wallet_lock](#wallet_lock)
    - [wallet_lock_all](#wallet_lock_all)
    - [wallet_import_key](#wallet_import_key)
    - [wallet_wallet_list](#wallet_list)
    - [wallet_list_keys](#wallet_list_keys)
    - [wallet_get_public_keys](#wallet_get_public_keys)
    - [wallet_set_timeout](#wallet_set_timeout)
    - [wallet_sign_trx](#wallet_sign_trx)


# 配置

eosd使用REST RPC接口，插件可以在API服务器上注册自己的端点。 本页将解释如何使用一些API来获取有关区块链和发送事务的信息。

在查询eosd之前，您必须先启用必要的API插件。根据您希望启用的API，将以下行添加到您的eosd的config.ini中：

```C++
plugin = eosio::chain_api_plugin // 生效链API
plugin = eosio::wallet_api_plugin // 生效钱包API
```
另外，对于电子钱包API，您还可以通过单独运行eos-walletd将电子钱包功能与eosd分开。

对于以下指南，我们假定我们已经在127.0.0.1:8888（启用了链接API插件，电子钱包API插件已禁用）以及在127.0.0.1:8889上运行的eos-walletd运行。

# 链接口
## get_info

获取与节点相关的最新信息

### get_info 用法示例

```bash
curl http://127.0.0.1:8888/v1/chain/get_info
```

### get_info结果示例

```json
{
  "server_version": "b2eb1667",
  "head_block_num": 259590,
  "last_irreversible_block_num": 259573,
  "head_block_id": "0003f60677f3707f0704f16177bf5f007ebd45eb6efbb749fb1c468747f72046",
  "head_block_time": "2017-12-10T17:05:36",
  "head_block_producer": "initp",
  "recent_slots": "1111111111111111111111111111111111111111111111111111111111111111",
  "participation_rate": "1.00000000000000000"
}
```

## get_block

获取一个块的信息

### get_block 用法示例

```bash
$ curl  http://127.0.0.1:8888/v1/chain/get_block -X POST -d '{"block_num_or_id":5}'
$ curl  http://127.0.0.1:8888/v1/chain/get_block -X POST -d '{"block_num_or_id":0000000445a9f27898383fd7de32835d5d6a978cc14ce40d9f327b5329de796b}'
```

### get_block 结果示例

```json
{
  "previous": "0000000445a9f27898383fd7de32835d5d6a978cc14ce40d9f327b5329de796b",
  "timestamp": "2017-07-18T20:16:36",
  "transaction_merkle_root": "0000000000000000000000000000000000000000000000000000000000000000",
  "producer": "initf",
  "producer_changes": [ ],
  "producer_signature": "204cb94b3186c3b4a7f88be4e9db9f8af2ffdb7ef0f27a065c8177a5fcfacf876f684e59c39fb009903c0c59220b147bb07f1144df1c65d26c57b534a76dd29073",
  "cycles": [ ],
  "id":"000000050c0175cbf218a70131ddc3c3fab8b6e954edef77e0bfe7c36b599b1d",
  "block_num":5,
  "ref_block_prefix":27728114
}
```

## get_account

获取账户的信息

### get_account 用法示例

```bash
$ curl  http://127.0.0.1:8888/v1/chain/get_account -X POST -d '{"account_name":"inita"}'
```

### get_account 结果示例

```json
{
  "name": "inita",
  "eos_balance": "999998.9574 EOS",
  "staked_balance": "0.0000 EOS",
  "unstaking_balance": "0.0000 EOS",
  "last_unstaking_time": "2106-02-07T06:28:15",
  "permissions": [
    {
      "name": "active",
      "parent": "owner",
      "required_auth": {
        "threshold": 1,
        "keys": [
          {
            "key": "EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",
            "weight": 1
          }
        ],
        "accounts": []
      }
    },
    {
      "name": "owner",
      "parent": "owner",
      "required_auth": {
        "threshold": 1,
        "keys": [
          {
            "key": "EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",
            "weight": 1
          }
        ],
        "accounts": []
      }
    }
  ]
}
```

## get_code

获取智能合约代码

### get_code 用法示例

```bash
$ curl  http://127.0.0.1:8888/v1/chain/get_code -X POST -d '{"account_name":"currency"}'
```

### get_code 结果示例

```json
{
  "name":"currency",
  "code_hash":"a1c8c84b4700c09c8edb83522237439e33cf011a4d7ace51075998bd002e04c9",
  "wast":"(module\n  (type $0 (func (param i64 i64 i32) (result i32)))\n ...truncated",
  "abi": {
  "types": [{
      "new_type_name": "account_name",
      "type": "name"
    }
  ],
  "structs": [{
      "name": "transfer",
      "base": "",
      "fields": [
        {"name":"from", "type":"account_name"},
        {"name":"to", "type":"account_name"},
        {"name":"quantity", "type":"uint64"}
      ]
    },{
      "name": "account",
      "base": "",
      "fields": [
        {"name":"key", "type":"name"},
        {"name":"balance", "type":"uint64"}
      ]
    }
  ],
  "actions": [{
      "name": "transfer",
      "type": "transfer"
    }
  ],
  "tables": [{
      "name": "account",
      "type": "account",
      "index_type": "i64",
      "key_names" : ["key"],
      "key_types" : ["name"]
    }
  ]
  }
}
```

## get_table_rows

获取智能合约数据

### get_table_rows 用法示例

```bash
$ curl  http://127.0.0.1:8888/v1/chain/get_table_rows -X POST -d '{"scope":"inita", "code":"currency", "table":"account", "json": true}'
$ curl  http://127.0.0.1:8888/v1/chain/get_table_rows -X POST -d '{"scope":"inita", "code":"currency", "table":"account", "json": true, "lower_bound":0, "upper_bound":-1, "limit":10}'
```

### get_table_rows 结果示例

```json
{
  "rows": [
    {
      "account": "account",
      "balance": 1000
    }
  ],
  "more": false
}
```

## abi_json_to_bin

将json序列化为二进制十六进制。得到的二进制十六进制通常用于push_transaction中的数据字段。

### abi_json_to_bin 用法示例

```bash
$ curl  http://127.0.0.1:8888/v1/chain/abi_json_to_bin -X POST -d '{"code":"currency", "action":"transfer", "args":{"from":"initb", "to":"initc", "quantity":1000}}'
```

### abi_json_to_bin 结果示例

```json
{
  "binargs": "000000008093dd74000000000094dd74e803000000000000",
  "required_scope": [],
  "required_auth": []
}
```

## abi_bin_to_json

将二进制十六进制序列化为json。

### abi_bin_to_json 用法示例

```bash
$ curl  http://127.0.0.1:8888/v1/chain/abi_bin_to_json -X POST -d '{"code":"currency", "action":"transfer", "binargs":"000000008093dd74000000000094dd74e803000000000000"}'
```

### abi_bin_to_json 结果示例

```json
{
  "args": {
    "from": "initb",
    "to": "initc",
    "quantity": 1000
  },
  "required_scope": [],
  "required_auth": []
}
```

## push_transaction

此方法预期采用JSON格式的事务，并将尝试将其应用于区块链。

### 成功的返回

成功时它将返回HTTP 200和事务ID。

```json
{
  "transaction_id": "..."
}
```

仅仅因为交易是在本地进行的，并不意味着交易已经被合并到一个区块中。

### 错误的返回

如果发生错误，它将返回HTTP 400（无效参数）或500（内部服务器错误）

```
HTTP/1.1 500 Internal Server Error
Content-Length: 1466
...error message...
```

## push_transactions

该方法一次推送多个事务。

### push_transactions 用法示例

```bash
curl  http://localhost:8888/v1/chain/push_transaction -X POST -d '[{"ref_block_num":"101","ref_block_prefix":"4159312339","expiration":"2017-09-25T06:28:49","scope":["initb","initc"],"actions":[{"code":"currency","type":"transfer","recipients":["initb","initc"],"authorization":[{"account":"initb","permission":"active"}],"data":"000000000041934b000000008041934be803000000000000"}],"signatures":[],"authorizations":[]}, {"ref_block_num":"101","ref_block_prefix":"4159312339","expiration":"2017-09-25T06:28:49","scope":["inita","initc"],"actions":[{"code":"currency","type":"transfer","recipients":["inita","initc"],"authorization":[{"account":"inita","permission":"active"}],"data":"000000008040934b000000008041934be803000000000000"}],"signatures":[],"authorizations":[]}]'
```

## get_required_keys

获取必需的密钥，从密钥列表中签署交易。

### get_required_keys 用法示例

```bash
curl  http://localhost:8888/v1/chain/get_required_keys -X POST -d '{"transaction": {"ref_block_num":"100","ref_block_prefix":"137469861","expiration":"2017-09-25T06:28:49","scope":["initb","initc"],"actions":[{"code":"currency","type":"transfer","recipients":["initb","initc"],"authorization":[{"account":"initb","permission":"active"}],"data":"000000000041934b000000008041934be803000000000000"}],"signatures":[],"authorizations":[]}, "available_keys":["EOS4toFS3YXEQCkuuw1aqDLrtHim86Gz9u3hBdcBw5KNPZcursVHq","EOS7d9A3uLe6As66jzN8j44TXJUqJSK3bFjjEEqR4oTvNAB3iM9SA","EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV"]}'```
```

### get_required_keys 结果示例

```json
{
  "required_keys": [
    "EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV"
  ]
}
```

## get_required_keys

获取必需的密钥，从密钥列表中签署交易。

### get_required_keys 用法示例

```bash
curl  http://localhost:8888/v1/chain/get_required_keys -X POST -d '{"transaction": {"ref_block_num":"100","ref_block_prefix":"137469861","expiration":"2017-09-25T06:28:49","scope":["initb","initc"],"actions":[{"code":"currency","type":"transfer","recipients":["initb","initc"],"authorization":[{"account":"initb","permission":"active"}],"data":"000000000041934b000000008041934be803000000000000"}],"signatures":[],"authorizations":[]}, "available_keys":["EOS4toFS3YXEQCkuuw1aqDLrtHim86Gz9u3hBdcBw5KNPZcursVHq","EOS7d9A3uLe6As66jzN8j44TXJUqJSK3bFjjEEqR4oTvNAB3iM9SA","EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV"]}'```
```

### get_required_keys 结果示例

```json
{
  "required_keys": [
    "EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV"
  ]
}
```

# 钱包接口


## wallet_create

用给定的名称创建一个新的钱包.

### wallet_create 用法示例

```bash
$ curl http://localhost:8889/v1/wallet/create -X POST -d '"default"'
```

### wallet_create 结果示例

该命令将返回将来可用于解锁钱包的密码.

```
PW5KFWYKqvt63d4iNvedfDEPVZL227D3RQ1zpVFzuUwhMAJmRAYyX
```

## wallet_open

打开给定名称的现有钱包.

### wallet_open 用法示例

```bash
$ curl http://localhost:8889/v1/wallet/open -X POST -d '"default"'
```

### wallet_open 结果示例

```json
{}
```

## wallet_lock_all

锁定所有钱包.

### wallet_lock_all 用法示例

```bash
$ curl http://localhost:8889/v1/wallet/lock_all 
```

### wallet_lock_all 结果示例

```json
{}
```

## wallet_unlock

用给定的名称和密码解锁钱包.

### wallet_unlock 用法示例

```bash
$ curl http://localhost:8889/v1/wallet/unlock -X POST -d '["default", "PW5KFWYKqvt63d4iNvedfDEPVZL227D3RQ1zpVFzuUwhMAJmRAYyX"]'
```

### wallet_unlock 结果示例

```json
{}
```

## wallet_list

列出所有钱包.

### wallet_list 用法示例

```bash
$ curl http://localhost:8889/v1/wallet/list_wallets
```

### wallet_list 结果示例

```json
["default *"]

```

## wallet_list_keys

列出所有钱包中的所有密钥对.

### wallet_list_keys 用法示例

```bash
$ curl http://localhost:8889/v1/wallet/list_keys
```

### wallet_list_keys 结果示例

```json
[["EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"]]

```

## wallet_get_public_keys

列出所有钱包中的所有公钥.

### wallet_get_public_keys 用法示例

```bash
$ curl http://localhost:8889/v1/wallet/get_public_keys 
```

### wallet_get_public_keys 结果示例

```json
["EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV"]
```

## wallet_set_timeout

设置钱包自动锁定超时（以秒为单位）.

### wallet_set_timeout 用法示例

```bash
$ curl http://localhost:8889/v1/wallet/set_timeout -X POST -d '10'
```

### wallet_set_timeout 结果示例

```json
{}
```

## wallet_sign_trx

给定一个事务数组的签名事务，需要公钥和链ID.

### wallet_sign_trx 用法示例

```bash
$ curl http://localhost:8889/v1/wallet/sign_transaction -X POST -d '[{"ref_block_num":21453,"ref_block_prefix":3165644999,"expiration":"2017-12-08T10:28:49","scope":["initb","initc"],"read_scope":[],"messages":[{"code":"currency","type":"transfer","authorization":[{"account":"initb","permission":"active"}],"data":"000000008093dd74000000000094dd74e803000000000000"}],"signatures":[]}, ["EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV"], ""]'```
```

### wallet_sign_trx 结果示例

```json
{
  "ref_block_num": 21453,
  "ref_block_prefix": 3165644999,
  "expiration": "2017-12-08T10:28:49",
  "scope": [
    "initb",
    "initc"
  ],
  "read_scope": [],
  "messages": [
    {
      "code": "currency",
      "type": "transfer",
      "authorization": [
        {
          "account": "initb",
          "permission": "active"
        }
      ],
      "data": "000000008093dd74000000000094dd74e803000000000000"
    }
  ],
  "signatures": [
    "1f393cc5ce6a6951fb53b11812345bcf14ffd978b07be386fd639eaf440bca7dca16b14833ec661ca0703d15e55a2a599a36d55ce78c4539433f6ce8bcee0158c3"
  ]
}
```