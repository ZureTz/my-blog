---
title: 二叉树与 Huffman 树
date: 2023-11-06 15:50:57
tags: 
	- code
	- algorithm
	- project
---

# 二叉树与 Huffman 树

## 二叉树

### 目的与要求

- 实现一个二叉树的数据结构
    - 实现拓展二叉树的先序建树
    - 在二叉树的基础上实现层序遍历
- 实现中序线索二叉树的数据结构
    - 在一个中序线索二叉树中，对于其中一个结点 `p`：
        - 求结点 `p` 的中序下的前驱
        - 求结点 `p` 的中序下的后继

### 实现二叉树的数据结构

由于二叉树是由许多结点组成的，因此，如果想要实现二叉树的数据结构，我们应该首先实现二叉树的节点的数据结构 `TreeNode`

一个节点拥有：

- 一个数据 `data` 
- 指向左孩子结点的指针 `left` 
- 指向右孩子结点的指针 `right` 

| left | data | right |
| ---- | ---- | ----- |

要构造节点的时候，我们需要一个数据 `data` ；当要删除节点的时候，我们调用的析构函数相应的也会去删除左右两个节点指向的节点。实现如下所示：

```c++
// Binary tree node
template <typename T> struct TreeNode {
  using NodePtr = TreeNode<T> *;

  T data;
  NodePtr left;
  NodePtr right;

  TreeNode<T>(T data = T(), NodePtr left = nullptr, NodePtr right = nullptr)
      : data(data), left(left), right(right) {}

  ~TreeNode<T>() {
    delete left;
    delete right;
  }
};
```

实现了节点之后，我们要实现二叉树本身的数据结构。二叉树需要一个根结点，所以我们在二叉树的数据项中，给出一个指向根结点的指针 `root` ，并且通过扩展二叉树的先序建树的方式，写一个构造函数，来构造二叉树，例如：

```c++
  /*
   *                   +--+
   *                   |30|
   *            +------------------+
   *            |                  |
   *         +--+                 ++-+
   *         |15|                 |35|
   *    +------------+         +----------+
   *    |            |       +-++       +-++
   *  +-+          +-++      |32|       |38|
   *  |4|          |20|      +--+       +--+
   *  +-+          +--+
   *         +-----+
   *         |18|
   *         +---+
   *             |
   *             +----+
   *               |19|
   *               +--+
   */ 
  BinaryTree<int> tree{
      30,
      15,
      4,
      Null,
      Null,
      20,
      18,
      Null,
      19,
      Null,
      Null,
      Null,
      35,
      32,
      Null,
      Null,
      38,
      Null,
      Null,
  };
```

要实现这样子的初始化方式，我们需要调用 `std::initializer_list` 来获取初始化列表内的信息，再通过非递归的方式进行构造。既然要非递归，那么我们要通过栈的方式，来模拟递归的过程，这里，我们使用 `initializerStack`。

1. 当遇到的元素不为 `Null` 时，使用该元素构造节点，然后压入 `initializerStack` 

2. 当遇到的元素为 `Null` 时，弹出 `initializerStack` 的栈顶元素，直到遇到不为 `Null` 的元素，或者栈为空。

所以，可以得到通过先序建树的方式实现构造树的构造函数。对于析构函数，只需要删除 `root` 指向的结点即可，节点的删除会因为析构函数而递归调用

```c++
template <typename T> struct BinaryTree {
  using NodePtr = TreeNode<T> *;

  NodePtr root;

  BinaryTree<T>(const std::initializer_list<T> &list) {
    if (list.size() == 0) {
      root = nullptr;
      return;
    }
    root = new TreeNode<T>(*list.begin());

    std::stack<TreeNode<T> *> initializerStack;
    initializerStack.push(root);
    TreeNode<T> *node = root;

    auto it = list.begin() + 1;
    while (it < list.end()) {
      while (it < list.end() && *it != T()) {
        node->left = new TreeNode<T>(*it);
        node = node->left;
        initializerStack.push(node);
        ++it;
      }
      // *it == null
      // or: the iterator comes to the end of the list
      while (it < list.end() && !initializerStack.empty() && *it == T()) {
        node = initializerStack.top();
        initializerStack.pop();
        ++it;
      }
      if (it >= list.end()) {
        break;
      }
      if (*it == T() && initializerStack.empty()) {
        break;
      }
      node->right = new TreeNode<T>(*it);
      node = node->right;
      initializerStack.push(node);
      ++it;
    }
  }
    
  ~BinaryTree<T>() { delete root; }
};
```

至此，我们已经实现了二叉树的基本数据结构，以及二叉树的拓展先序建树

接下来，我们会在该二叉树的数据结构中内置层序遍历的算法

#### 在二叉树的基础上实现层序遍历

要实现层序遍历，需要使用到队列 `traverseQueue` ，并将 `root ` 压入 `traverseQueue` 内；直到 `traverseQueue` 为空之前，取出队尾节点，并向 `traverseQueue`  插入两个孩子节点，循环往复：

```c++
template <typename T> struct TreeNode {
  /* ...... */
    
	void layerOrderTraversal(std::ostream &os) const {
    if (root == nullptr) {
      return;
    }
    std::queue<TreeNode<T> *> traverseQueue;
    traverseQueue.push(root);
    while (!traverseQueue.empty()) {
      const TreeNode<T> *node = traverseQueue.front();
      traverseQueue.pop();
      if (node == nullptr) {
        continue;
      }
      os << node->data << ' ';
      traverseQueue.push(node->left);
      traverseQueue.push(node->right);
    }
  }
    
  /* ...... */
};
```

### 实现中序线索二叉树的数据结构

与二叉树类似，中序线索二叉树也有指向左右结点的指针 `left` 和 `right` ，以及数据项 `data`；但是不同之处在于，需要两个标签分别表示左右指针的指向状态（`Link` 或者 `Threaded`）

| enum PointerTag |
| --------------- |
| Link            |
| Threaded        |

| leftTag | left | data | right | rightTag |
| ------- | ---- | ---- | ----- | -------- |

在构造函数方面，使用相同的使用 `data` 构造的方法；而在析构的时候，由于会有 `Link` 和 `Threaded` 两种状态的两个指针指向同一个结点，因此我们只对标签为 `Link` 的指针进行删除处理。代码如下：

