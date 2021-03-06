# 3. 数据库理论

## 3.1 关系数据模型

**关系数据库** 是目前应用最广泛的数据库模型，其建立在严格的关系数学理论基础之上。

根据数据模型的三要素（结构、操作、完整性约束），关系数据模型由以下三部分组成：

- 关系数据结构
- 关系操作集合
- 关系完整性约束

### 3.1.1 关系数据结构

关系数据模型采用单一的数据结构，即 **关系** 来表示实体之间的联系。

从用户角度看，一个关系就是一个 **规范化** 的二维表。

- 规范化 指表中每列都是原子项，即没有「表中表」。

一个关系由以下三者组成：

- **关系名** 对应二维表的表名。
- **关系模式** 对应二维表的表头。
- **关系实例** 对应二维表的数据。

#### 3.1.1.1 常用术语

- **关系** (Relation) 一个关系指一张二维表。
- **元组** (Tuple) 一个元组指二维表中的一行。
- **属性** (Attribute) 一个属性指二维表中的一列，表中每列均有名称，即属性名。
- **键** (Key) 键也称码、关键字、关键码，指表中可唯一确定元组的属性或属性组合。
- **域** (Domain) 域指属性的取值范围。
- **分量** 分量指元组中的一个属性值。
- **关系模式** 关系模式是对关系「型」的描述，通常表示为：`关系名 ( 属性 1 , 属性 2 , ... , 属性 n )`，是一系列属性的集合。

> 可以参照下表将术语联想至下表中对应的常用词汇：
>
> | 关系术语 | 现实世界词汇 |
> | -------- | ------------ |
> | 关系名   | 表名         |
> | 关系模式 | 表头         |
> | 关系     | 二维表       |
> | 元组     | 记录         |
> | 属性     | 列           |
> | 属性名   | 列名         |
> | 属性值   | 列值         |

#### 3.1.1.2 关系数据结构的形式化定义

##### 域 (Domain)

域是一组具有相同数据类型的值的集合。

> 可以是：  
> 整数实数介于某个取值范围的整数指定长度的字符串集合 { '男' ，'女' } 介于某个取值范围的日期

##### 笛卡尔积 (Cartesian Product)

笛卡尔积有如下定义：

给定一组域 $D_1,D_2,\dots,D_n$ ，这些域中允许存在相同。 $D_1,D_2,\dots,D_n$ 的笛卡尔积为：

$$D_1×D_2× \dots ×D_n＝\{(d_1,d_2, \dots ,d_n)｜d_i\in D_i , i＝1,2, \dots ,n \}$$

笛卡尔积具有如下属性：

- 所有域的所有取值的一个组合。
- 不能重复。

笛卡尔积中每个元素，即一组 $(d_1,d_2,\dots,d_n)$ ，称为一个 **n 元组** (n-tuple) 或简称 **元组** 。  
元组中的每一个值 $d_i$ 称为一个 **分量** 。  
**基数** 是关系中元组的个数。

笛卡尔积可以表示为一个二维表。表中每行对应一个元组，表中的每列对应一个域。

| 商品编号 | 商品类型 |
| -------- | -------- |
| 100101   | 食品     |
| 100102   | 服装     |
| 100103   | 文具     |
| 100104   | 食品     |
| 100105   | 日用品   |

##### 关系 (Relation)

$D_1×D_2×…×D_n$ 的任一子集称为在域 $D_1,D_2,\dots,D_n$ 上的关系，表示为

$$R (D_1,D_2,\dots ,D_n) $$

其中：

- **R** 关系名
- **n** 关系的目或度 (Degree)

关系具有如下特性：

- 数据库中，关系是笛卡尔积的有限子集。
- 承上性质，关系也是一个 **二维表** ，表的任意一行对应一个元组，表的每一列来自同一域。由于域可以相同，为了加以区别，必须为每列起一个名字，称为 **属性** (Attribute) 。n 元关系有 n 个属性，属性的名字唯一。
- 关系中元组个数是关系的基数。

