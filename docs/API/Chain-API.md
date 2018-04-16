# Chain API

查询链内部状态的API.

## Chain C API

### 函数

- uint32_t 	[get_active_producers](#get_active_producers) (account_name *producers, uint32_t datalen)


###详细描述 

###函数说明

- <h5> <span id="get_active_producers">get_active_producers()</span></h5>
    > uint32_t get_active_producers	(account_name * producers, uint32_t 	datalen)
    - 返回活动生产者的集合
    - 参数
        - producers - 一个指向account_names缓冲区的指针
        - datalen - 缓冲区的字节长度
    - 返回
        - 实际填充的字节数
    
- 示例
```
account_name producers[21];
uint32_t bytes_populated = get_active_producers(producers, sizeof(account_name)*21);
```