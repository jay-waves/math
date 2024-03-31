*MD5杂凑值的每一个比特是所有输入比特的函数*, 因此有很好扩散效果.   
但由于长度较短(128bit), 仍易受到[第二类生日攻击](生日攻击.md)威胁. MD5已被证明无法提供足够的抗碰撞性.  
此外, MD5速度快于SHA1

1. 填充  
MD5不限定输入数据长度上限, 如果长度大于$2^{64}$, 就只用其低64位. 低32比特字先填充, 高32比特字后填充.   
后续哈希函数出于安全性和实用性角度, 为输入设定了上限.


***

MD5哈希算法的输出为128比特的二进制串，其$b=128, v=512$.
然而，MD5算法并不安全。在2012年，MD5算法被卡耐基梅隆大学软件工程研究所宣布能被密码学方法破解，不适合将来的使用。但是，如果只是简单的摘要对比，对安全性并不高的话，MD5哈希算法也是一个比较简便的方法。

在MD5算法中，二进制串既要以比特串的形式进行异或等逻辑运算，也要以二进制数的形式进行加减等算术运算。因此，在MD5算法中涉及到了数据的存储问题。由于是在计算机上实现的，而计算机中一个数据单元是1字节，也就是8比特。因此，数据存储的问题变成了字节存储顺序的问题。比如说，以十六进制表示的二进制串$1234$究竟表示的是$1\times 16^3+2\times 16^2+3\times 16+4$还是$4\times 16^3+3\times 16^2+2\times 16+1$呢？前者被称为小端法，后者被称为大端法（但无论是小端法还是大端法，每个字节内部比特的存储顺序都是小端法）。而MD5算法中使用的是大端法。但是，现代计算机以及本算法使用的`BigInteger`类均采用小端法处理二进制串。因此，在C++程序实现的时候，每次讲二进制串转化为数字，以及数字转化为二进制串时，都要调整端序。

虽然事实上，端序并不会影响加密性，但是，作为一个通行的标准，MD5码仍然需要我们使用大端法。

MD5算法包括了四个步骤：

1.  填充比特

2.  填充长度

3.  初始化变量

4.  处理消息

其中，填充比特和填充长度就是之前讲的Merkle-Damgård结构中的填充函数，而初始化链接变量是为了之后处理消息使用的参数，处理消息则是运用单向压缩函数，并进行输出。

### 填充比特

假设输入消息的长度为$k$, 那么如果$k\bmod{512}<448$,
那么将其后先填充一个'1', 再不停填充'0'直到新串的长度模$512$等于$448$.
如果$k\bmod{512}>448$, 那么先将其后填充一个'1',
然后再不停填充0，填满一个块以后，再继续填充$448$位。

其C程序实现如下：

::: prove
    BigInteger appendPaddingBits(BigInteger plainText)
    {
        int spareLength = plainText.getLength() % 512;
        if (spareLength < 448)
        {
            int appendLength = 448 - spareLength;
            bool *appendValue = new bool[appendLength];
            *(appendValue + appendLength - 1) = true;
            int index = appendLength - 2;
            while (index > -1)
            {
                *(appendValue + index) = false;
                index--;
            }
            plainText.append(appendValue, appendLength);
        }
        else if (spareLength > 448)
        {
            int appendLength = 512 - spareLength + 448;
            bool *appendValue = new bool[appendLength];
            *(appendValue + appendLength - 1) = true;
            int index = appendLength - 2;
            while (index > -1)
            {
                *(appendValue + index) = false;
                index--;
            }
            plainText.append(appendValue, appendLength);
        }
        return plainText;
    }
:::

### 填充长度

记最初消息的长度为$k$.
将其表示成二进制串，如果其长度小于64位，则再其首部填0；如果其长度大于64位，则截取其最后64位。

这里由于涉及到数向二进制串的转化，需要调整端序。

其C程序实现如下：

```prove
    BigInteger appendLength(BigInteger appendedText, int textLength)
    {
        BigInteger appendBigInt(textLength);
        appendBigInt.limitTo(64);
        BigInteger bitSlice[8];
        for (int byte = 0; byte < 8; byte++)
        {
            bitSlice[byte] = appendBigInt.slice(8 * byte, 
                                                8 * (byte + 1));
            appendedText.append(bitSlice[7 - byte]);
        }
        return appendedText;
    }
```

### 初始化变量

MD5算法中，一共要初始化4个32位的链接变量，和64个辅助变量。

在MD5最初使用的大端法表示下，MD5使用的4个32位链接变量的值分别是：

-   `A=0x01234567`

-   `B=0x89abcdef`

-   `C=0xfedcba98`

