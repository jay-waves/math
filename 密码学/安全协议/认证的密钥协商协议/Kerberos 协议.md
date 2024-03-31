 Kerberos

Kerberos是现在使用最广泛的一种基于对称密钥的安全通信协议。Kerberos是希腊神话中冥王哈迪斯的地狱三头犬，是MIT最初为了保护雅典娜工程提供的网络服务器而发明的。其起名为地狱三头犬也表明了这种协议十分安全。该协议现在广泛的应用于Windows,
macOS等处。
Kerberos安全通信的完整过程，是由客户向KDC发出安全通信请求，然后获得会话密钥后与特定服务器通信的过程。也就是说，先前我们提到的两个人之间的通信，在这里可以理解成一个用户$C$和一个服务器$S$之间的通信。同时，KDC中也有两个服务器：认证服务器AS和票据授权服务器TGS.
而之前提到的主密钥，在这里，则是用户$C$与TGS之间通信的密钥。

Kerberos的完整过程包括几个部分：

-   客户认证

    该部分的作用主要在于使用户$C$拥有与TGS之间通信的主密钥$K_{C, \mathrm{TGS}}$,
    在Kerberos中与之相关的一个概念是票据授权票据TGT. 其步骤为：

    1.  用户向认证服务器发送如下信息：
        $$C\to\mathrm{AS}: \ID_C, \ID_{\mathrm{TGS}}, \mathrm{IPs}, \mathrm{Lifetime}$$  
        
        其中$\ID_C, \ID_{\mathrm{TGS}}$分别是$C$与TGS的身份识别号，$\mathrm{IPs}$是$C$的IP地址范围。而Lifetime则是其申请的TGT能持续的时间。

    2.  认证服务器向用户发送如下信息：
    $$\mathbb{AS}\to C: \brace{\ID_{\mathrm{TGS}}, T_1, \mathbb{Lifetime}, K_{C, \mathbb{TGS}}}_{K_{\mathbb{User}}},$$
    $$\brace{\ID_C, \ID_{\mathbb{TGS}}, \mathbb{IPs}, T_1, \mathbb{Lifetime}, K_{C, \mathbb{TGS}}}_{K_{\mathbb{TGS}}}$$
        其中$T_1$为时间戳，$K_{\mathrm{User}}$是AS与$C$共有的一个密钥，$K_{TGS}$是AS与TGS共有的一个密钥。

        同时，$\ID_C, \ID_{\mathrm{TGS}}, \mathrm{IPs}, T_1, \mathrm{Lifetime}, K_{C, \mathrm{TGS}}$被称作票据授权票据TGT.

    3.  $C$可以利用自己拥有的$K_{\mathrm{User}}$解密获得消息的第一部分，从而获得与TGS之间通信的主密钥$K_{C, \mathrm{TGS}}$.

-   服务授权

    该部分的作用主要是使用户和TGS之间进行通信从而获得与目的服务器$S$之间通信的会话密钥$K_{C, S}$。其步骤为：

    1.  用户向票据授权服务器发送如下信息：
        $$C\to\mathrm{TGS}: \ID_{S}, \mathrm{Lifetime}, \brace{\ID_C, T_2}_{K_{C, \mathrm{TGS}}}, \brace{\mathrm{TGT}}_{K_{\mathrm{TGS}}}$$
        其中$\brace{\mathrm{TGT}}_{K_{\mathrm{TGS}}}$是用户从AS获得信息的第二部分。

    2.  票据授权服务器用$K_{\mathrm{TGT}}$解密最后一个部分，得到TGT,
        从而获得$K_{C, \mathrm{TGS}}$,
        然后解密倒数第二个部分。接着检查：

        -   TGT没有过期。

            即票据授权服务器获得信息的时间与TGT中时间戳$T_1$之间的时间差要在Lifetime允许的范围内。

        -   倒数第二个部分中的$\ID_{C}$与TGT中的$\ID_C$是一致的

        -   倒数第二个部分也没有过期。

            即票据授权服务器获得信息的时间与倒数第二个部分中的时间戳$T_2$之间的时间差要在Lifetime允许的范围内。

        -   如果IPs非空那么检查该信息的发送源IP是否在这之中。

    3.  票据授权服务器向用户发送如下信息：
        $$\mathrm{TGS}\to C: \brace{\ID_S, T_3, \mathrm{Lifetime}, K_{C, S}}_{K_{C, \mathrm{TGS}}}, \brace{\ID_C, \ID_S, \mathrm{IPs}, \mathrm{Lifetime}, K_{C, S}}_{K_{\mathrm{Server}}}$$
        其中$K_{C, S}$即为$C$与$S$通信的会话密钥，$K_{\mathrm{Server}}$是TGS与$S$共有的一个密钥。

        同时，$\ID_C, \ID_S, \mathrm{IPs}, \mathrm{Lifetime}, K_{C, S}$被称作票据Ticket.

    4.  $C$可以利用自己拥有的$K_{C, \mathrm{TGS}}$解密第一部分，从而获得$K_{C, S}$

-   服务请求

    该部分是用户与目的服务器之间建立安全信道通信。其步骤为：

    1.  用户向目的服务器发送如下信息：
        $$C\to S: \brace{\ID_C, T_4}_{K_{C, S}}, \brace{\mathrm{Ticket}}_{K_{\mathrm{Server}}}$$
        其中后半部分是$C$之前收到的信息

    2.  $S$用自己拥有的$K_{\mathrm{Server}}$密钥解密第二部分，获得$K_{C, S}$解密第一部分，然后检查：

        -   Ticket没有过期。

            即服务器获得信息的时间与时间戳$T_2$之间的时间差要在Lifetime允许的范围内。

        -   第一部分中的$\ID_C$与第二部分中的$\ID_C$是一致的

        -   第一部分也没有过期。

            即服务器获得信息的时间与时间戳$T_4$之间的时间差在Lifetime允许的范围内。

        -   如果IPs非空那么检查该信息的发送源IP是否在这之中。

由于Kerberos涉及了许多密钥，下面这张表格给出了每一个参与者获得相应密钥的步骤

         $K_{\mathrm{User}}$   $K_{\mathrm{TGS}}$   $K_{\mathrm{Server}}$   $K_{C, \mathrm{TGS}}$   $K_{C, S}$
  ----- --------------------- -------------------- ----------------------- ----------------------- ------------
    C         预先获得                                                            客户认证3         服务授权4
   AS         预先获得              预先获得                                      由其生成         
   TGS                              预先获得              预先获得                服务授权2          由其生成
    S                                                     预先获得                                  服务请求2