```c++
// Threaded binary tree
enum PointerTag { Link, Threaded };
template <typename T> struct ThreadedTreeNode {
  using NodePtr = ThreadedTreeNode<T> *;

  T data;
  PointerTag leftTag, rightTag;
  NodePtr left, right;

  ThreadedTreeNode<T>(T data) : data(data) {
    leftTag = rightTag = PointerTag::Link;
    left = right = nullptr;
  }
    
  ~ThreadedTreeNode<T>() {
    if (leftTag == PointerTag::Link) {
      delete left;
    }
    if (rightTag == PointerTag::Link) {
      delete right;
    }
  }
};
```

实现了线索二叉树的结点后，我们实现线索二叉树的树；同样的，其包含指向根节点的指针 `root` 。对于如何构造线索二叉树，我们只有在知道了原来的二叉树之后，才能在原来的二叉树基础上构造线索二叉树。所以，其构造函数的参数就是对原二叉树的引用，在原二叉树的基础上，用数据拷贝出一个线索二叉树，并实行线索化。这里使用递归函数 `copyAndPush`，在实现拷贝数据的同时，记录中序遍历的数据。最后，按照中序遍历数据的从头到尾，依次对每个含有 `nullptr` 的结点（即度小于2的结点进行线索化），具体代码如下所示：

```c++
template <typename T> struct ThreadedBinaryTree {
  using NodePtr = ThreadedTreeNode<T> *;

  NodePtr root;

  ThreadedBinaryTree<T>(const BinaryTree<T> &sourceTree) {
    if (sourceTree.root == nullptr) {
      root = nullptr;
      return;
    }

    std::vector<NodePtr> inOrderResults;
    std::function<NodePtr(TreeNode<T> *)> copyAndPush =
        [&inOrderResults, &copyAndPush](TreeNode<T> *source) -> NodePtr {
      if (source == nullptr) {
        return nullptr;
      }
      NodePtr result = new ThreadedTreeNode<T>(source->data);
      result->left = copyAndPush(source->left);
      inOrderResults.push_back(result);
      result->right = copyAndPush(source->right);
      return result;
    };

    this->root = copyAndPush(sourceTree.root);

    if (inOrderResults.size() < 2) {
      this->root->leftTag = this->root->rightTag = PointerTag::Threaded;
      return;
    }

    NodePtr frontNode = inOrderResults.front();
    frontNode->leftTag = PointerTag::Threaded;
    if (frontNode->right == nullptr) {
      frontNode->rightTag = PointerTag::Threaded;
      frontNode->right = *(inOrderResults.begin() + 1);
    }
    for (auto it = inOrderResults.begin() + 1; it != inOrderResults.end() - 1;
         ++it) {
      NodePtr currentNode = (*it);
      if (currentNode->left == nullptr) {
        currentNode->leftTag = PointerTag::Threaded;
        currentNode->left = *(it - 1);
      }
      if (currentNode->right == nullptr) {
        currentNode->rightTag = PointerTag::Threaded;
        currentNode->right = *(it + 1);
      }
    }
    NodePtr backNode = inOrderResults.back();
    backNode->rightTag = PointerTag::Threaded;
    if (backNode->left == nullptr) {
      backNode->leftTag = PointerTag::Threaded;
      backNode->left = *(inOrderResults.end() - 2);
    }
  }

  ~ThreadedBinaryTree<T>() { delete root; }
};
```

#### 求结点 `p` 的前驱与后继

其实对于一个线索二叉树结点 `p` 来说，其前驱与后继是有规律的：

- 当 `leftTag`/`rightTag` 为 `PointerTag::Threaded` 的时候，该节点的前驱/后继就是 `left`/`right`指向的节点
- 当 `leftTag` 为 `PointerTag::Link` 的时候，该节点的前驱结点为该节点的左子树的最右侧节点
- 当 `rightTag` 为 `PointerTag::Link` 的时候，该节点的后继结点为该节点的右子树的最左侧节点

因此，可得对于一个节点求前驱节点的 `priorNode()` 和 `nextNode()` 函数：

```c++
template <typename T> struct ThreadedTreeNode {
  using NodePtr = ThreadedTreeNode<T> *;
    
  /* ...... */

  NodePtr priorNode() const {
    if (this->leftTag == PointerTag::Link) {
      NodePtr node = this->left;
      while (node->rightTag == PointerTag::Link) {
        node = node->right;
      }
      return node;
    }
    // else: the prior node is threaded
    return this->left;
  }

  NodePtr nextNode() const {
    if (this->rightTag == PointerTag::Link) {
      NodePtr node = this->right;
      while (node->leftTag == PointerTag::Link) {
        node = node->left;
      }
      return node;
    }
    // else: the next node is threaded
    return this->right;
  }

  /* ...... */
};
```

### 完整实现

至此，已经完成二叉树和线索二叉树的所有实现，下面给出完整代码：

