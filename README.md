# C13

这个 **C Piscine 项目（C 13）** 旨在通过实现二叉树（Binary Tree）操作，帮助你掌握 **C 语言数据结构设计**、**递归与迭代算法**、**动态内存管理** 和 **函数指针应用** 的核心技能，并深化对 **指针操作**、**资源管理** 和 **代码健壮性** 的理解。以下是其核心学习目标的详细解析：

---

### **核心学习目标**
1. **二叉树数据结构实现**
- **基础操作**：通过 `btree_create_node`（创建节点）、`btree_insert_data`（插入数据）等练习，掌握二叉树的构建与维护。
- **递归逻辑**：在遍历（前序、中序、后序）和搜索操作中，理解递归调用的执行流程与终止条件。

2. **动态内存管理**
- **内存分配与释放**：在 `btree_create_node` 中使用 `malloc` 创建节点，在复杂操作中确保无内存泄漏。
- **资源安全性**：处理空指针（如 `root` 初始为 `NULL` 的情况）和越界访问，避免程序崩溃。

3. **函数指针与泛型设计**
- **比较函数解耦**：在 `btree_insert_data` 和 `btree_search_item` 中，通过 `cmpf` 实现数据无关的排序与搜索逻辑。
- **灵活回调机制**：在 `btree_apply_by_level` 中，通过函数指针实现按层级自定义操作。

4. **算法设计与优化**
- **层级遍历**：`btree_apply_by_level` 需实现广度优先搜索（BFS），可能借助队列结构管理节点层级。
- **树高计算**：`btree_level_count` 需递归计算左右子树高度，取最大值加一。

5. **边界条件与健壮性**
- **空树处理**：在插入、搜索、遍历等函数中正确处理 `root` 为 `NULL` 的场景。
- **递归深度限制**：避免栈溢出（如极端不平衡树导致递归深度过大）。

---

### **关键技术点解析**
#### 1. 二叉树遍历的递归实现
- **前序遍历（Prefix）**：
```c
	void	btree_apply_prefix(t_btree *root, void (*applyf)(void *))
	{
		if (!root)
			return ;
		applyf(root->item);        // 先处理当前节点
		btree_apply_prefix(root->left, applyf);  // 递归左子树
		btree_apply_prefix(root->right, applyf); // 递归右子树
	}
```
关键点：递归顺序为 **根→左→右**，确保函数正确终止。

- **中序遍历（Infix）**：
递归顺序为 **左→根→右**，适用于二叉搜索树的顺序输出。

#### 2. 二叉搜索树的插入与搜索
- **`btree_insert_data` 实现**：
```c
	void	btree_insert_data(t_btree **root, void *item, int (*cmpf)(void *, void *))
	{
		if (!*root)
		{
			*root = btree_create_node(item); // 创建新节点
			return ;
		}
		int cmp_result = cmpf(item, (*root)->item);
		if (cmp_result < 0)
			btree_insert_data(&(*root)->left, item, cmpf); // 插入左子树
		else
			btree_insert_data(&(*root)->right, item, cmpf); // 插入右子树
	}
```
关键点：通过双指针 `t_btree **` 修改树结构，递归比较决定插入方向。

- **`btree_search_item` 的递归搜索**：
使用中序遍历找到第一个匹配项，时间复杂度为 O(n)。

#### 3. 层级遍历与树高计算
- **`btree_level_count` 递归实现**：
```c
	int	btree_level_count(t_btree *root)
	{
		if (!root)
			return 0;
		int left = btree_level_count(root->left);
		int right = btree_level_count(root->right);
		return 1 + (left > right ? left : right); // 树高为左右子树最大高度+1
	}
```
关键点：递归计算左右子树高度，取最大值。

- **`btree_apply_by_level` 的广度优先遍历**：
需使用队列辅助实现，按层级顺序处理节点，代码复杂度较高。

---

### **工程实践与思维提升**
1. **模块化设计**
- 将二叉树结构体 `t_btree` 和函数原型集中到 `ft_btree.h`，提升代码复用性。
- 分离数据操作（如比较函数）与算法逻辑，符合高内聚低耦合原则。

2. **防御式编程**
- 检查函数参数有效性（如 `root` 是否为 `NULL`，`cmpf` 是否有效）。
- 在递归函数中确保终止条件完备，避免无限递归。

3. **测试策略**
- 为每个函数设计多组测试用例（空树、单节点树、完全二叉树、极端不平衡树）。
- 使用 `valgrind` 检测内存泄漏，尤其在涉及动态内存的练习中。

---

### **项目隐喻与深层价值**
- **“二叉树是算法思维的试金石”**：通过递归和遍历的反复练习，理解分治思想与树形结构的天然契合。
- **“Moulinette 的严苛性”**：隐喻工业环境中自动化测试的重要性，培养对代码质量的全方位把控。
- **“从插入到按层遍历的递进”**：体现从基础到高阶的算法学习路径，夯实底层能力后再挑战复杂问题。

---

### **通关要点**
1. **优先通过 Norminette**：确保代码缩进、函数长度、变量命名等符合规范。
2. **边界测试全覆盖**：例如 `btree_insert_data` 中测试插入到空树、左子树、右子树的情况。
3. **内存管理零容忍**：在涉及动态分配的函数中，确保所有路径下资源正确释放。
4. **算法效率优化**：在层级遍历中选择队列而非递归实现，避免栈溢出风险。

---

通过这一项目，你将从“语法使用者”进阶为“算法设计者”，掌握如何用 C 语言高效操作复杂数据结构，同时培养严谨的工程习惯——这是成为系统级开发或算法工程师的核心能力。