-   `D=0x76543210`

所谓链接变量，将其链接起来，就是一个长为128比特的二进制串。在Merkle-Damgård结构结构中，这个128比特的二进制串就是作为初始值$\mathrm{IV}$.

由于这也涉及到数向二进制串的转化，因此，需要调整端序。

`T[64]`是一个辅助数组，`T[i]`的值是$4294967296\abs{\sin(i+1)}$.

其C程序实现如下：

```prove
    BigInteger A, B, C, D;
    void initializeBuffer()
    {
        A = BigInteger("0x67452301");
        B = BigInteger("0xefcdab89");
        C = BigInteger("0x98badcfe");
        D = BigInteger("0x10325476");


        for (int i = 0; i < 64; i++)
        {
            T[i] = BigInteger((unsigned int)(4294967296 * 
                                                fabs(sin(i + 1))));
            T[i].limitTo(32);
        }
    }
```

### 处理消息

在处理消息的过程中，需要使用四个辅助函数`FF`, `GG`, `HH`, `II`.
而这四个辅助函数又是由另外四个辅助函数`F`, `G`, `H`,
`I`定义的。因此，我们首先定义八个函数：

```prove
    BigInteger F(BigInteger X, BigInteger Y, BigInteger Z)
    {
        return (X & Y) | (~X & Z);
    }

    BigInteger G(BigInteger X, BigInteger Y, BigInteger Z)
    {
        return (X & Z) | (Y & ~Z);
    }

    BigInteger H(BigInteger X, BigInteger Y, BigInteger Z)
    {
        return (X ^ Y) ^ Z;
    }

    BigInteger I(BigInteger X, BigInteger Y, BigInteger Z)
    {
        return Y ^ (X | ~Z);
    }

    BigInteger X[16];

    void FF(BigInteger &A, BigInteger B, BigInteger C, BigInteger D, 
            int k, int s, int i){
        BigInteger tmp = A + F(B, C, D) + X[k] + T[i];
        tmp.limitTo(32);
        A = B + tmp.rotLeft(s);
        A.limitTo(32);
    }

    void GG(BigInteger &A, BigInteger B, BigInteger C, BigInteger D, 
            int k, int s, int i)
    {
        BigInteger tmp = A + G(B, C, D) + X[k] + T[i];
        tmp.limitTo(32);
        A = B + tmp.rotLeft(s);
        A.limitTo(32);
    }

    void HH(BigInteger &A, BigInteger B, BigInteger C, BigInteger D, 
            int k, int s, int i)
    {
        BigInteger tmp = A + H(B, C, D) + X[k] + T[i];
        tmp.limitTo(32);
        A = B + tmp.rotLeft(s);
        A.limitTo(32);
    }

    void II(BigInteger &A, BigInteger B, BigInteger C, BigInteger D, 
            int k, int s, int i)
    {
        BigInteger tmp = A + I(B, C, D) + X[k] + T[i];
        tmp.limitTo(32);
        A = B + tmp.rotLeft(s);
        A.limitTo(32);
    }
```

在上述程序中，`X[16]`是在下一环节中赋值的变量，每个元素为32比特长的二进制数。`T[64]`是一个上一环节初始化的辅助数组。

下一环节则是正式处理消息。首先，根据Merkle-Damgård结构，需要将处理好的消息分为512比特的块，然后将一个块再分为16个长度为32比特的二进制串`X[16]`。处理的过程中，由于用到了算术运算，因此，也要调整端序。处理的过程就是一个单向压缩函数，其算法就是反复调用之前的`FF`,
`GG`, `HH`, `II`四个函数。

最后，输出的时候，就是先调整端序，然后拼接并输出。

其C程序实现如下：