关系中的每个元素是关系中的元组，通常用 t 表示。当 n=1 时，称该关系为单元关系 (Unary relation) 。当 n=2 时，称该关系为二元关系 (Binary relation) 。

#### 3.1.1.3 关系的性质

- 列是同质的 (Homogeneous) ，即每列中的分量必须是同一类型的数据。
- 不同的列可以出自同一个域，但不同的属性必须赋予不同的属性名。
- 列的顺序可以任意交换。交换时，应连同属性名一起交换。
- 任意两个元组不能完全相同。
- 关系中元组的顺序可任意，即可任意交换两行的次序。
- 分量必须取原子值，即要求每个分量都是不可再分的数据项。

#### 3.1.1.4 关系模式

关系的描述称为 **关系模式** (Relation Schema) 。它可以表示为：

$$R(U,D,dom,F)$$

$R$ ：关系名。  
$U$ ：责成关系的属性名集合（属性集）。  
$D$ ：属性集 $U$ 中属性所来自的域。  
$dom$ ：属性与域之间的映像集合。  
$F$ ：属性间依赖关系的集合。

关系模式通常可以简记为 $R(U)$ 或 $R(A_1,A_2,\dots,A_n)$ 。  
$R$ 关系名，$A_1,A_2,\dots,A_n$ 属性名。  
域名及属性到域的映像则以属性的类型、长度来说明。

- 关系模式是静态的、稳定的。
- 关系是动态的、随时间不断变化的。关系是关系模式在某一时刻的状态或内容。

#### 3.1.1.5 关系数据库

在一个给定的应用领域中，所有实体及实体之间联系的关系的集合构成一个 **关系数据库** 。

关系数据库也有型和值之分。

- 关系数据库的 **型** 称为关系数据库模式，是对关系数据库的描述。
- 关系数据库的 **值** 是这些关系模式在某一时刻对应的关系的集合，通常简称为关系数据库。

在关系元组中允许出现空值 (Null) 。  
空值表示信息的空缺。空值表示未知的值或不存在值。

#### 3.1.1.6 码

##### 超码

**超码** (Super Key) 是能够唯一标识（关系中）元组的一个属性或属性集合。

**Def.** 设关系 $R(A_1,A_2,\dots,A_n)$ ，其属性为 $A_1，A_2，\dots，A_n$ ，属性集 $K$ 为 $R$ 的子集，$K=(A_i,A_j,\dots,A_k),1\leqslant i,j,\dots,k\leqslant n$ 。当且仅当满足下列条件时，$K$ 被称为候选码：

- **唯一性** 对关系 $R$ 的任两个元组，其在属性集 $K$ 上的值是不同的。

##### 候选码

**候选码** (Candidate Key) 也称候选关键字或候选键，是能够唯一标识（关系中）元组的一个属性或 **最小** 属性集合。

**Def.** 设关系 $R(A_1,A_2,\dots,A_n)$ ，其属性为 $A_1，A_2，\dots，A_n$ ，属性集 $K$ 为 $R$ 的子集，$K=(A_i,A_j,\dots,A_k),1\leqslant i,j,\dots,k\leqslant n$ 。当且仅当满足下列两个条件时，$K$ 被称为候选码：

- **唯一性** 对关系 $R$ 的任两个元组，其在属性集 $K$ 上的值是不同的。
- **最小性** 属性集 $K=(A_i,A_j,\dots,A_k)$ 是最小集，即若删除 $K$ 中的任一属性， $K$ 都将丧失唯一性。

由上性质可知，候选码是超码的子集，候选码任意子集都不构成超码。

##### 主码

若一个关系有多个候选码，则选定其中一个为 **主码** (Primary key) 。

主码的各个属性称为 **主属性** (Prime attribute) 。  
不包含在任何侯选码中的属性称为 **非主属性** (Non-key attribute) ，也称 **非码属性**。

主码一般用 <u>下划线</u> 划出。

一般，一个候选码只包含一个属性。若所有属性的组合为码，则称为全码 (All-key)。

##### 外码

