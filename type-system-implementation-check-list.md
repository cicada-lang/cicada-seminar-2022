# 实现类型系统所需要的基础技巧

1. 熟悉 lambda 演算。

2. 会用 closure 处理 lexical scope。

3. 会用 NbE (normalization by evaluation)，
   来判断两个 lambda 表达式是否等价（beta-reduction + eta-expansion）。

   即，首先把表达式化到正规形式 normal form，
   再用 alphp-equivalence 判断 normal form 之间的等价。

4. 会用 bidi (bi-directional type checking)，
   来把逻辑中的推理规则 (inference rules) 转化为类型检查算法。

   - 首先，要知道逻辑与类型系统是等价的。
   - 其次，论文中都是写 inference rules 的。

5. 会用 elab (elaboration) 来简化表达式。

   在类型检查过程中，我们可以省略很多信息，不写在表达式里，
   因为这些信息可以从表达式的类型中得到。

   elaboration 就在于在贴近具体语法的表达式 `Exp` 之外，
   分离出来一个经过补充信息的（简化的） `Core` 语言。
