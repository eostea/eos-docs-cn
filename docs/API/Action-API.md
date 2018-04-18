# Action API

定义用于查询操作属性的C语言API.

# 模块


## Action C API   
定义用于查询操作属性的API.

###　函数

##### uint32_t [read_action_data](#read_action_data) (void *msg, uint32_t len)

 	将当前操作数据复制到指定的位置.
 
##### uint32_t [action_data_size](#action_data_size) ()

 	获取当前操作的数据字段的长度。

 
##### void [require_recipient](#require_recipient) (account_name name)
 	
 	将指定的帐户添加到要通知的帐户集。
 
##### void [require_auth](#require_auth) (account_name name)

 	验证指定的帐户是否存在于所提供的一组授权中。
 
##### bool [has_auth](#has_auth) (account_name name)
 
##### void [require_auth2](#require_auth2) (account_name name, permission_name permission)
 	
 	验证指定的帐户是否存在于所提供的一组授权中。
 
##### void [send_inline](#send_inline) (char *serialized_action, size_t size)
 
##### void [send_context_free_inline](#send_context_free_inline) (char *serialized_action, size_t size)
 
##### void [require_write_lock](#require_write_lock) (account_name name)
 	
 	验证该名称存在于持有的写锁集合中。
 
##### void [require_read_lock](#require_read_lock) (account_name name)

 	验证该名称存在于保持的读锁集合中。
 
##### time [publication_time](#publication_time) ()

 	获取发布时间。
 
##### account_name [current_sender](#current_sender) ()

 	获取操作的执行者。
 
##### account_name [current_receiver](#current_receiver) ()

 	获取该动作的当前接收者。
 	
### 详细描述

EOS.IO的action具有以下抽象结构：    

```c++
struct action {
  scope_name scope; // the contract defining the primary code to execute for code/type
  action_name name; // the action to be taken
  permission_level[] authorization; // the accounts and permission levels provided
  bytes data; // opaque data processed by code
};
```
    该API使您的合同能够检查当前操作的字段并相应采取行动。

例子：

```c++
// Assume this action is used for the following examples:
// {
//  "code": "eos",
//  "type": "transfer",
//  "authorization": [{ "account": "inita", "permission": "active" }],
//  "data": {
//    "from": "inita",
//    "to": "initb",
//    "amount": 1000
//  }
// }
char buffer[128];
uint32_t total = read_action(buffer, 5); // buffer contains the content of the action up to 5 bytes
print(total); // Output: 5
uint32_t msgsize = action_size();
print(msgsize); // Output: size of the above action's data field
require_recipient(N(initc)); // initc account will be notified for this action
require_auth(N(inita)); // Do nothing since inita exists in the auth list
require_auth(N(initb)); // Throws an exception
print(now()); // Output: timestamp of last accepted block
```

### 函数文档

<h5 id="action_data_size">action_data_size()</h5>
   > uint32_t action_data_size()

    获取当前动作的数据字段的长度此方法对动态大小的action很有用

   - 返回    
    当前操作的数据字段的长度
    
<h5 id="current_receiver">current_receiver()</h5>
   > account_name current_receiver()	

    获取action的接收者。

   - 返回
   
     当前action接收者的帐户
     
<h5 id="current_sender">current_sender()</h5>
   > account_name current_sender()

    获取当前action的发送者账户

   - 返回
        当前action发送者的帐户

<h5 id="has_auth">has_auth()</h5>
   > bool has_auth(account_name name)
   
<h5 id="publication_time">publication_time()</h5>
   > time publication_time()

    返回publication_time 1970年以秒为单位的时间.

   - 返回       
    返回publication_time 1970年以秒为单位的时间.
    
<h5 id="read_action_data">read_action_data()</h5>
   > uint32_t read_action_data(void * msg, uint32_t len)		

    将当前操作数据的len个字节复制到指定的位置

   - 参数    
        - msg	- 一个指针，最多可以复制当前操作数据的len个字节    
        - len	- len将要复制的当前操作数据    
   - 返回    
        复制到msg的字节数
        
<h5 id="require_auth">require_auth()</h5>
   > void require_auth(account_name name)

    验证该操作中提供的身份验证集中是否存在该名称。 如果找不到，则throws

   - 参数    
        name　- 待验证账户的名称
        
