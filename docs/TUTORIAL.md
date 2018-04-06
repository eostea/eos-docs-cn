## EOSIO 开发者指南 

版权声明：
本文由 IMEOS-猫片 翻译，校对：慢雾安全团队、IMEOS-EOS 技术研发团队刘伟江大佬。转载请保留此声明。



本文翻译自 EOS.IO 开发者指南[英文版](https://github.com/EOSIO/eos/wiki/Tutorial-Getting-Started-With-Contracts)，如果有表述不一致的地方，以英文版本为准！



本教程的目的是演示如何配置可用于开发智能合约的本地EOS网络。本教程的第一部分将着重于：

1. 启动一个节点
2. 创建一个钱包
3. 创建账户
4. 部署合同
5. 与合同交互

本教程的第二部分将引导你创建和部署自己的合同。

本教程默认你已经安装了 EOSIO，并且已经生成 `nodeos` 和 `cleos` 工具 。


## 启动一个私链

你可以使用下面命令运行单节点网络:

```
$ nodeos -e -p eosio --plugin eosio::wallet_api_plugin --plugin eosio::chain_api_plugin --plugin eosio::account_history_api_plugin 
...
eosio generated block 046b9984... #101527 @ 2018-04-01T14:24:58.000 with 0 trxs
eosio generated block 5e527ee2... #101528 @ 2018-04-01T14:24:58.500 with 0 trxs

```

该命令设置了许多标志并加载了一些可选的插件，我们将在本教程的其余部分中使用这些插件。假设一切正常，你应该每0.5秒看到一个区块生成消息。

```
eosio generated block 046b9984... #101527 @ 2018-04-01T14:24:58.000 with 0 trxs
```

这意味着您的本地网络处于激活状态，正在生成区块，并可投入使用。

参阅关于 `nodeos` 参数信息请执行：

```
nodeos --help
```

## 创建一个钱包

钱包是一个授权对区块链执行 actions 所必需的私钥库。这些密钥存储在加密的磁盘上 (加密密码是随机生成的高强度密码)。建议将该密码应该存储在安全的密码管理器中。

```
$ cleos wallet create
Creating wallet: default
Save password to use in the future to unlock this wallet.
Without password imported keys will not be retrievable.
"PW5JuBXoXJ8JHiCTXfXcYuJabjF9f9UNNqHJjqDVY7igVffe3pXub"
```

为了实现这个简单的开发环境，你的钱包由本地`nodeos` 配置的`eosio::wallet_api_plugin`管理，这个插件在我们启动 `nodeos`时自动激活。无论任何时候你重新启动 `nodeos` ，在你使用密钥之前，你将必须解锁你的钱包.

```
$ cleos wallet unlock --password PW5JuBXoXJ8JHiCTXfXcYuJabjF9f9UNNqHJjqDVY7igVffe3pXub
Unlocked: default
```

在命令行中直接使用密码通常是不安全的，它会被记录到你的 bash 历史记录中。因此你也可以在交互模式下解锁：

```
$ cleos wallet unlock
password:
```

出于安全考虑，通常最好在不使用钱包时锁定钱包。要锁定你的钱包而不关闭 `nodeos` ，你可以这样做:

```
$ cleos wallet lock
Locked: default
```

您将需要为本教程的其余部分解锁钱包。

所有新的区块链都是以唯一初始帐户`eosio`的私钥开始的。为了与区块链互动，你需要将此初始帐户的私钥导入你的钱包。将`eosio`帐户的私钥导入你的钱包

```
$ cleos wallet import 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
imported private key for: EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
```

## 加载 Bios 合约

现在我们有一个带有`eosio`账户私钥的钱包，我们可以设置默认的系统合约。为了开发的目的，默认可以使用`eosio.bios` 合约。此合约使你可以直接控制其他帐户的资源分配并访问其他特权 API 调用。在公链中，该合同将管理Token的 staking 和 unstaking，为合约预留 CPU、网络活动和内存的带宽。

智能合约 `eosio.bios` 在 EOSIO 源代码的 `contracts/eosio.bios` 文件夹里。cleos 指令默认是在 eosio 源码根目录中执行，但可以通过指定 ${EOSIO_SOURCE}/build/contracts/eosio.bios 的全路径从任意位置来执行它。

```
$ cleos set contract eosio build/contracts/eosio.bios -p eosio
Reading WAST...
Assembling WASM...
Publishing contract...
executed transaction: 414cf0dc7740d22474992779b2416b0eabdbc91522c16521307dd682051af083  4068 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"eosio","vmtype":0,"vmversion":0,"code":"0061736d0100000001ab011960037f7e7f0060057f7e7e7e...
#         eosio <= eosio::setabi                {"account":"eosio","abi":{"types":[],"structs":[{"name":"set_account_limits","base":"","fields":[{"n...
```

这个命令的结果是`cleos` 产生了一个交易和两个 action， `eosio::setcode` 和 `eosio::setabi`。

代码定义了合约如何运行，abi 描述了如何在 binary 和参数的 json 描述文件之间转换。虽然 abi 文件是可选的，但是所有的 EOSIO 工具都依赖于它以便于使用。

无论何时，你执行一个交易都会看到如下输出：

```
$ cleos wallet unlock --password
PW5JuBXoXJ8JHiCTXfXcYuJabjF9f9UNNqHJjqDVY7igVffe3pXub
Unlocked: default
```



```
executed transaction: 414cf0dc7740d22474992779b2416b0eabdbc91522c16521307dd682051af083  4068 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"eosio","vmtype":0,"vmversion":0,"code":"0061736d0100000001ab011960037f7e7f0060057f7e7e7e...
#         eosio <= eosio::setabi                {"account":"eosio","abi":{"types":[],"structs":[{"name":"set_account_limits","base":"","fields":[{"n...
```

这可以理解为：由`eosio`所定义的 action `setcode` 被`eosio`合约以参数`{args...}`执行。

```
#         ${executor} <= ${contract}:${action} ${args...}
> console output from this execution, if any
```

正如我们稍后会看到的，actions 可以被多个合约所处理。这次调用的最后一个参数是 `-p eosio`，这个参数告诉 `cleos` 使用我们之前导入的`eosio`账户的私钥来签名这个action。




## 创建一个账户

现在我们已经建立了基础系统合约，可以开始创建自己的账户。我们将创建 2 个账户`user` 和 `tester`，同时需要一个密钥与每个帐户相关联。在该教程中，我们将为 2 个账户配置同一个密钥。

为此，我们首先为该帐户生成一对私钥。

```
$ cleos create key
Private key: 5Jmsawgsp1tQ3GD6JyGCwy1dcvqKZgX6ugMVMdjirx85iv5VyPR
Public key: EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4

```

然后我们将这个私钥导入我们的钱包：

```
$ cleos wallet import 5Jmsawgsp1tQ3GD6JyGCwy1dcvqKZgX6ugMVMdjirx85iv5VyPR
imported private key for: EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
```
**注意：请确保使用由上方`cleos`命令生成的私钥，而不是这个示例中显示的值。**

密钥不会自动添加到钱包，因此跳过此步骤可能会导致你对你的帐户失去控制权。



## 创建两个账户

接下来，我们将使用上一步骤中创建和导入的私钥，创建两个帐户： `user` 和 `tester`

```
$ cleos create account eosio user  EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
executed transaction: 8aedb926cc1ca31642ada8daf4350833c95cbe98b869230f44da76d70f6d6242  364 bytes  1000 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"user","owner":{"threshold":1,"keys":[{"key":"EOS7ijWCBmoXBi3CgtK7DJxentZZ...

$ cleos create account eosio tester EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
executed transaction: 414cf0dc7740d22474992779b2416b0eabdbc91522c16521307dd682051af083 366 bytes  1000 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"tester","owner":{"threshold":1,"keys":[{"key":"EOS7ijWCBmoXBi3CgtK7DJxentZZ...
```

**注意：`create account`命令需要两个密钥，一个是OwnerKey（生产环境中应保持高度安全），另一个是ActiveKey。在本教程示例中，两者都使用相同的密钥。**

因为我们正在使用 `eosio::account_history_api_plugin` ，所以我们能够查询所有由我们的私钥控制的账户：

```
$ cleos get accounts EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
{
  "account_names": [
    "tester",
    "user"
  ]
}
```



## 创建 Token 合约

在这个阶段，区块链还不能做很多事，因此让我们来部署 `eosio.token` 合约。该合约允许创建许多不同的 token，这些 token 都运行在同一个合约上，但可能由不同的账户管理。 

首先需要创建一个账户来部署这个合约。

```
$ cleos create account eosio eosio.token  EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
...
```

接着可以开始部署合约（合约代码位于 `${EOSIO_SOURCE}/build/contracts/eosio.token`）

```

$ cleos set contract eosio.token build/contracts/eosio.token -p eosio.token
Reading WAST...
Assembling WASM...
Publishing contract...
executed transaction: 528bdbce1181dc5fd72a24e4181e6587dace8ab43b2d7ac9b22b2017992a07ad  8708 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"eosio.token","vmtype":0,"vmversion":0,"code":"0061736d0100000001ce011d60067f7e7f7f7f7f00...
#         eosio <= eosio::setabi                {"account":"eosio.token","abi":{"types":[],"structs":[{"name":"transfer","base":"","fields":[{"name"...
```

### 创建 EOS Token

你可以在 `contracts/eosio.token/eosio.token.hpp` 头文件中查看- `eosio.token` 合约的接口：

```
   void create( account_name issuer,
                asset        maximum_supply,
                uint8_t      can_freeze,
                uint8_t      can_recall,
                uint8_t      can_whitelist );


   void issue( account_name to, asset quantity, string memo );

   void transfer( account_name from,
                  account_name to,
                  asset        quantity,
                  string       memo );
```

要创建一个新的token，我们必须用合适的参数来调用 `create(...)` action。该命令将使用最大的符号，用来从其他 token 中，唯一地鉴别这个 token。发行人将有权要求发行和执行其他 action，例如冻结，召回以及将所有者列入白名单。

使用位置参数来调用这个方法：

```
$ cleos push action eosio.token create '[ "eosio", "1000000000.0000 EOS", 0, 0, 0]' -p eosio.token
executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  260 bytes  1000 cycles
#   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 EOS","can_freeze":0,"can_recall":0,"can_whitelis...

```

使用参数名键值对来调用：

```
$ cleos push action eosio.token create '{"issuer":"eosio", "maximum_supply":"1000000000.0000 EOS", "can_freeze":0, "can_recall":0, "can_whitelist":0}' -p eosio.token
executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  260 bytes  1000 cycles
#   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 EOS","can_freeze":0,"can_recall":0,"can_whitelis...
```


该命令创建一个名为 `EOS` 的新 token，其精密度为4 位数，最大供应量为 1000000000.0000 EOS。

为了创建这个 token，我们需要获得 `eosio.token` 合约的许可，因为它“拥有”符号（e.g. "EOS"）的命名空间。该合同的未来版本可能允许其他地方自动购买符号名称。当前我们必须通过`-p eosio.token` 在此授权。


### 发行 Tokens 给`User`账户

现在我们已经创建了token，发行者可以用我们之前创建的`user`账户发行新的 token。我们根据参数位置来调用 issue 这个 action。

```
$ cleos push action eosio.token issue '[ "user", "100.0000 EOS", "memo" ]' -p eosio
executed transaction: 822a607a9196112831ecc2dc14ffb1722634f1749f3ac18b73ffacd41160b019  268 bytes  1000 cycles
#   eosio.token <= eosio.token::issue           {"to":"user","quantity":"100.0000 EOS","memo":"memo"}
>> issue
#   eosio.token <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
>> transfer
#         eosio <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
#          user <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
```

这次输出包含着几个不同的 action， 发行 和 3 次转账。尽管我们仅仅执行了 `issue`这个 action，但是`issue` action 默认执行了 "inline transfer"，"inline transfer" 通知了发件人和收件人帐户。输出指明所有被调用的 action 处理程序、被调用的顺序，以及是否生成任何输出。



从技术上说，eosio.token 合约可以跳过`inline transfer`并选择直接修改余额，但在这种情况下，eosio.token 合约遵循我们的 token 约定，该约定要求所有账户余额可以通过他们传输 actions 的总和推论出来。它还要求通知资金的发送者和收款人，以便他们能够自动处理存款和提款。

如果你想要看到 broadcast 的实际交易，你可以使用  `-d -j` 选项来表示 "don't broadcast" 和 "return transaction as json".

```
$ cleos push action eosio.token issue '["user", "100.0000 EOS", "memo"]' -p eosio -d -j
{
  "expiration": "2018-04-01T15:20:44",
  "region": 0,
  "ref_block_num": 42580,
  "ref_block_prefix": 3987474256,
  "net_usage_words": 21,
  "kcpu_usage": 1000,
  "delay_sec": 0,
  "context_free_actions": [],
  "actions": [{
      "account": "eosio.token",
      "name": "issue",
      "authorization": [{
          "actor": "eosio",
          "permission": "active"
        }
      ],
      "data": "00000000007015d640420f000000000004454f5300000000046d656d6f"
    }
  ],
  "signatures": [
    "EOSJzPywCKsgBitRh9kxFNeMJc8BeD6QZLagtXzmdS2ib5gKTeELiVxXvcnrdRUiY3ExP9saVkdkzvUNyRZSXj2CLJnj7U42H"
  ],
  "context_free_data": []
}
```

### 转移 Tokens 到 `Tester`账户

现在 `user` 账户有 token， 我们将会把一些转移到账户 `tester`。我们让`user`账户使用权限参数`-p user`来授权这个动作。

```
$ cleos push action eosio.token transfer '[ "user", "tester", "25.0000 EOS", "m" ]' -p user
executed transaction: 06d0a99652c11637230d08a207520bf38066b8817ef7cafaab2f0344aafd7018  268 bytes  1000 cycles
#   eosio.token <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
>> transfer
#          user <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
#        tester <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
```



## Hello World 合约

下一步我们将创建我们的第一个 “hello world” 合约。创建一个名为 “hello” 的新文件夹，然后创建内容文件 “hello / hello.cpp”：

#### hello/hello.cpp
```
#include <eosiolib/eosio.hpp>
#include <eosiolib/print.hpp>
using namespace eosio;

class hello : public eosio::contract {
  public:
      using contract::contract;

      /// @abi action 
      void hi( account_name user ) {
         print( "Hello, ", name{user} );
      }
};

EOSIO_ABI( hello, (hi) )
```

使用如下命令将它编译成 Web Assembly（.wast）：

```
$ eosiocpp -o hello.wast hello.cpp
```

**注意：编译器可能会生成警告，可以放心地忽略。**

现在可以生成 abi:

```
$ eosiocpp -g hello.abi hello.cpp
Generated hello.abi
```

下一步我们创建账户并上传合约：

```
$ cleos create account eosio hello.code EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
...
$ cleos set contract hello.code ../hello -p hello.code
...
```

现在我们可以运行合约：

```
$ cleos push action hello.code hi '["user"]' -p user
executed transaction: 4c10c1426c16b1656e802f3302677594731b380b18a44851d38e8b5275072857  244 bytes  1000 cycles
#    hello.code <= hello.code::hi               {"user":"user"}
>> Hello, user
```

这时合约允许任何人授权，我们也可以这样：

```
$ cleos push action hello.code hi '["user"]' -p tester
executed transaction: 28d92256c8ffd8b0255be324e4596b7c745f50f85722d0c4400471bc184b9a16  244 bytes  1000 cycles
#    hello.code <= hello.code::hi               {"user":"user"}
>> Hello, user
```

这时 `tester` 是授权者，`user` 只是一个参数。如果想要合约对账户进行身份认证，那么我们可以修改下 `hi()` 方法：
```
void hi( account_name user ) {
   require_auth( user );
   print( "Hello, ", name{user} );
}
```

重复上文步骤来编译 wast 文件并生成 abi，然后再次设置合约以部署更新。

现在，我们尝试让账户(对应下文的`tester`)和授权者(对应下文的`user`)不匹配，那么合同会抛出一个错误：

```
$ cleos push action hello.code hi '["tester"]' -p user
Error 3030001: missing required authority
Ensure that you have the related authority inside your transaction!;
If you are currently using 'cleos push action' command, try to add the relevant authority using -p option.
Error Details:
missing authority of tester
```

我们可以通过授予`tester`权限来解决此问题：

```
$ cleos push action hello.code hi '["tester"]' -p tester
executed transaction: 235bd766c2097f4a698cfb948eb2e709532df8d18458b92c9c6aae74ed8e4518  244 bytes  1000 cycles
#    hello.code <= hello.code::hi               {"user":"tester"}
>> Hello, tester
```



## 部署 Exchange 合约

与上述示例类似，我们可以部署 exchange 合约。请确保是从 EOSIO 源的根目录运行的。

```
$ cleos create account eosio exchange  EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
executed transaction: 4d38de16631a2dc698f1d433f7eb30982d855219e7c7314a888efbbba04e571c  364 bytes  1000 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"exchange","owner":{"threshold":1,"keys":[{"key":"EOS7ijWCBmoXBi3CgtK7DJxe...

$ cleos set contract exchange build/contracts/exchange -p exchange
Reading WAST...
Assembling WASM...
Publishing contract...
executed transaction: 5a63b4de8a1da415590778f163c5ed26dc164c960185b20fd834c297cf7fa8f4  35172 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"exchange","vmtype":0,"vmversion":0,"code":"0061736d0100000001f0023460067f7e7f7f7f7f00600...
#         eosio <= eosio::setabi                {"account":"exchange","abi":{"types":[{"new_type_name":"account_name","type":"name"}],"structs":[{"n...
```



本文由 IMEOS-猫片 翻译，校对：慢雾安全团队、IMEOS-EOS 技术研发团队刘伟江大佬。转载请保留此声明。