如果关系 $R_1$ 的属性或属性组 $K$ 不是 $R_1$ 的主码，而是另一关系 $R_2$ 的主码，则称 $K$ 为关系 $R_1$ 的外码 (Foreign Key)

- 称关系 $R_1$ 为参照关系 (Referencing Relation) 。
- 关系 $R_2$ 为被参照关系 (Referenced Relation) 。

### 3.1.2 关系操作

#### 3.1.2.1 基本关系操作

##### 查询

**数据查询操作** 用于对关系数据进行各种检索。

- 它是一个数据库最基本的功能，通过查询，用户可以访问关系数据库中的数据。
- 查询可以在一个关系内或多个关系间进行。
- 关系查询的基本单位是元组分量，查询即定位符合条件的元组。

##### 更新

**数据更新操作** 包括插入、删除和修改三种。

- 数据插入的功能是在指定关系中插入一个或多个元组。
- 数据删除的基本单位为元组，其功能是将指定关系内的指定元组删除。
- 数据修改是在一个关系中修改指定的元组属性值。

#### 3.1.2.2 关系代数

关系代数是一种抽象的查询语言，用对关系的运算来表达查询。

关系代数表达式的三个要素：

- 运算对象：关系
- 运算结果：关系
- 运算符： 集合运算符、（专门）关系运算符、比较运算符、逻辑运算符

| 运算符类比       |    记号     | 含义       |
| ---------------- | :---------: | ---------- |
| 集合运算符       |   $\cup$    | 并         |
| 集合运算符       |   $\cap$    | 交         |
| 集合运算符       |     $-$     | 差         |
| 集合运算符       |  $\times$   | 笛卡尔积   |
| 专门的关系运算符 |  $\sigma$   | 选择       |
| 专门的关系运算符 |   $\prod$   | 投影       |
| 专门的关系运算符 |  $\bowtie$  | 连接       |
| 专门的关系运算符 |   $\div$    | 除法       |
| 比较运算符       |     $<$     | 小于       |
| 比较运算符       | $\leqslant$ | 小于或等于 |
| 比较运算符       |     $>$     | 大于       |
| 比较运算符       | $\geqslant$ | 大于或等于 |
| 比较运算符       |     $=$     | 等于       |
| 比较运算符       |    $<>$     | 不等于     |
| 逻辑运算符       |  $\wedge$   | 与         |
| 逻辑运算符       |   $\vee$    | 或         |
| 逻辑运算符       |   $\neg$    | 非         |

关系代数的运算可以分为两类：

- 传统的集合运算
- 专门的关系运算

传统的集合运算是二目运算。除笛卡尔积运算外，都要求参与运算的两个关系满足 **相容性** 条件。

**Def.** 设两个关系 $R,S$，若 $R,S$ 满足一下两个条件：

- 具有相同的度 $n$
- $R$ 中第 $i$ 个属性和 $S$ 中第 $i$ 个属性来自同一个域，则称关系 $R,S$ 满足 **相容性** 条件。

##### 并 (Union)

删去多余的相同元组后，将两个关系合并组成一个新的关系。

$R \cup S = \{t|t \in R \vee t \in S\}$

##### 交 (Difference)

选择相同元组，组成一个新的关系。

$R \cap S = \{t|t \in R \wedge t \in S\}$

##### 差 (Intersection)

删去与后者相同的元组，成为一个新的关系。

$R - S = \{t|t \in R \wedge t \notin S\}$

##### 广义笛卡尔积 (Extended Cartesian Product)

笛卡尔积运算不需要遵循相容性条件。

连接两个关系。

$R\times S = \{ t_r \frown t_s |t_r \in R \wedge t_s \in S \}$