```prove
    BigInteger processMessage(BigInteger message)
    {
        int N = message.getLength() / 32;
        for (int i = 0; i < N / 16; i++)
        {
            for (int j = 0; j < 16; j++)
            {
                X[j] = message.slice(32 * (i * 16 + j), 
                                        32 * (i * 16 + j + 1));
                BigInteger bitSlice[4] = {X[j].slice(0, 8), 
                                            X[j].slice(8, 16), 
                                            X[j].slice(16, 24), 
                                            X[j].slice(24, 32)};
                X[j] = bitSlice[3];
                X[j].append(bitSlice[2]);
                X[j].append(bitSlice[1]);
                X[j].append(bitSlice[0]);
            }
            
            BigInteger AA = A, BB = B, CC = C, DD = D;
            FF(A, B, C, D, 0, 7, 0);
            FF(D, A, B, C, 1, 12, 1);
            FF(C, D, A, B, 2, 17, 2);
            FF(B, C, D, A, 3, 22, 3);
            FF(A, B, C, D, 4, 7, 4);
            FF(D, A, B, C, 5, 12, 5);
            FF(C, D, A, B, 6, 17, 6);
            FF(B, C, D, A, 7, 22, 7);
            FF(A, B, C, D, 8, 7, 8);
            FF(D, A, B, C, 9, 12, 9);
            FF(C, D, A, B, 10, 17, 10);
            FF(B, C, D, A, 11, 22, 11);
            FF(A, B, C, D, 12, 7, 12);
            FF(D, A, B, C, 13, 12, 13);
            FF(C, D, A, B, 14, 17, 14);
            FF(B, C, D, A, 15, 22, 15);
            
            GG(A, B, C, D, 1, 5, 16);
            GG(D, A, B, C, 6, 9, 17);
            GG(C, D, A, B, 11, 14, 18);
            GG(B, C, D, A, 0, 20, 19);
            GG(A, B, C, D, 5, 5, 20);
            GG(D, A, B, C, 10, 9 ,21);
            GG(C, D, A, B, 15, 14, 22);
            GG(B, C, D, A, 4, 20, 23);
            GG(A, B, C, D, 9, 5, 24);
            GG(D, A, B, C, 14, 9 ,25);
            GG(C, D, A, B, 3, 14, 26);
            GG(B, C, D, A, 8, 20, 27);
            GG(A, B, C, D, 13, 5, 28);
            GG(D, A, B, C, 2, 9 ,29);
            GG(C, D, A, B, 7, 14, 30);
            GG(B, C, D, A, 12, 20, 31);
            
            HH(A, B, C, D, 5, 4, 32);
            HH(D, A, B, C, 8, 11, 33);
            HH(C, D, A, B, 11, 16,34);
            HH(B, C, D, A, 14, 23, 35);
            HH(A, B, C, D, 1, 4, 36);
            HH(D, A, B, C, 4, 11, 37);
            HH(C, D, A, B, 7, 16, 38);
            HH(B, C, D, A, 10, 23, 39);
            HH(A, B, C, D, 13, 4, 40);
            HH(D, A, B, C, 0, 11, 41);
            HH(C, D, A, B, 3, 16, 42);
            HH(B, C, D, A, 6, 23, 43);
            HH(A, B, C, D, 9, 4, 44);
            HH(D, A, B, C, 12, 11, 45);
            HH(C, D, A, B, 15, 16, 46);
            HH(B, C, D, A, 2, 23, 47);
            
            II(A, B, C, D, 0, 6, 48);
            II(D, A, B, C, 7, 10, 49);
            II(C, D, A, B, 14, 15, 50);
            II(B, C, D, A, 5, 21, 51);
            II(A, B, C, D, 12, 6, 52);
            II(D, A, B, C, 3, 10, 53);
            II(C, D, A, B, 10, 15, 54);
            II(B, C, D, A, 1, 21, 55);
            II(A, B, C, D, 8, 6, 56);
            II(D, A, B, C, 15, 10, 57);
            II(C, D, A, B, 6, 15, 58);
            II(B, C, D, A, 13, 21, 59);
            II(A, B, C, D, 4, 6, 60);
            II(D, A, B, C, 11, 10, 61);
            II(C, D, A, B, 2, 15, 62);
            II(B, C, D, A, 9, 21, 63);
            
            A = A + AA;
            A.limitTo(32);
            
            B = B + BB;
            B.limitTo(32);
            
            C = C + CC;
            C.limitTo(32);
            
            D = D + DD;
            D.limitTo(32);
        }
        
        BigInteger output = A.slice(24, 32);
        output.append(A.slice(16, 24));
        output.append(A.slice(8, 16));
        output.append(A.slice(0, 8));
        output.append(B.slice(24, 32));
        output.append(B.slice(16, 24));
        output.append(B.slice(8, 16));
        output.append(B.slice(0, 8));
        output.append(C.slice(24, 32));
        output.append(C.slice(16, 24));
        output.append(C.slice(8, 16));
        output.append(C.slice(0, 8));
        output.append(D.slice(24, 32));
        output.append(D.slice(16, 24));
        output.append(D.slice(8, 16));
        output.append(D.slice(0, 8));
        return output;
    }
```

### 总过程

基于上述函数，最终总的MD5哈希算法的过程为：

::: prove
    BigInteger MD5_hash(BigInteger plainText)
    {
        BigInteger appendedText = appendPaddingBits(plainText);
        BigInteger message = appendLength(appendedText, 
                                            plainText.getLength());
        initializeBuffer();
        return processMessage(message);
    }
:::