```c++
// binary_tree.h
#ifndef BINARY_TREE_H
#define BINARY_TREE_H

#include <functional>
#include <initializer_list>
#include <iostream>
#include <queue>
#include <stack>
#include <utility>
#include <vector>

// Binary tree node
template <typename T> struct TreeNode {
  using NodePtr = TreeNode<T> *;

  T data;
  NodePtr left;
  NodePtr right;

  TreeNode<T>(T data = T(), NodePtr left = nullptr, NodePtr right = nullptr)
      : data(data), left(left), right(right) {}

  ~TreeNode<T>() {
    delete left;
    delete right;
  }
};

// Binary tree (contains a root pointer)
template <typename T> struct BinaryTree {
  using NodePtr = TreeNode<T> *;

  NodePtr root;

  BinaryTree<T>(const std::initializer_list<T> &list) {
    if (list.size() == 0) {
      root = nullptr;
      return;
    }
    root = new TreeNode<T>(*list.begin());

    std::stack<TreeNode<T> *> initializerStack;
    initializerStack.push(root);
    TreeNode<T> *node = root;

    auto it = list.begin() + 1;
    while (it < list.end()) {
      while (it < list.end() && *it != T()) {
        node->left = new TreeNode<T>(*it);
        node = node->left;
        initializerStack.push(node);
        ++it;
      }
      // *it == null
      // or: the iterator comes to the end of the list
      while (it < list.end() && !initializerStack.empty() && *it == T()) {
        node = initializerStack.top();
        initializerStack.pop();
        ++it;
      }
      if (it >= list.end()) {
        break;
      }
      if (*it == T() && initializerStack.empty()) {
        break;
      }
      node->right = new TreeNode<T>(*it);
      node = node->right;
      initializerStack.push(node);
      ++it;
    }
  }

  void layerOrderTraversal(std::ostream &os) const {
    if (root == nullptr) {
      return;
    }
    std::queue<TreeNode<T> *> traverseQueue;
    traverseQueue.push(root);
    while (!traverseQueue.empty()) {
      const TreeNode<T> *node = traverseQueue.front();
      traverseQueue.pop();
      if (node == nullptr) {
        continue;
      }
      os << node->data << ' ';
      traverseQueue.push(node->left);
      traverseQueue.push(node->right);
    }
  }

  ~BinaryTree<T>() { delete root; }
};

// Threaded binary tree
enum PointerTag { Link, Threaded };
template <typename T> struct ThreadedTreeNode {
  using NodePtr = ThreadedTreeNode<T> *;

  T data;
  PointerTag leftTag, rightTag;
  NodePtr left, right;

  ThreadedTreeNode<T>(T data) : data(data) {
    leftTag = rightTag = PointerTag::Link;
    left = right = nullptr;
  }

  NodePtr priorNode() const {
    if (this->leftTag == PointerTag::Link) {
      NodePtr node = this->left;
      while (node->rightTag == PointerTag::Link) {
        node = node->right;
      }
      return node;
    }
    // else: the prior node is threaded
    return this->left;
  }

  NodePtr nextNode() const {
    if (this->rightTag == PointerTag::Link) {
      NodePtr node = this->right;
      while (node->leftTag == PointerTag::Link) {
        node = node->left;
      }
      return node;
    }
    // else: the next node is threaded
    return this->right;
  }

  ~ThreadedTreeNode<T>() {
    if (leftTag == PointerTag::Link) {
      delete left;
    }
    if (rightTag == PointerTag::Link) {
      delete right;
    }
  }
};

template <typename T> struct ThreadedBinaryTree {
  using NodePtr = ThreadedTreeNode<T> *;

  NodePtr root;

  ThreadedBinaryTree<T>(const BinaryTree<T> &sourceTree) {
    if (sourceTree.root == nullptr) {
      root = nullptr;
      return;
    }

    std::vector<NodePtr> inOrderResults;
    std::function<NodePtr(TreeNode<T> *)> copyAndPush =
        [&inOrderResults, &copyAndPush](TreeNode<T> *source) -> NodePtr {
      if (source == nullptr) {
        return nullptr;
      }
      NodePtr result = new ThreadedTreeNode<T>(source->data);
      result->left = copyAndPush(source->left);
      inOrderResults.push_back(result);
      result->right = copyAndPush(source->right);
      return result;
    };

    this->root = copyAndPush(sourceTree.root);

    if (inOrderResults.size() < 2) {
      this->root->leftTag = this->root->rightTag = PointerTag::Threaded;
      return;
    }

    NodePtr frontNode = inOrderResults.front();
    frontNode->leftTag = PointerTag::Threaded;
    if (frontNode->right == nullptr) {
      frontNode->rightTag = PointerTag::Threaded;
      frontNode->right = *(inOrderResults.begin() + 1);
    }
    for (auto it = inOrderResults.begin() + 1; it != inOrderResults.end() - 1;
         ++it) {
      NodePtr currentNode = (*it);
      if (currentNode->left == nullptr) {
        currentNode->leftTag = PointerTag::Threaded;
        currentNode->left = *(it - 1);
      }
      if (currentNode->right == nullptr) {
        currentNode->rightTag = PointerTag::Threaded;
        currentNode->right = *(it + 1);
      }
    }
    NodePtr backNode = inOrderResults.back();
    backNode->rightTag = PointerTag::Threaded;
    if (backNode->left == nullptr) {
      backNode->leftTag = PointerTag::Threaded;
      backNode->left = *(inOrderResults.end() - 2);
    }
  }

  void preOrderTraversal(std::ostream &os) const {
    if (root == nullptr) {
      return;
    }

    NodePtr node = root;
    while (node) {
      os << node->data << ' ';
      if (node->leftTag == PointerTag::Link) {
        node = node->left;
        continue;
      }
      if (node->rightTag == PointerTag::Link) {
        node = node->right;
        continue;
      }
      while (node && node->rightTag == PointerTag::Threaded) {
        node = node->right;
      }
      if (node) {
        node = node->right;
      }
    }
  }

  ~ThreadedBinaryTree<T>() { delete root; }
};

#endif
```

### 测试结果

下面给出拓展二叉树的建立、二叉树的层序遍历、求结点 `p` 的前驱和后继的测试数据和测试结果

```c++
// binaryTreeTest.cc
#include <iostream>
#include "binary_tree.h"
using std::cout;

template <typename T> struct Null {
  inline T operator()(void) const { return T(); }
};

int main(int argc, char const *argv[]) {
  std::ios::sync_with_stdio(false);
  /*
   *                  +--+
   *                  |30|
   *            +------------------+
   *            |                  |
   *         +--+                 ++-+
   *         |15|                 |35|
   *    +------------+         +----------+
   *    |            |       +--+       +--+
   *  +-+          +-++      |32|       |38|
   *  |4|          |20|      +--+       +--+
   *  +-+          +--+
   *         +-----+
   *         |18|
   *         +---+
   *             |
   *             +----+
   *               |19|
   *               +--+
   */
  BinaryTree<int> tree{
      30,
      15,
      4,
      Null<int>()(),
      Null<int>()(),
      20,
      18,
      Null<int>()(),
      19,
      Null<int>()(),
      Null<int>()(),
      Null<int>()(),
      35,
      32,
      Null<int>()(),
      Null<int>()(),
      38,
      Null<int>()(),
      Null<int>()(),
  };

  tree.layerOrderTraversal(cout);
  cout.put('\n');

  ThreadedBinaryTree<int> Threaded(tree);
  ThreadedTreeNode<int> *node = Threaded.root->left->right;
  cout << (node->priorNode() ? node->priorNode()->data : 0) << ' '
       << (node->nextNode() ? node->nextNode()->data : 0) << std::endl;
  
  return 0;
}
```