<h5 id="require_auth2">require_auth2()</h5>
   >void require_auth2(account_name name,permission_name permission)		

    验证该操作中提供的身份验证集中是否存在该名称。 如果找不到，则throws

  - 参数    
    - name	- 待验证账户的名称    
    - permission	- 权限级别进行验证    

<h5 id="require_read_lock">require_read_lock()</h5>
   > void require_read_lock(account_name name)	

    验证该动作中保存的读锁的名称是否存在。 如果找不到，则throws

   - 参数    
      - name	- 待验证账户的名称    
<h5 id="require_recipient">require_recipient()</h5>
   > void require_recipient(account_name name)	

    将指定的帐户添加到要通知的帐户集

   - 参数    
        - name	- 待验证账户的名称   
        
<h5 id="require_write_lock">require_write_lock()</h5>
   > void require_write_lock(account_name name)	

    验证该操作中保存的写锁集合中是否存在该名称。 如果找不到，则throws

   - 参数    
       - name	- 待验证账户的名称
       
<h5 id="send_context_free_inline">send_context_free_inline()</h5>
   > void send_context_free_inline(char * serialized_action, size_t size)		

    在此操作的父事务处理的上下文中发送内联上下文免费操作

   - 参数    
        - serialized_action	- 序列化操作    
        - size	- 序列化操作的大小，以字节为单位

<h5 id="send_inline">send_inline()</h5>
   >void send_inline(char * serialized_action, size_t size)

    在此操作的父事务处理的上下文中发送内联操作

   - 参数    
        - serialized_action	- 序列化操作    
        - size	- 序列化操作的大小，以字节为单位   

## Action CPP API   
类型安全的对Action C API的C++封装.


### 类
##### struct  [eosio::permission_level]()
 
##### struct  [eosio::action]()
 
##### struct  [eosio::action_meta< Account, Name >]()
 
### 函数

###### template< typename T \>
> T 	eosio::[current_action_data](#current_action_data) ()
 	
 	将行动主体解释为T型。
 
###### template< typename T \>
T 	eosio::[unpack_action_data](#unpack_action_data) ()
 
###### template< typename... accounts \>

void eosio::[require_recipient](#require_recipient) (account_name name, accounts... remaining_accounts)
 	
 	验证指定的帐户是否存在于一组通知帐户中。
 
void 	eosio::[require_auth](#require_auth) (const permission_level &level)
 
###### template< typename T , typename... Args \>

void 	eosio::[dispatch_inline](#dispatch_inline) (permission_level perm, account_name code, action_name act, void(T::*)(Args...), std::tuple< Args... > args)
 

详细描述
---
> 注意：
> Action C API中有一些方法可以直接从C ++中使用.

###　函数文档

<h5 id="current_action_data">current_action_data()</h5>
> template< typename T \>    
> T eosio::current_action_data()	

    此方法尝试将操作主体重新解释为T类型。这仅在操作没有动态字段且T类型的结构包装已正确定义时才起作用。

例子:
```
struct dummy_action {
  char a; //1
  unsigned long long b; //8
  int  c; //4
};
dummy_action msg = current_action_data<dummy_action>();
```

<h5 id="dispatch_inline">dispatch_inline()</h5>
> template< typename T,typename... Args \>   
> void eosio::dispatch_inline(permission_level perm,account_name code, action_name act, void(T::*)(Args...) ,std::tuple<Args... >args )		

<h5 id="require_auth">require_auth()</h5>
> void eosio::require_auth(const permission_level& level)	

<h5 id="require_recipient">require_recipient()</h5>
> template<typename... accounts \>    
> void eosio::require_recipient(account_name name, accounts... remaining_accounts)
		
    所有列出的账户将被添加到要通知的账户集合中

    这种辅助方法使您能够通过一次调用将多个账户添加到要通知列表的账户，而不必多次调用类似的C API。

> 注意
action.code也被视为通知帐户集的一部分

例子:
```
require_recipient(N(Account1), N(Account2), N(Account3)); // throws exception if any of them not in set.
```

<h5 id="unpack_action_data">unpack_action_data()</h5>
> template<typename T \>
> T eosio::unpack_action_data()
