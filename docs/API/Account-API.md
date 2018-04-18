# Account API
# Account C API   
查询账户数据的c语言api.

## 函数

> bool 	account_balance_get(void *balance, uint32_t len)   
检索所提供帐户的余额

## 函数文档

* <h5>account_balance_get()   </h5>
bool 	account_balance_get(void *balance, uint32_t len)   

> * 参数:   
    * balance - 指向存储余额数据的一系列内存的指针   
    * len - 存储余额数据的内存范围的长度
       
> * Return:     
     如果账户信息被检索到返回: `true`   
> * 前提:   
    数据是指向至少datalen字节长的内存范围的有效指针   
    数据是指向balance对象的指针\*((uint64_t\*)data)存储主键   
    
例子:
```C
balance b;
b.account = N(myaccount);
balance(b, sizeof(balance));
```

# Account CPP API    
查询账户数据的c++语言api.例子: account balance

## 类
> struct  [eosio::account::account_balance](#h2_tag)　　  
账户余额的二进制结构

## 函数
* bool eosio::accout::get(account_balance &acnt)  
返回一个账户余额结构

## 函数说明
* <h5>bool eosio::account::get(account_balance & acnt) </h5>  
 	返回一个账户余额结构

>* 参数:
    * acnt - 账户
>* 返回  
如果找到帐户的余额，则为`true`

<h2><span id="h2_tag">eosio::account::account_balance 类型说明</span></h2>
账户余额的二进制结构


\#include<account.hpp\>


### 公共属性

* <h5>account_name 	[account](#account)   </h5>
 	所查余额的账户名称
 
* <h5>asset [eos_balance](#eos_balance)  </h5>
 	账户的余额
 
* <h5>asset 	[staked_balance](#staked_balance)  </h5> 
 	账户的抵押余额
 
* <h5>asset 	[unstaking_balance](#unstaking_balance)   </h5>
 	账户正在取消抵押的余额
 
* <h5>time 	[last_unstaking_time](#last_unstaking_time) </h5>  
 	这个账户最后取消抵押余额的时间</h5>

### 详细的描述
例子:
```c++
account_balance test1_balance;
test1_balance.account = N(test1);
if (account_api::get(test1_balance))
{
   eosio::print("test1 balance=", test1_balance.eos_balance, "\n");
}
```

### 数据成员说明文档

<h5 id="account">account</h5>
> account_name eosio::account::account_balance::account   

        所查余额的账户名称

<h5 id="eos_balance">eos_balance</h5>
> asset eosio::account::account_balance::eos_balance   
        
     账户的余额

<h5 id="last_unstaking_time">last_unstaking_time</h5>   
>time eosio::account::account_balance::last_unstaking_time   

    账户正在取消抵押的余额

<h5 id="staked_balance">staked_balance</h5>
> asset eosio::account::account_balance::staked_balance   

    账户的抵押余额

<h5 id="unstaking_balance">unstaking_balance</h5>
> asset eosio::account::account_balance::unstaking_balance   

    这个账户最后取消抵押余额的时间

### 源文档
<h5> contracts/eosiolib/account.hpp</h5>
