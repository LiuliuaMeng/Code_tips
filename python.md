# 概述

如何使用Python实现TEA算法。TEA（Tiny Encryption Algorithm）是一种简单的对称加密算法，它使用64位的块大小和128位的密钥。我将分步骤介绍如何实现该算法，并提供相应的Python代码示例。

# 算法流程

下面是实现TEA算法的步骤：

1. 密钥扩展	将128位密钥扩展为4个32位子密钥

2. 加密轮	使用扩展密钥对输入数据进行加密

3. 解密轮	使用扩展密钥对加密后的数据进行解密

4. 结果输出	输出加密或解密后的数据
   接下来，我将为每个步骤提供具体的实现和相关的代码示例。

## 密钥扩展

  密钥扩展是TEA算法的第一步，它将128位的密钥扩展为4个32位的子密钥。下面是密钥扩展的代码示例：

```python
def key_schedule(key):
    delta = 0x9e3779b9
    sum = 0
    k = [0] * 4    
    
    
    for i in range(4):
        k[i] = int.from_bytes(key[i*4:(i+1)*4], 'big')

    for _ in range(32):
        sum = (sum + delta) & 0xFFFFFFFF
        k[0] += ((k[1] << 4) + delta) ^ (k[1] + sum) ^ ((k[1] >> 5) + delta)
        k[0] &= 0xFFFFFFFF
        k[1] += ((k[0] << 4) + delta) ^ (k[0] + sum) ^ ((k[0] >> 5) + delta)
        k[1] &= 0xFFFFFFFF
        k[2] += ((k[3] << 4) + delta) ^ (k[3] + sum) ^ ((k[3] >> 5) + delta)
        k[2] &= 0xFFFFFFFF
        k[3] += ((k[2] << 4) + delta) ^ (k[2] + sum) ^ ((k[2] >> 5) + delta)
        k[3] &= 0xFFFFFFFF

    return k
```

上述代码中，我们首先将128位的密钥拆分成4个32位的子密钥。然后，我们使用TEA算法的密钥扩展规则对这4个子密钥进行循环变换，生成扩展密钥。

## 加密轮

加密轮是TEA算法的第二步，它使用扩展密钥对输入数据进行加密。下面是加密轮的代码示例：

```python
def encrypt(plain, key):
    v0, v1 = int.from_bytes(plain[:4], 'big'), int.from_bytes(plain[4:], 'big')
    k = key_schedule(key)
    delta = 0x9e3779b9
    sum = 0
    for _ in range(32):
        sum = (sum + delta) & 0xFFFFFFFF
        v0 += ((v1 << 4) + k[0]) ^ (v1 + sum) ^ ((v1 >> 5) + k[1])
        v0 &= 0xFFFFFFFF
        v1 += ((v0 << 4) + k[2]) ^ (v0 + sum) ^ ((v0 >> 5) + k[3])
        v1 &= 0xFFFFFFFF

		return v0.to_bytes(4, 'big') + v1.to_bytes(4, 'big')
```

上述代码中，我们首先将输入的64位数据拆分成两个32位的块。然后，我们使用TEA算法的加密规则对这两个块进行循环变换，生成加密后的数据。

## 解密轮

解密轮是TEA算法的第三步，它使用扩展密钥对加密后的数据进行解密。下面是解密轮的代码示例：

```python
def decrypt(v,k):
	v0=c_uint32(v[0])
	v1=c_uint32(v[1])
	delta=0x9e3779b9
	sum1=c_uint32(delta*32)
	for i in range(32):
      v1.value-=((v0.value<<4)+k[2])^(v0.value+sum1.value)^((v0.value>>5)+k[3])
      v0.value-=((v1.value<<4)+k[0])^(v1.value+sum1.value)^((v1.value>>5)+k[1])
      sum1.value-=delta
  return v0.value,v1.value
```