```bash
30 15 35 4 20 32 38 18 19 
19 30
```

在输出结果的第一行，给出了二叉树的层序遍历；第二行，则给出了节点 `20` 的前驱结点和后继结点

## Huffman 树

### 目的与要求

> 假定用于通信的电文仅由 9 个字母 A， B， C， D， E， F， G， H， I 组成，各字母在电文中出现的频率分别为 6， 21， 3， 9， 12， 18， 38， 4， 11。试为这 9 个字母设计不等长 Huffman 编码，并给出该电文的总码数。

针对以上问题，我们可以得到以下要求：

- 实现一个 Huffman 树的数据结构
    - 实现通过一个通过形如 $[<字母_1,\ 字母出现的频率_1>,\ <字母_2,\ 字母出现的频率_2>,\ \cdots]$ 的映射表来构造 Huffman 树的构造函数
    - 实现一个直接通过字符集本身（即通过一个字符串）来构造 Huffman 树的构造函数
    - 对于字符集里面的任意一个字符，通过 Huffman 树对其进行编码，并输出编码的结果
    - 按照 bit 长度，给出原字符集经过 Huffman 树编码后，所得的总长度

### Huffman 树数据结构的实现

要实现 Huffman 树的数据结构，首先应该实现 Huffman 树的结点。每个 Huffman 树结点，都含有

- 指向左右两个孩子结点的指针 `left`, `right`
- 指向双亲结点的指针 `parent`
- 出现的权值 `frequencyCount`
- （在叶子结点才具有的）字符 `element`，为此，我们引入`C++ STL` 的 `std::optional`

| parent | left | right | frequencyCount | (optianal) element |
| ------ | ---- | ----- | -------------- | ------------------ |

具体实现如下所示：

```c++
struct HuffmanNode {
  using NodePtr = HuffmanNode *;

  NodePtr parent;
  NodePtr left, right;
  int frequencyCount;
  std::optional<char> element;

  HuffmanNode(int frequencyCount,
              std::optional<char> element = std::optional<char>())
      : parent(nullptr), left(nullptr), right(nullptr),
        frequencyCount(frequencyCount), element(element) {}
  ~HuffmanNode() {
    delete left;
    delete right;
  }
};
```

实现了 Huffman 树的结点之后，我们接着就可以实现 Huffman 树的类了

由于 Huffman 树的实现涉及编码和解码的两个过程，而如果只有一个 Huffman 树的话，实现编码会变得有些复杂。假设现在有一个字符 `'A'` ，即便有 Huffman 树，我们也不知道 `'A'` 的具体位置在哪里，为此，我们需要遍历一整个 Huffman 树，才能得到 `'A'` 的编码，此时的时间复杂度为 `O(n)` （n为结点的数量），如果一共有 `m` 个字符，总体的时间复杂度就是 `O(mn)` ，而每当遇到相同的字符的时候，都需要再遍历一遍树，这显然很慢。为此，引入一个 `unordered_map<char, string> finalMap`，实现了字符到编码的一一映射，且查找映射的时间复杂度为 `O(1)`

下面是类中具体含有的数据项：

```c++
struct HuffmanTree {
  using NodePtr = HuffmanNode *;
  std::unordered_map<char, std::string> finalMap;
  NodePtr root = nullptr;
};
```

#### 构造函数的实现

实现了数据项之后，就要实现构造函数了。这里通过两种方式实现构造函数，一种是通过映射表实现，另一种是通过一个字符串实现

##### 通过映射表实现

> 实现通过一个通过形如 $[<字母_1,\ 字母出现的频率_1>,\ <字母_2,\ 字母出现的频率_2>,\ \cdots]$ 的映射表来构造 Huffman 树的构造函数

要这样实现的话，我们需要在构造函数内提供 `initializer_list<pair<char, int>>` 作为参数，并且遍历整个 `initializer_list` 。由于在构造 Huffman 树的时候，需要比较不同结点的 `frequncyCount`，并且选取出现频率最低的两个字符， 所以需要使用 `priority_queue` 来对出现频率最低的两个字符进行选取；既然我们是要创建节点，那么最好是在读取初始化列表的时候直接进行创建，并将创建好的结点指针存储在 `priority_queue` 中；由于STL中并没有针对结点指针进行排序的排序方法，因此更需要写一个 `NodePtrComp` 结构体来指定对指针的排序：

```c++
struct HuffmanTree {
/* ...... */
private:
  struct NodePtrComp {
    bool operator()(const HuffmanNode *const node1,
                    const HuffmanNode *const node2) const {
      if (node1->frequencyCount == node2->frequencyCount) {
        return node1->element > node2->element;
      }
      return node1->frequencyCount > node2->frequencyCount;
    }
  };
/* ...... */
};
```

由于我们需要出现频率最低的两个结点指针，而优先级队列是从队尾弹出元素的，因此我们需要把出现频率最低的字符放在队尾；由于两个不同结点，其含有的不同字符，出现的频率可能是相同的，因此当出现的频率相同的时候，我们 fallback 到字符本身的比较。这里，把相对较小的字符放在队尾优先取出

定义好基本的数据结构后，我们就要开始写正式的算法了

1. 将`initializer_list` 中所有的 $<字母,\ 字母出现的频率>$ 键值对，分别创建节点，并且压入 `priority_queue` 
2. 持续从`priority_queue` 的队尾弹出两个出现频率最小的两个结点，并创建一个新的结点，将两个结点和新的结点连接，并将新的结点压入队列中；直到队列的大小为 `1`
3. 最后，将 `root` 设置为队尾中的唯一指针

这样，就完成了通过映射表实现构造 Huffman 树的方法。但是，我们还需要知道字符和最后 Huffman 编码的唯一映射；因此我们还需要 `implementTreeInMap` 的方法来将字符映射到 `finalMap` 里面

下面给出具体的代码：

