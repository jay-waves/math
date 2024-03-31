## 基于全域哈希的MAC算法

基于全域哈希(Universal hashing)的MAC算法，简称为UMAC.
它的特点是，对于每一次求MAC的消息，其采用的哈希函数是不同的。也就是对于同一个消息，多次求MAC的结果并不相同。现在最常用的UMAC算法，就是Poly1305,
其也被称为目前处于顶尖水平的MAC算法。这种算法运算速度极快，安全性也较高。下面就介绍一下该算法。

### Poly1305

该算法接收任意长度的消息$M$，一个256比特的密钥$K$,
输出为128比特的标签。该算法要求使用的密钥每一次都不同，因此，属于全域哈希。

该算法涉及到二进制串与二进制数的转化，因此，需要涉及端序的问题。而该算法属于小端法，因此需要调整端序。

该算法分为如下几个步骤：

1.  处理密钥

2.  设置常数

3.  更新累加器

4.  输出

#### 处理密钥

对于输入的256位密钥$K$,
将其分为左右两个128比特的串$r$和$s$(这里要求$r$和$s$都需要调整端序).
然后对于$r$, 需要对其进行过滤(clamp). 要求其调整端序后，从右往左数：

-   第$4, 8, 12, 16$个字节的高4位变为0

-   第$5, 9, 13$个字节的最低2位变为0

其C程序实现如下：

```prove
    void clampR(BigInteger &r)
    {
        r &= BigInteger::bigIntegerFromHexString("0ffffffc0ffffffc
                                                  0ffffffc0fffffff");
    }

    BigInteger r = key.slice(0, 128);
    changeEndian(r);
    clampR(r);
    BigInteger s = key.slice(128, 256);
    changeEndian(s);
```

#### 设置常数

在该算法中，涉及到常数$p=2^{130}-5$. 同时，将累加器`accumulator`置为0.

其C程序实现如下：

::: prove
    BigInteger accumulator = 0;
    BigInteger p = (BigInteger(1) << 130) - 5;
:::

#### 更新累加器

将消息$M$从左向右分为16字节，也就是128比特的块（最后一部分可以小于128比特）。然后对于每个块，进行如下操作以更新累加器：

1.  将该块以小端法转化为二进制数$m$

2.  在二进制数$m$的最高位前面加'00000001',
    也就是说，如果$m$的十六进制表示为\
    $(22222222222222222222222222222222)_{16}$,
    那么加了之后的十六进制表示为\
    $(0122222222222222222222222222222222)_{16}$

3.  如果该块的长度仍然小于17字节（即对于最后一个块来说），那么只需要在其高位填充0，使其达到17字节即可

4.  将最终得到的二进制数$m$加到`accumulator`上

5.  将`accumulator`乘$r$后再模$p$

其C程序实现如下：

::: prove
    void processBlock(BigInteger &block)
    {
        const int N = block.getLength() / 8;
        changeEndian(block);
        BigInteger addByte = BigInteger(1) << block.getLength();
        block += addByte;
        if (block.getLength() < 136)
        {
            block.limitTo(136);
        }
    }

    int length = plainText.getLength();
    int N = length / 128;
    if (length % 128 != 0)
        N++;
    for (int i = 0; i < N; i++)
    {
        int upper = (i + 1) * 128;
        if (upper > length)
            upper = length;
        BigInteger block = plainText.slice(i * 128, upper);
        processBlock(block);
        accumulator += block;
        accumulator = (r * accumulator) % p;
    }
    accumulator += s;
:::

#### 输出

将`accumulator`转化成二进制串，也就是小端法调整端序后，输出其最低的128位即可。

其C程序实现如下：

::: prove
    BigInteger tag = accumulator;
    tag.limitTo(128);
    changeEndian(tag);
:::

#### 总过程

综上，Poly1305的总过程的C程序实现如下：

```prove
    BigInteger Poly1305_MAC(BigInteger plainText, BigInteger key)
    {
        BigInteger r = key.slice(0, 128);
        changeEndian(r);
        clampR(r);
        BigInteger s = key.slice(128, 256);
        changeEndian(s);
        BigInteger accumulator = 0;
        BigInteger p = (BigInteger(1) << 130) - 5;
        int length = plainText.getLength();
        int N = length / 128;
        if (length % 128 != 0)
            N++;
        for (int i = 0; i < N; i++)
        {
            int upper = (i + 1) * 128;
            if (upper > length)
                upper = length;
            BigInteger block = plainText.slice(i * 128, upper);
            processBlock(block);
            accumulator += block;
            accumulator = (r * accumulator) % p;
        }
        accumulator += s;
        BigInteger tag = accumulator;
        tag.limitTo(128);
        changeEndian(tag);
        return tag;
    }
```