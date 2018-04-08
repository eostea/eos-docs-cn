Builtin Types
---

指定typedefs和别名

模块
---
> [fixed size key sorted lexicographically(固定大小的按键按字典顺序排序)]()  

> [Variable Length Integer(可变长度整数)]()

类
---
> struct [public key]()   

> struct [signature]()

> struct [checksum256]()

> struct [checksum160]()

> struct [checksum512]()

> struct [fixed_string16]()

> struct [fixed_string32]()

> struct [account_permission]()

> struct [eosio::name]()   
封装一个[uint64_t]()以确保它只传递给期望Name的方法.


宏
---
> \#define [PACKED(X)](#PACKED) __attribute((packed)) X

> \#define [N(X)](#N) ::eosio::string_to_name(#X)   
用于从X的base32编码的字符串解释中生成一个编译时间[uint64_t]()

类型定义
---

 
> typedef [uint64_t]() 	[account_name](#permission_name)
 
> typedef [uint64_t]() 	[permission_name](#permission_name)
 
> typedef [uint64_t]() 	[token_name](#token_name)
 
> typedef [uint64_t]() 	[table_name](#table_name)
 
> typedef [uint32_t]() 	[time](#time)
 
> typedef [uint64_t]() 	[scope_name](#scope_name)
 
> typedef [uint64_t]() 	[action_name](#action_name)
 
> typedef [uint16_t]() 	[region_id](#region_id)
 
> typedef [uint64_t]() 	[asset_symbol](#asset_symbol)
 
> typedef [int64_t]() 	[share_type](#share_type)
 
> typedef [uint16_t]() 	[weight_type](#weight_type)
 
> typedef struct [checksum256]() 	[transaction_id_type](#transaction_id_type)
 
> typedef struct [fixed_string16]() 	[field_name](#field_name)
 
> typedef struct [fixed_string32]() 	[type_name](#type_name)

详细描述
----

宏定义文档
---