```c++
struct HuffmanTree {
  using NodePtr = HuffmanNode *;
  /*.......*/
    
  HuffmanTree(const std::initializer_list<std::pair<char, int>> &list) {
    std::priority_queue<HuffmanNode *, std::vector<HuffmanNode *>, NodePtrComp>
        queue;
    for (auto it = list.begin(); it != list.end(); ++it) {
      queue.push(new HuffmanNode(it->second, it->first));
    }

    while (queue.size() > 1) {
      const NodePtr node1 = queue.top();
      queue.pop();
      const NodePtr node2 = queue.top();
      queue.pop();
      const NodePtr newNode =
          new HuffmanNode(node1->frequencyCount + node2->frequencyCount);
      newNode->left = node1, newNode->right = node2;
      node1->parent = node2->parent = newNode;
      queue.push(newNode);
    }
    root = queue.top();
    implementTreeInMap(root);
  }
    
private:
  void implementTreeInMap(const NodePtr node, std::string pathString = "") {
    // Exit:
    if (node->element.has_value()) {
      finalMap.insert({node->element.value(), std::move(pathString)});
      return;
    }
    // Non-exit: pass the expanded string as an argument to the next recursive call:
    implementTreeInMap(node->left, std::move(pathString + "0"));
    implementTreeInMap(node->right, std::move(pathString + "1"));
  }
    
  /*......*/
};
```

##### 通过字符集实现

除了通过映射表实现构造 Huffman 树外，还可以通过一个特定的字符集来构造 Huffman 树；这里与通过映射表进行构造相比的区别是：

- 需要通过字符集实现对所有出现字符的统计，具体来说，需要创建一个临时的 `map` 存放字符的统计结果
- 从临时的 `map` 中取出所有统计结果， 并将统计结果放入到 `priority_queue` 中
- 后面的步骤与通过映射表构造是相同的

下面给出具体的实现：

```c++
struct HuffmanTree {
  using NodePtr = HuffmanNode *;
  /*......*/
    
  HuffmanTree(const std::string &sourceStr) {
    auto queue = getFrequencyQueue(sourceStr);
    while (queue.size() > 1) {
      const NodePtr node1 = queue.top();
      queue.pop();
      const NodePtr node2 = queue.top();
      queue.pop();

      const NodePtr newNode =
          new HuffmanNode(node1->frequencyCount + node2->frequencyCount);
      newNode->left = node1, newNode->right = node2;
      node1->parent = node2->parent = newNode;
      queue.push(newNode);
    }
    root = queue.top();
    implementTreeInMap(root);
  }

private:
  std::priority_queue<HuffmanNode *, std::vector<HuffmanNode *>, NodePtrComp>
  getFrequencyQueue(const std::string &sourceStr) {
    std::unordered_map<char, int> tempMap;
    for (const char ch : sourceStr) {
      tempMap[ch]++;
    }

    std::priority_queue<HuffmanNode *, std::vector<HuffmanNode *>, NodePtrComp>
        result;
    for (auto it = tempMap.begin(); it != tempMap.end(); ++it) {
      result.push(new HuffmanNode(it->second, it->first));
    }
    return result;
  }
    
/*......*/
};
```

#### 给出每个字符的映射

既然在构造函数中已经创建了字符和编码的直接映射，那么直接输出字符的映射即可，下面给出具体的实现：

```c++
struct HuffmanTree {
  using NodePtr = HuffmanNode *;
  /* ...... */
    
  void printFinalMap(std::ostream &os) const {
    for (auto it = finalMap.begin(); it != finalMap.end(); ++it) {
      os << '\'' << it->first << '\'' << " is aliased to: " << std::setw(15)
         << std::setfill(' ') << it->second
         << ", whose frequency is: " << std::setw(9) << std::setfill(' ')
         << getFrequency(it->first) << std::endl;
    }
  }
    
  /* ...... */
};
```

#### 给出编码数据的总长度

如果需要给出编码数据的总长度，那么需要知道每个字符的编码长度以及其出现的频率；编码长度可以通过字符在 `finalMap` 与编码的映射直接得到 ；而字符和其出现的频率的对应实际上储存在 Huffman 树中；因此可以通过一个字符的编码，递归寻找字符的出现频率

知道了单个字符的编码长度和出现频率后，需要将编码长度和出现频率相乘，并求和；最后可以得到经过 Huffman 编码后，数据以 bit 表示的总长度：

```c++
struct HuffmanTree {
  using NodePtr = HuffmanNode *;
  /* ...... */
  int fullLength() const {
    int result = 0;
    for (auto it = finalMap.begin(); it != finalMap.end(); ++it) {
      result += it->second.size() * getFrequency(it->first);
    }
    return result;
  }

  int getFrequency(const char ch) const {
    if (finalMap.find(ch) == finalMap.end()) {
      return 0;
    }
    return getFrequencyByCode(root, finalMap.find(ch)->second);
  }

private:
  int getFrequencyByCode(NodePtr node, std::string code) const {
    if (code.empty()) {
      return node->frequencyCount;
    }
    const char front = code.front();
    code.erase(0, 1);
    switch (front) {
    case '0':
      return getFrequencyByCode(node->left, std::move(code));
    case '1':
      return getFrequencyByCode(node->right, std::move(code));
    default:
      return 0;
    }
  }
  /* ...... */
};
```

### 完整实现

至此，有关 Huffman 树的需求已经完整实现。下面给出 `huffman_tree.h` 的完整代码：