> 关系 $R$
>
> | $A$   | $B$   |
> | ----- | ----- |
> | $a_1$ | $b_1$ |
> | $a_2$ | $b_2$ |
> | $a_3$ | $b_3$ |
>
> 关系 $S$
>
> | $A$   | $B$   |
> | ----- | ----- |
> | $a_1$ | $b_2$ |
> | $a_2$ | $b_2$ |
>
> $R\times S$
>
> | $R.A$ | $R.B$ | $S.A$ | $S.B$ |
> | ----- | ----- | ----- | ----- |
> | $a_1$ | $b_1$ | $a_1$ | $b_2$ |
> | $a_1$ | $b_1$ | $a_2$ | $b_2$ |
> | $a_2$ | $b_2$ | $a_1$ | $b_2$ |
> | $a_2$ | $b_2$ | $a_2$ | $b_2$ |
> | $a_3$ | $b_3$ | $a_1$ | $b_2$ |
> | $a_3$ | $b_3$ | $a_2$ | $b_2$ |

专门的关系运算灵活地实现了关系数据库的多样化查询操作。

专门的关系运算引入了如下几个概念：

- 设关系模式为 $R(A_1,A_2,\dots,A_n)$ ，它的一个关系为 $R$ 。$t \in R$ 表示 $t$ 是 $R$ 的一个元组；$t[A_i]$ 则表示元组 $t$ 中相应于属性 $A_i$ 的一个分量。
- 若 $A=\{A_{i1},A_{i2},\cdots,A_{ik}\}$ ，其中 $A_{i1},A_{i2},\cdots,A_{ik}$ 是 $A_1,A_2,\cdots,A_n$ 中的一部分，则 $A$ 称为 **属性列** 或 **域列** 。$t[A]=(t[A_{i1}],t[A_{i2}],\cdots,t[A_{ik}])$ 表示元组 $t$ 在属性列 $A$ 上诸分量的集合。
- $R$ 为 $n$ 目关系，$S$ 为 $m$ 目关系。$t_r \in R，t_s \in S， t_r \frown t_s$ 称为元组的 **连接** (Concatenation) 。它的前 $n$ 个分量为 $R$ 中的一个 $n$ 元组，后 $m$ 个分量为 $S$ 中的一个 $m$ 元组。
- 给定以个关系 $R(X,Z)$ ，设 $X$ 和 $Z$ 为属性组，定义当 $t[X]=x$ 时，$x$ 在 $R$ 中的 **像集** (Image Set) 为 $Z_X = \{t[Z]|t\in R,t[X]=x\}$ ，它表示 $R$ 中的属性组 $X$ 上值为 $x$ 的各元组在 $Z$ 上分量的集合。

##### 选择 (Selection)

**选择** 又称为 **限制** (Restriction) ，是单目运算。

在给定的关系 $R$ 中选取满足一定条件的若干元组，组成一个新关系。

$\sigma_F=\{t|t\in R \wedge F(t)={\color{blue} \text{true} } \}$

$\sigma $ 为选择运算符
$F$ 为选择条件，其是由运算对象（属性名，常数，简单函数）、算数比较运算符（ $ > ,\geqslant ,<,\leqslant ,=,\neq$ ）和逻辑运算符（ $\wedge ,\vee ,\neg $ ） 连接起来的逻辑表达式，结果为逻辑值 ${\color{blue} \text{true} }$ 或 ${\color{blue} \text{false} }$ 。

选择的结果为关系 $R$ 中的若干行。

##### 投影 (Projection)

**投影** 是单目运算。

在关系 $R$ 中选取若干属性列组成新的关系。

$\prod _A(R)=\{t[A]|t\in R\}$

$\prod$ 为投影运算符。  
$A$ 为 $R$ 中的属性列。

投影的结果为关系 $R$ 中的若干列。

##### 连接 (Join)

**连接** 是二目运算。

在关系 $R$ 与关系 $S$ 中，分别选取满足 $\operatorname{\theta}$ 关系的元组，将其一一连接，组成新的关系。

$R\bowtie S=\{t_R \frown t_S |t_R \in R \wedge t_S \in S \wedge t_R[X] \; \operatorname{\theta} \; t_S[Y] = {\color{blue} \text{true} } \}$

