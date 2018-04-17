# Math API

定义常用的数学函数.

# 模块

## Math C API   
定义使用更高抽象的基本数学运算.

### 函数

- <h5> void 	[multeq_i128](#multeq_i128) (uint128_t *self, const uint128_t *other)</h5>
 	- 乘以两个128位无符号位整数。 如果指针无效，则抛出异常。

- <h5> void 	[diveq_i128](#diveq_i128) (uint128_t *self, const uint128_t *other)</h5>
 	- 将两个128位无符号位整数相除，并在无效指针时引发异常.

- <h5> uint64_t 	[double_add](#double_add) (uint64_t a, uint64_t b)</h5>
 	- double类型之间相加。

- <h5> uint64_t 	[double_mult](#double_mult) (uint64_t a, uint64_t b)</h5>
 	-  double类型之间相乘

- <h5>uint64_t 	[double_div](#double_div) (uint64_t a, uint64_t b)</h5>
 	-  double类型之间相除

- <h5>uint32_t 	[double_lt](#double_lt) (uint64_t a, uint64_t b)</h5>
 	- double类型小于比较。

- <h5>uint32_t 	[double_eq](#double_eq) (uint64_t a, uint64_t b)</h5>
 	- double类型相等比较。

- <h5>uint32_t 	[double_gt](#double_gt) (uint64_t a, uint64_t b)</h5>
 	- double类型大于比较。

- <h5>uint64_t [double_to_i64](#double_to_i64) (uint64_t a)</h5>
 	- 将double转换为64位无符号整数。

- <h5>uint64_t 	[i64_to_double](#i64_to_double) (uint64_t a)</h5>
 	- 将64位无符号整数转换为double（解释为64位无符号整数）。

### 详细描述

### 函数说明

- <h5> <span id="diveq_i128">diveq_i128()</span></h5>

    > void diveq_i128	(uint128_t * self, const uint128_t *  other )   
    
        将两个128位无符号整数相除，并将该值分配给第一个参数。如果orther值为0，它会抛出异常。   
    - 参数
        - self 指向分子的指针。它将被替换为结果
        - other 指向分母的指针　　　

例子:

```c++
uint128_t self(100);
uint128_t other(100);
diveq_i128(&self, &other);
printi128(self); // Output: 1
```

- <h5 id="double_add">double_add()</h5>

    > uint64_t double_add( uint64_t a, uint64_t b)    
    
        获得两个double之间的加法结果，解析为64位无符号整数此函数首先将两个输入重新解码为双精度（50个十进制数字精度），将它们相加，然后reinterpret_cast将结果重新解析为64位无符号整数。　　　
    - 参数
        - a 双精度解释为64位无符号整数
        - b 双精度解释为64位无符号整数
    - 返回
        - reinterpret_cast将相加得到的64位无符号整数的结果
        　　　　

例子:

```c++
uint64_t a = double_div( i64_to_double(5), i64_to_double(10) );
uint64_t b = double_div( i64_to_double(5), i64_to_double(2) );
uint64_t res = double_add( a, b );
printd(res); // Output: 3
```
- <h5 id="double_div">double_div()</h5>
    > uint64_t double_div(uint64_t a, uint64_t 	b )     
    
        得到两个双精度解的结果，解释为64位无符号整数该函数首先将两个输入重新解码为双精度（50位十进制数字精度），用分母分隔分子，然后将结果重新解释为64位无符号整数。 如果b为零（在reinterpret_cast加倍后）将引发错误.
    - 参数
        - a 双分子解释为64位无符号整数
        - b 双分母被解释为64位无符号整数
    - 返回
        - 相除的结果reinterpret_cast转换为64位无符号整数　
        　　
例子:

```c++
uint64_t a = double_div( i64_to_double(10), i64_to_double(100) );
printd(a); // Output: 0.1
```
- <h5 id="double_eq">double_eq()</h5>
    > uint32_t double_eq(uint64_t a, uint64_t 	b )     
    
        获取两个double之间的相等检查结果在进行相等检查之前，此函数将首先重新解释两个输入以重复（50位十进制数字精度）    
    - 参数
        - a 双精度解析为64位无符号整数
        - b 双精度解析为64位无符号整数
    - 返回
        - 如果第一个输入等于第二个输入，则为1，否则为0　　
        　
例子:

```c++
int64_t a = double_div( i64_to_double(10), i64_to_double(10) );
uint64_t b = double_div( i64_to_double(5), i64_to_double(2) );
uint64_t res = double_eq( a, b );
printi(res); // Output: 0
```
- <h5 id="double_gt">double_gt()</h5>
    > uint32_t double_gt(uint64_t a, uint64_t 	b )     
    
        两个双精度进行大于比较获得大的一个，此函数首先将两个输入重新解释为双精度（50个十进制数精度）。    
    - 参数
        - a 双精度解析为64位无符号整数
        - b 双精度解析为64位无符号整数
    - 返回
        - 如果第一个输入大于于第二个输入，则为1，否则为0　　
        　
例子:

```c++
uint64_t a = double_div( i64_to_double(10), i64_to_double(10) );
uint64_t b = double_div( i64_to_double(5), i64_to_double(2) );
uint64_t res = double_gt( a, b );
printi(res); // Output: 0
```

- <h5 id="double_lt">double_lt()</h5>
    > uint32_t double_lt(uint64_t a, uint64_t 	b )     
    
        获得两个双精度小于比较的结果，该函数首先将两个输入重新解码为双精度（50个十进制数字精度）。    
    - 参数
        - a 双精度解析为64位无符号整数
        - b 双精度解析为64位无符号整数
    - 返回
        - 如果第一个输入小于于第二个输入，则为1，否则为0
        　　　　
例子:

```c++
uint64_t a = double_div( i64_to_double(10), i64_to_double(10) );
uint64_t b = double_div( i64_to_double(5), i64_to_double(2) );
uint64_t res = double_lt( a, b );
printi(res); // Output: 1
```

- <h5 id="double_mult">double_mult()</h5>
    > uint64_t double_mult(uint64_t a, uint64_t 	b )     
    
        获得两个double之间的乘法结果，解释为64位无符号整数此函数首先将两个输入重新解码为双精度（50位十进制精度），然后将它们相乘，然后reinterpret_cast将结果重新解析为64位无符号整数。   
    - 参数
        - a 双精度解析为64位无符号整数
        - b 双精度解析为64位无符号整数
    - 返回
        - 相乘的结果reinterpret_cast转换为64位无符号整数
        　　　
例子:

```c++
uint64_t a = double_div( i64_to_double(10), i64_to_double(10) );
uint64_t b = double_div( i64_to_double(5), i64_to_double(2) );
uint64_t res = double_mult( a, b );
printd(res); // Output: 2.5
```
- <h5 id="double_to_i64">double_to_i64()</h5>
    > uint64_t double_to_i64(uint64_t a )     
    
        将double（解释为64位无符号整数）转换为64位无符号整数。 该函数首先将输入重新解码为双精度（50位十进制数字精度），然后将其转换为双精度，然后将其重新解码为64位无符号整数。    
    - 参数
        - a 双精度解析为64位无符号整数
    - 返回
        -　64位无符号整数转换结果　
        　　
例子:

```c++
uint64_t a = double_div( i64_to_double(5), i64_to_double(2) );
uint64_t res = double_to_i64( a );
printi(res); // Output: 2
```

- <h5 id="i64_to_double">i64_to_double()</h5>
    > uint64_t i64_to_double(uint64_t a )     
    
        将double（解释为64位无符号整数）转换为64位无符号整数。 该函数首先将输入重新解码为双精度（50位十进制数字精度），然后将其转换为双精度，然后将其重新解码为64位无符号整数。    
    - 参数
        - a 双精度解析为64位无符号整数
    - 返回
        -　double类型转换结果　　　

例子:

```c++
uint64_t res = i64_to_double( 3 );
printd(res); // Output: 3
```
<h5 id="multeq_i128">multeq_i128()</h5>
  > uint64_t multeq_i128(uint128_t *  self, const uint128_t *   other )     
    
        将两个128位无符号整数相乘，并将该值赋给第一个参数。
  - 参数
    - self 指向要相乘的值的指针。它将被替换为结果
    - other 指向要相乘的值的指针。
  - 返回
    -　double类型转换结果　　　

例子:

```c++
uint128_t self(100);
uint128_t other(100);
multeq_i128(&self, &other);
printi128(self); // Output: 10000

```


## Math CPP API   

    定义常用的数学函数和帮助器类型.

<h5>Fixed Point</h5>

    定点变量的32,64,128,256位版本

<h5>Real number</h5>

    带有基本操作符的实数数据类型。 封装double类Math C API.

<h2>类</h2>
<h5>struct  	eosio::uint128</h5>
        
    封装了uint128整数并定义常见运算符重载的结构。

<h2>函数</h2>

##### void [eosio::multeq](#multeq) (uint128_t& self, const uint128_t& other)
 	封装了multeq_i128　Math C API。

##### void [eosio::diveq](#diveq) (uint128_t& self, const uint128_t& other)
 	封装了diveq_i128 Math C API。

<h5>template<typename T ></h5>
T 	[eosio::min](#min) (const T& a, const T& b)
 	
 	定义了类似std::min()函数。

<h5>template<typename T ></h5>
T 	[eosio::max](#max) (const T& a, const T& b)
 	
 	定义了类似std::max()函数。

<h2>函数文档</h2>

<h5 id="diveq">diveq()</h5>
  > void eosio::diveq	(uint128_t& self,const uint128_t& other) | inline      

    将两个128位无符号整数相除，并将该值赋给第一个参数。 如果other值为零，它将抛出异常。 封装了Math C API的diveq_i128      
  - Parameters
       - self	分子. 它的值将被结果替代。
       - other	分母。
示例:
```
uint128_t self(100);
uint128_t other(100);
diveq(self, other);
std::cout << self; // Output: 1
```

<h5 id="max">max()</h5>
  > template<typename T \>    
  > T eosio::max(const T& a,const T& b )		
        
        获取最大值。
  - 参数
      - a　比较值
      - b　比较值
  - 返回
    获取a和b中更大的一个. 如果相等返回a
示例:
```
uint128_t a(1);
uint128_t b(2);
std::cout << max(a, b); // Output: 2
```

<h5 id="min">min()</h5>
   > template<typename T >   
   > T eosio::min(const T& a, const T& b)		

    获取更小的一个值
   - 参数
     - a 比较值
     - b 比较值
   - 返回
        获取a和b中更小的一个. 如果相等返回a
示例:
```
uint128_t a(1);
uint128_t b(2);
std::cout << min(a, b); // Output: 1
```

<h5 id="multeq">multeq()</h5>
   > void eosio::multeq	(uint128_t& self, const uint128_t& other )	 <span class="mlabel">inline</span>

    将两个128位无符号整数相乘，并将该值赋给第一个参数。 这是对multeq_i128 Math C API的封装.
   - 参数
      - self	相乘的值. 它的值将被结果替代。
      - other	相乘的值.
示例:
```
uint128_t self(100);
uint128_t other(100);
multeq(self, other);
std::cout << self; // Output: 10000
```