```c++
// huffman_tree.h
#ifndef HUFFMAN_TREE_H
#define HUFFMAN_TREE_H

#include <deque>
#include <initializer_list>
#include <iomanip>
#include <optional>
#include <ostream>
#include <queue>
#include <string>
#include <unordered_map>
#include <utility>
#include <vector>

struct HuffmanNode {
  using NodePtr = HuffmanNode *;

  NodePtr parent;
  NodePtr left, right;
  int frequencyCount;
  std::optional<char> element;

  HuffmanNode(int frequencyCount,
              std::optional<char> element = std::optional<char>())
      : parent(nullptr), left(nullptr), right(nullptr),
        frequencyCount(frequencyCount), element(element) {}
  ~HuffmanNode() {
    delete left;
    delete right;
  }
};

struct HuffmanTree {
  using NodePtr = HuffmanNode *;

  std::unordered_map<char, std::string> finalMap;
  NodePtr root = nullptr;

  HuffmanTree(const std::initializer_list<std::pair<char, int>> &list) {
    std::priority_queue<HuffmanNode *, std::vector<HuffmanNode *>, NodePtrComp>
        queue;
    for (auto it = list.begin(); it != list.end(); ++it) {
      queue.push(new HuffmanNode(it->second, it->first));
    }

    while (queue.size() > 1) {
      const NodePtr node1 = queue.top();
      queue.pop();
      const NodePtr node2 = queue.top();
      queue.pop();
      const NodePtr newNode =
          new HuffmanNode(node1->frequencyCount + node2->frequencyCount);
      newNode->left = node1, newNode->right = node2;
      node1->parent = node2->parent = newNode;
      queue.push(newNode);
    }
    root = queue.top();
    implementTreeInMap(root);
  }

  HuffmanTree(const std::string &sourceStr) {
    auto queue = getFrequencyQueue(sourceStr);
    while (queue.size() > 1) {
      const NodePtr node1 = queue.top();
      queue.pop();
      const NodePtr node2 = queue.top();
      queue.pop();

      const NodePtr newNode =
          new HuffmanNode(node1->frequencyCount + node2->frequencyCount);
      newNode->left = node1, newNode->right = node2;
      node1->parent = node2->parent = newNode;
      queue.push(newNode);
    }
    root = queue.top();
    implementTreeInMap(root);
  }

  void printFinalMap(std::ostream &os) const {
    for (auto it = finalMap.begin(); it != finalMap.end(); ++it) {
      os << '\'' << it->first << '\'' << " is aliased to: " << std::setw(15)
         << std::setfill(' ') << it->second
         << ", whose frequency is: " << std::setw(9) << std::setfill(' ')
         << getFrequency(it->first) << std::endl;
    }
  }

  int fullLength() const {
    int result = 0;
    for (auto it = finalMap.begin(); it != finalMap.end(); ++it) {
      result += it->second.size() * getFrequency(it->first);
    }
    return result;
  }

  int getFrequency(const char ch) const {
    if (finalMap.find(ch) == finalMap.end()) {
      return 0;
    }
    return getFrequencyByCode(root, finalMap.find(ch)->second);
  }
  ~HuffmanTree() { delete root; }

private:
  struct NodePtrComp {
    bool operator()(const HuffmanNode *const node1,
                    const HuffmanNode *const node2) const {
      if (node1->frequencyCount == node2->frequencyCount) {
        return node1->element > node2->element;
      }
      return node1->frequencyCount > node2->frequencyCount;
    }
  };

  std::priority_queue<HuffmanNode *, std::vector<HuffmanNode *>, NodePtrComp>
  getFrequencyQueue(const std::string &sourceStr) {
    std::unordered_map<char, int> tempMap;
    for (const char ch : sourceStr) {
      tempMap[ch]++;
    }

    std::priority_queue<HuffmanNode *, std::vector<HuffmanNode *>, NodePtrComp>
        result;
    for (auto it = tempMap.begin(); it != tempMap.end(); ++it) {
      result.push(new HuffmanNode(it->second, it->first));
    }
    return result;
  }

  void implementTreeInMap(const NodePtr node, std::string pathString = "") {
    // Exit:
    if (node->element.has_value()) {
      finalMap.insert({node->element.value(), std::move(pathString)});
      return;
    }
    // Non-exit: pass the expanded string as an argument to the next recursive call:
    implementTreeInMap(node->left, std::move(pathString + "0"));
    implementTreeInMap(node->right, std::move(pathString + "1"));
  }

  int getFrequencyByCode(NodePtr node, std::string code) const {
    if (code.empty()) {
      return node->frequencyCount;
    }
    const char front = code.front();
    code.erase(0, 1);
    switch (front) {
    case '0':
      return getFrequencyByCode(node->left, std::move(code));
    case '1':
      return getFrequencyByCode(node->right, std::move(code));
    default:
      return 0;
    }
  }
};

#endif
```

### 测试结果

下面给出关于 Huffman 树的建立、通过字符集和映射表编码的测试的数据：