$\bowtie$ 为连接运算符。  
$\operatorname{\theta}$ 为算数比较运算符，也称 $\operatorname{\theta}$ 连接。$X \; \operatorname{\theta} \; Y$ 为连接条件。

- $\operatorname{\theta}$ 为 $=$ 时，称为等值连接。
- $\operatorname{\theta}$ 为 $>$ 时，称为小于连接。
- $\operatorname{\theta}$ 为 $<$ 时，称为大于连接。

连接亦可表示为：

$R\bowtie S=\sigma_{X \operatorname{\theta} Y}(R \times S)$

常常使用 **自然连接** (Natural Join) ，即将重复的属性去除。

> 有 $R$:
>
> |  $A$  |  $B$  | $C$  |
> | :---: | :---: | :--: |
> | $a_1$ | $b_1$ | $5$  |
> | $a_1$ | $b_2$ | $6$  |
> | $a_2$ | $b_3$ | $8$  |
> | $a_2$ | $b_4$ | $12$ |
>
> $S$:
>
> |  $B$  | $E$  |
> | :---: | :--: |
> | $b_1$ | $3$  |
> | $b_2$ | $7$  |
> | $b_3$ | $10$ |
> | $b_3$ | $2$  |
> | $b_5$ | $2$  |
>
> 等值连接：
>
> $R \bowtie_{R.B=S.B} S$:
>
> |  $A$  | $R.B$ | $C$ | $S.B$ | $E$  |
> | :---: | :---: | :-: | :---: | :--: |
> | $a_1$ | $b_1$ | $5$ | $b_1$ | $3$  |
> | $a_1$ | $b_2$ | $6$ | $b_2$ | $7$  |
> | $a_2$ | $b_3$ | $8$ | $b_3$ | $10$ |
> | $a_2$ | $b_3$ | $8$ | $b_3$ | $2$  |
>
> 小于连接：
>
> $R \bowtie_{C<E} S$:
>
> |  $A$  | $R.B$ | $C$ | $S.B$ | $E$  |
> | :---: | :---: | :-: | :---: | :--: |
> | $a_1$ | $b_1$ | $5$ | $b_2$ | $7$  |
> | $a_1$ | $b_1$ | $5$ | $b_3$ | $10$ |
> | $a_1$ | $b_2$ | $6$ | $b_2$ | $7$  |
> | $a_1$ | $b_2$ | $6$ | $b_3$ | $10$ |
> | $a_2$ | $b_3$ | $8$ | $b_3$ | $10$ |
>
> 自然连接：
>
> $R \bowtie_{R.B=S.B} S$:
>
> |  $A$  |  $B$  | $C$ | $E$  |
> | :---: | :---: | :-: | :--: |
> | $a_1$ | $b_1$ | $5$ | $3$  |
> | $a_1$ | $b_2$ | $6$ | $7$  |
> | $a_2$ | $b_3$ | $8$ | $10$ |
> | $a_2$ | $b_3$ | $8$ | $2$  |

以上不保留无关元组的方式成为内连接。也可保留未匹配元组，称为外连接。

可采用外连接、左外连接、右外连接三种策略。

> $R$ 与 $S$ 外连接（全保留）：
>
> |  $A$  |  $B$  | $C$  | $E$  |
> | :---: | :---: | :--: | :--: |
> | $a_1$ | $b_1$ | $5$  | $3$  |
> | $a_1$ | $b_2$ | $6$  | $7$  |
> | $a_2$ | $b_3$ | $8$  | $10$ |
> | $a_2$ | $b_3$ | $8$  | $2$  |
> | $a_2$ | $b_4$ | $12$ | Null |
> | Null  | $b_5$ | Null | $2$  |
>
> $R$ 与 $S$ 左外连接（保留 $R$）：
>
> |  $A$  |  $B$  | $C$  | $E$  |
> | :---: | :---: | :--: | :--: |
> | $a_1$ | $b_1$ | $5$  | $3$  |
> | $a_1$ | $b_2$ | $6$  | $7$  |
> | $a_2$ | $b_3$ | $8$  | $10$ |
> | $a_2$ | $b_3$ | $8$  | $2$  |
> | $a_2$ | $b_4$ | $12$ | Null |
>
> $R$ 与 $S$ 右外连接（全保留 $S$）：
>
> |  $A$  |  $B$  | $C$  | $E$  |
> | :---: | :---: | :--: | :--: |
> | $a_1$ | $b_1$ | $5$  | $3$  |
> | $a_1$ | $b_2$ | $6$  | $7$  |
> | $a_2$ | $b_3$ | $8$  | $10$ |
> | $a_2$ | $b_3$ | $8$  | $2$  |
> | Null  | $b_5$ | Null | $2$  |

