# 集合Set

# 关系Relation

集合A和B的笛卡尔积的子集

# 语言Language

- 字母表alphabet：一个由符号构成的有限集$$\Sigma$$

- 字符串string：字母表上的有限字符序列，空字符串记为e

    字符串有连接操作concatenation

- 语言language：字符串的集合（有限或无限集）。

    其中$$\Sigma$$*表示字母表上所有字符串的集合，是$$\Sigma$$上最大的语言。

    任何其他语言都是$$\Sigma$$*的子集。

    $$\Sigma$$* 是可数无限集，但$$\Sigma$$* 的幂集，也就是全体语言的集合，是不可数的。

- 语言作为集合，存在运算操作

    Union： L1L2中的字符串，以L1中字符串为首，L2中字符串为尾。由此可定义语言的幂

    <img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221009225735425.png" alt="image-20221009225735425" style="zoom: 33%;" />

    KS一定包含空串，根据此定义可以理解用$$\Sigma$$*表示全体字符串的集合的缘由。

- 正则表达式regular expression

    某一类语言可以用简单的语言（如单字符集）的**连接、并和KS三种基本操作**有限次操作（KS本质上也是连接和并的迭代，但是是无限次迭代，所以把KS也列为基本操作，保证基本操作有限性）得到，从而达到简便描述无限语言的目的。

    正则表达式有如下性质

    <img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221009230313426.png" alt="image-20221009230313426" style="zoom:33%;" />=

    两个语言的并就是语言中的字符串可以来自任何一个语言。

    

# 有限自动机Finite Automata

- DFA确定性有限状态机

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221009230454074.png" alt="image-20221009230454074" style="zoom:50%;" />

​		有若干种状态（状态名不重要），字母表（可以表示状态转换的操作名），有初始状态和终止状态集，对于任一状态，给定该状态下		的某种可行操作（字母表表示），唯一地有下一个状态。

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221012165845159.png" alt="image-20221012165845159" style="zoom: 40%;" />

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221012170158293.png" alt="image-20221012170158293" style="zoom:40%;" />

​		configuration: Kx$$\Sigma$$*中说一个元素成为配置，即某个状态和后续一些列操作（有序字符串，可以无限）。一个配置可以生成出另一		个，生成的方式是：执行完第一个操作，状态变成下一个，操作字符串删掉第一个，剩下后续的操作。一个配置可以通过多次生成形		成多种配置。一个字符串w，如果和初始状态s形成的配置可以生成出（某个最终状态，空串）的配置，则说这个字串被状态机M所接		受。所有被接受的字符串的集合记为语言L(M)。一个字符串能被接受，通俗地讲就是从起始状态开始，按字符串顺序操作进行状态跳		转，最终能够跳转至末尾状态。



- NFA非确定性有限状态机

<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221009230638198.png" alt="image-20221009230638198" style="zoom:50%;" />

​		有若干种状态（状态名不重要），字母表（可以表示状态转换的操作名），有初始状态和终止状态集，对于任一状态，给定该状态下		的某种可行操作（字母表表示），有一个或多个=可能的下一个状态。注意DFA不同状态间不能用空串e转化，但NFA可以。

​		DFA也可以定义configuration和接受语言，只不过一种configuration可以到达不同状态。所以判定某个语言能否被接受，应该判断		从起始状态开始经过该语言是否存在一种变化可以到达终止状态。

- DFA和NFA的转化（等价性）

    DFA的优点是给定字符串能机械地判定能否被接受，缺点是状态可能过多、状态机复杂。NFA则相反。

    任何一个DFA毫无疑问都是NFA，而关键在于，能否把NFA通过某种方法转化为DFA，使得接受判定更为容易？需要根据一个NFA，得到一个DFA使两者接受的语言相同。

    <img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221013105923780.png" alt="image-20221013105923780" style="zoom:50%;" />

    答案是肯定的，NFA一定可以转化为等价的DFA，主要思想是讲状态集的幂集看作新状态集，即生成的DFA的状态是原NFA某些状态的集合。

    - <img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221013111224339.png" alt="image-20221013111224339" style="zoom: 33%;" />

    <img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221013111314912.png" alt="image-20221013111314912" style="zoom:33%;" />

    首先定义E(q)表示状态q下不需要任何输入字符就可以到达的状态集合，即状态图中用e相连的子块。

    对于新状态机DFA，状态集就是原状态集的幂集，字母表不变。初始状态取E操作，对于某个状态，只要这个状态（是NFA状态的一个集合）跟原NFA的终止状态集有相交，就是DFA的一个终止状态。

    转换函数定义如上，对于某个DFA状态集Q和字符输入a，则其下一个DFA状态集Q‘是所有可以由Q中状态经a转化到的状态取E，再取并的集合，三重累积：

    Q中的多个状态+Q中某个状态经过a得到的多种可能的下一个状态+某个下一状态的取E等价的多个状态。

    注意如果Q中的任一状态经过a都不存在下一状态，则Q，a的下一个状态是空集。

    - 证明等价性：

        首先按这种方法构造的M‘是确定性的自动机，因为转化函数已经定义清除。

        关键在于证明M和M’接受的语言完全相同。

        有重要引理：

        <img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221013125918035.png" alt="image-20221013125918035" style="zoom: 50%;" />

        即生成关系在NFA和其构造的DFA之间有这样的等价关系。如果将q设为起点s，p是某个DFA终止状态，可以看出某个字符串被NFA接受和被DFA接受是等价的。

        这个引理的证明需要用到递归，证明对长度为k的字符串w结论成立（k=0，1，2...）

        