```c++
// huffmanTreeTest.cc
#include "huffman_tree.h"
#include <iomanip>
#include <iostream>
#include <optional>
#include <utility>
#include <vector>
using std::cout;

int main(int argc, char const *argv[]) {
  cout << "--------------------------------------------------------------------"
          "------------"
       << std::endl;

  HuffmanTree tree{
      {'A', 6},  {'B', 21}, {'C', 3}, {'D', 9},  {'E', 12},
      {'F', 18}, {'G', 38}, {'H', 4}, {'I', 11},
  };

  tree.printFinalMap(cout);
  cout << "Full length of the string in bit: " << tree.fullLength()
       << std::endl;

  cout << "--------------------------------------------------------------------"
          "------------"
       << std::endl;

  HuffmanTree tree2(
      "The original justification for adding SIMD instructions to existing "
      "architectures was that many microprocessors were connected to graphics "
      "displays in PCs and workstations, so an increasing fraction of "
      "processing time was used for graphics. As Moore's Law increased the "
      "number of transistors available to microprocessors, it therefore made "
      "sense to improve graphics processing.A major driving force for "
      "improving graphics processing was the computer game industry, both on "
      "PCs and in dedicated game consoles such as the Sony PlayStation. The "
      "rapidly growing game market encouraged many companies to make "
      "increasing investments in developing faster graphics hardware, and this "
      "positive feedback loop led graphics processing to improve at a quicker "
      "rate than general-purpose processing in mainstream "
      "microprocessors.Given that the graphics and game community had "
      "different goals than the microprocessor development community, it "
      "evolved its own style of processing and terminology. As the graphics "
      "processors increased in power, they earned the name Graphics Processing "
      "Units or GPUs to distinguish themselves from CPUs.For a few hundred "
      "dollars, anyone can buy a GPU today with hundreds of parallel "
      "floating-point units, which makes high-performance computing more "
      "accessible. The interest in GPU computing blossomed when this potential "
      "was combined with a programming language that made GPUs easier to "
      "program. Hence, many programmers of scientific and multimedia "
      "applications today are pondering whether to use GPUs or CPUs.(This "
      "section concentrates on using GPUs for computing. To see how GPU "
      "computing combines with the traditional role of graphics acceleration, "
      "seeAppendix B.)Here are some of the key characteristics as to how GPUs "
      "vary from CPUs:n GPUs are accelerators that supplement a CPU, so they "
      "do not need to be able to perform all the tasks of a CPU. This role "
      "allows them to dedicate all their resources to graphics. It's fine for "
      "GPUs to perform some tasks poorly or not at all, given that in a system "
      "with both a CPU and a GPU, the CPU can do them if needed.n The GPU "
      "problem sizes are typically hundreds of megabytes to gigabytes, but not "
      "hundreds of gigabytes to terabytes.These differences led to different "
      "styles of architecture:n Perhaps the biggest difference is that GPUs do "
      "not rely on multilevel caches to overcome the long latency to memory, "
      "as do CPUs. Instead, GPUs rely on hardware multithreading (Section 6.4) "
      "to hide the latency to memory. That is, between the time of a memory "
      "request and the time that data arrive, the GPU executes hundreds or "
      "thousands of threads that are independent of that request."
      "The GPU memory is thus oriented toward bandwidth rather than latency. "
      "There are even special graphics DRAM chips for GPUs that are wider and "
      "have higher bandwidth than DRAM chips for CPUs. In addition, GPU "
      "memories have traditionally had smaller main memories than conventional "
      "microprocessors. In 2013, GPUs typically have 4 to 6 GiB or less, while "
      "CPUs have 32 to 256 GiB. Finally, keep in mind that for general-purpose "
      "computation, you must include the time to transfer the data between CPU "
      "memory and GPU memory, since the GPU is a coprocessor.n Given the "
      "reliance on many threads to deliver good memory bandwidth, GPUs can "
      "accommodate many parallel processors (MIMD) as well as many threads. "
      "Hence, each GPU processor is more highly multithreaded than a typical "
      "CPU, plus they have more processors.Although GPUs were designed for a "
      "narrower set of applications, some programmers wondered if they could "
      "specify their applications in a form that would let them tap the high "
      "potential performance of GPUs. After tiring of trying to specify their "
      "problems using the graphics APIs and languages, they developed "
      "C-inspired programming languages to allow them to write programs "
      "directly for the GPUs. An example is NVIDIA's CUDA (Compute Unified "
      "Device Architecture), which enables the programmer to write C programs "
      "to execute on GPUs, albeit with some restrictions. Appendix B gives "
      "examples of CUDA code. (OpenCL is a multi-company initiative to develop "
      "a portable programming language that provides many of the benefits of "
      "CUDA.)NVIDIA decided that the unifying theme of all these forms of "
      "parallelism is the CUDA Thread. Using this lowest level of parallelism "
      "as the programming primitive, the compiler and the hardware can gang "
      "thousands of CUDA threads together to utilize the various styles of "
      "parallelism within a GPU: multithreading, MIMD, SIMD, and "
      "instruction-level parallelism. These threads are blocked together and "
      "executed in groups of 32 at a time. A multithreaded processor inside a "
      "GPU executes these blocks of threads, and a GPU consists of 8 to 32 of "
      "these multithreaded processors.We use NVIDIA systems as our example as "
      "they are representative of GPU architectures. Specifically, we follow "
      "the terminology of the CUDA parallel programming language and use the "
      "Fermi architecture as the example.Like vector architectures, GPUs work "
      "well only with data-level parallel problems. Both styles have "
      "gather-scatter data transfers, and GPU processors have even "
      "moreregisters than do vector processors. Unlike most vector "
      "architectures, GPUs also rely on hardware multithreading within a "
      "single multithreaded SIMD processor to hide memory latency (see Section "
      "6.4).A multithreaded SIMD processor is similar to a vector processor, "
      "but the former has many parallel functional units instead of just a few "
      "that are deeply pipelined, as does the latter.As mentioned above, a GPU "
      "contains a collection of multithreaded SIMD processors; that is, a GPU "
      "is a MIMD composed of multithreaded SIMD processors. For example, "
      "NVIDIA has four implementations of the Fermi architecture at different "
      "price points with 7, 11, 14, or 15 multithreaded SIMD processors. To "
      "provide transparent scalability across models of GPUs with differing "
      "number of multithreaded SIMD processors, the Thread Block Scheduler "
      "hardware assigns blocks of threads to multithreaded SIMD processors. "
      "Figure 6.9 shows a simplified block diagram of a multithreaded SIMD "
      "processor.Dropping down one more level of detail, the machine object "
      "that the hardware creates, manages, schedules, and executes is a thread "
      "of SIMD instructions, which we will also call a SIMD thread. It is a "
      "traditional thread, but it contains exclusively SIMD instructions. "
      "These SIMD threads have their own program counters, and they run on a "
      "multithreaded SIMD processor. The SIMD Thread Scheduler includes a "
      "controller that lets it know which threads of SIMD instructions are "
      "ready to run, and then it sends them off to a dispatch unit to be run "
      "on the multithreadedSIMD processor. It is identical to a hardware "
      "thread scheduler in a traditional multithreaded processor (see Section "
      "6.4), except that it is scheduling threads of SIMD instructions. Thus, "
      "GPU hardware has two levels of hardware schedulers:1. The Thread Block "
      "Scheduler that assigns blocks of threads to multithreaded SIMD "
      "processors, and2. The SIMD Thread Scheduler within a SIMD processor, "
      "which schedules when SIMD threads should run.The SIMD instructions of "
      "these threads are 32 wide, so each thread of SIMD instructions would "
      "compute 32 of the elements of the computation. Since the thread "
      "consists of SIMD instructions, the SIMD processor must have parallel "
      "functional units to perform the operation. We call them SIMD Lanes, and "
      "they are quite similar to the Vector Lanes in Section 6.3.Elaboration: "
      "The number of lanes per SIMD processor varies across GPU generations. "
      "With Fermi, each 32-wide thread of SIMD instructions is mapped to 16 "
      "SIMD lanes, so each SIMD instruction in a thread of SIMD instructions "
      "takes two clock cycles to complete. Each thread of SIMD instructions is "
      "executed in lock step. Staying with the analogy of a SIMD processor as "
      "a vector processor, you could say that it has 16 lanes, and the vector "
      "length would be 32. This wide but shallow nature is why we use the term "
      "SIMD processor instead of vector processor, as it is more "
      "intuitive.Since by definition the threads of SIMD instructions are "
      "independent, the SIMD Thread Scheduler can pick whatever thread of SIMD "
      "instructions is ready, and need not stick with the next SIMD "
      "instruction in the sequence within a single thread. Thus, using the "
      "terminology of Section 6.4, it uses fine-grained multithreading.To hold "
      "these memory elements, a Fermi SIMD processor has an impressive 32,768 "
      "32-bit registers. Just like a vector processor, these registers are "
      "divided logically across the vector lanes or, in this case, SIMD lanes. "
      "Each SIMD thread is limited to no more than 64 registers, so you might "
      "think of a SIMD thread as having up to 64 vector registers, with each "
      "vector register having 32 elements and each element being 32 bits "
      "wide.Since Fermi has 16 SIMD lanes, each contains 2048 registers. Each "
      "CUDA thread gets one element of each of the vector registers. Note that "
      "a CUDA thread is just a vertical cut of a thread of SIMD instructions, "
      "corresponding to one element executed by one SIMD lane. Beware that "
      "CUDA threads are very different from POSIX threads; you can't make "
      "arbitrary system calls or synchronize arbitrarily in a CUDA thread."
     );

  tree2.printFinalMap(cout);
  cout << "Full length of the string in bit: " << tree2.fullLength()
       << std::endl;
  cout << "--------------------------------------------------------------------"
          "------------"
       << std::endl;

  return 0;
}
```