##### 除法 (Division)

**除法** 是二目运算。

- 给定关系 $R(X,Y)$ 和 $S(Y,Z)$ ，其中 $X,Y,Z$ 为属性集合。
- $R$ 中的 $Y$ 与 $S$ 中的 $Y$ 可以有不同的属性名，但必须出自相同的域集。
- $R$ 与 $S$ 的除运算得到一个新的关系 $P(X)$ ，$P$ 是 $R$ 中满足下列条件的元组在 $X$ 属性列上的投影：
  - 元组在 $X$ 上分量值 $x$ 的像集 $Y_x$ 包含 $S$ 在 $Y$ 上投影的集合。

$R \div S = \{t_r[X] | t_r \in R \wedge \pi_Y(S) \subseteq Y_x \}$

$Y_x$：$x$ 在 $R$ 中的像集，$x = t_r[X]$

> 有 $R$:
>
> |  $A$  |  $B$  |  $C$  |
> | :---: | :---: | :---: |
> | $a_1$ | $b_1$ | $c_1$ |
> | $a_2$ | $b_2$ | $c_2$ |
> | $a_3$ | $b_3$ | $c_3$ |
> | $a_1$ | $b_2$ | $c_1$ |
>
> $S$:
>
> |  $B$  |  $C$  |
> | :---: | :---: |
> | $b_1$ | $c_1$ |
> | $b_2$ | $c_1$ |
>
> $Y=\{B\}$
>
> 关系 $R \div S$ ：
>
> |  $A$  |
> | :---: |
> | $a_1$ |
>
> 理由 在关系 $R$ 中：
>
> |  $A$  |  $B$  |  $C$  |
> | :---: | :---: | :---: |
> | $a_1$ | $b_1$ | $c_1$ |
> | $a_1$ | $b_2$ | $c_1$ |
>
> 包含 $S.B$

### 3.1.3 数据完整性

关系模型的完整性规则是对关系的某种约束条件。  
其包含三类完整性约束：

- 实体完整性
- 参照完整性
- 用户定义的完整性

实体完整性和参照完整性是关系模型必须满足的完整性约束条件，被称作是关系的两个 **不变性** ，应该由关系系统自动支持。

#### 3.1.3.1 实体完整性 (Entity Integrity)

若属性 $A$ 是基本关系 $R$ 的主属性，则属性 $A$ 不能取空值。

根据实体完整性约束，一个关系中不允许存在两类元组：

- 无主码值的元组
- 主码值相同的元组

#### 3.1.3.2 参照完整性

若属性（或属性组）$F$ 是参照关系 $R$ 的外码，它与被参照关系 $S$ 的主码 $K_S$ 相对应。则对于 $R$ 中每个元组在 $F$ 上的值必须为：

- 取空值（$F$ 为属性组时每个属性值均为空值）
- 等于 $S$ 中某个元组的主码值。

关系间的引用：在关系模型中实体及实体间的联系都是用关系来描述的，因此可能存在着关系与关系间的引用。

#### 3.1.3.3 用户定义完整性

用户定义的完整性是针对某一具体关系数据库的约束条件，反映某一具体应用所涉及的数据必须满足的语义要求。

> 如 要求 电话号码 必须为满足某完整性的纯数字。

关系模型应提供定义和检验这类完整性的机制，以便用统一的系统的方法处理它们，而不要由应用程序承担这一功能。