测试结果如下所示：

```bash
--------------------------------------------------------------------------------
'G' is aliased to:              11, whose frequency is:        38
'I' is aliased to:            1011, whose frequency is:        11
'D' is aliased to:            1010, whose frequency is:         9
'F' is aliased to:             100, whose frequency is:        18
'H' is aliased to:           01111, whose frequency is:         4
'C' is aliased to:           01110, whose frequency is:         3
'A' is aliased to:            0110, whose frequency is:         6
'E' is aliased to:             010, whose frequency is:        12
'B' is aliased to:              00, whose frequency is:        21
Full length of the string in bit: 347
--------------------------------------------------------------------------------
'n' is aliased to:           11111, whose frequency is:       405
'M' is aliased to:        11110111, whose frequency is:        56
'b' is aliased to:        11110110, whose frequency is:        53
'y' is aliased to:         1111010, whose frequency is:        97
'(' is aliased to:     11110011111, whose frequency is:         7
'5' is aliased to:   1111001111011, whose frequency is:         2
'0' is aliased to:   1111001111010, whose frequency is:         2
'z' is aliased to:    111100111100, whose frequency is:         3
'3' is aliased to:      1111001110, whose frequency is:        13
'-' is aliased to:      1111001101, whose frequency is:        12
'8' is aliased to:    111100110011, whose frequency is:         3
'J' is aliased to:  11110011001011, whose frequency is:         1
'9' is aliased to:  11110011001010, whose frequency is:         1
'X' is aliased to:   1111001100100, whose frequency is:         1
'W' is aliased to:    111100110001, whose frequency is:         3
'H' is aliased to:    111100110000, whose frequency is:         3
'G' is aliased to:        11110010, whose frequency is:        44
'w' is aliased to:         1111000, whose frequency is:        87
'h' is aliased to:           11101, whose frequency is:       352
'p' is aliased to:          111001, whose frequency is:       181
',' is aliased to:         1010111, whose frequency is:        82
'.' is aliased to:         1010110, whose frequency is:        75
'N' is aliased to:     10101011100, whose frequency is:         5
'L' is aliased to:     10101011011, whose frequency is:         5
'u' is aliased to:          111000, whose frequency is:       163
'F' is aliased to:      1010101100, whose frequency is:         9
'1' is aliased to:      1010101000, whose frequency is:         9
'v' is aliased to:         1010100, whose frequency is:        70
'4' is aliased to:      1010101001, whose frequency is:         9
'c' is aliased to:           10100, whose frequency is:       291
'g' is aliased to:          011111, whose frequency is:       138
's' is aliased to:            1001, whose frequency is:       543
'D' is aliased to:         0111101, whose frequency is:        68
'x' is aliased to:       101010101, whose frequency is:        18
'I' is aliased to:         0111100, whose frequency is:        66
'd' is aliased to:           01110, whose frequency is:       271
'S' is aliased to:         0101000, whose frequency is:        65
'q' is aliased to:     10101011111, whose frequency is:         5
'B' is aliased to:      0010111111, whose frequency is:         8
'7' is aliased to:    001011111000, whose frequency is:         2
'f' is aliased to:          010101, whose frequency is:       130
' ' is aliased to:             110, whose frequency is:      1449
'O' is aliased to:    001011111010, whose frequency is:         2
':' is aliased to:     10101011010, whose frequency is:         5
'i' is aliased to:            0011, whose frequency is:       486
'R' is aliased to:    001011111011, whose frequency is:         2
'j' is aliased to:     10101011110, whose frequency is:         5
';' is aliased to:    001011111001, whose frequency is:         2
'a' is aliased to:            1000, whose frequency is:       543
'2' is aliased to:       001011110, whose frequency is:        15
'k' is aliased to:        00101110, whose frequency is:        29
'T' is aliased to:        00101011, whose frequency is:        29
'P' is aliased to:         0010110, whose frequency is:        58
't' is aliased to:            1011, whose frequency is:       628
'E' is aliased to:     00101001111, whose frequency is:         4
'l' is aliased to:           01011, whose frequency is:       269
'U' is aliased to:         0101001, whose frequency is:        65
'V' is aliased to:     10101011101, whose frequency is:         5
''' is aliased to:     00101001110, whose frequency is:         4
'r' is aliased to:            0110, whose frequency is:       535
'C' is aliased to:        00101000, whose frequency is:        28
'o' is aliased to:            0100, whose frequency is:       527
'A' is aliased to:        00101010, whose frequency is:        29
'e' is aliased to:             000, whose frequency is:       869
'6' is aliased to:       001010010, whose frequency is:        14
'm' is aliased to:           00100, whose frequency is:       212
')' is aliased to:      0010100110, whose frequency is:         7
Full length of the string in bit: 41870
--------------------------------------------------------------------------------
```
