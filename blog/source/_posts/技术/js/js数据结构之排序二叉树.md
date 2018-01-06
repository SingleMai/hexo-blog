---
title: javascript排序二叉树
date: 2017-09-26 14:43:03
categories: 'js'
---
想想好像是隔了有点久没写了~跑去慕课刷了一下新更新的课程。发现居然有数据结构的！！二话不说就看起来了，这是关于排序二叉树的。
<!-- more -->
其实二叉树在很久之前我就自学过，但是很快就都忘了，因为老是觉得在工作中实在是用不上这些概念。但是这次着重讲的排序二叉树确实是让我眼前一亮

## 什么是排序二叉树
讲排序二叉树得先理解什么是二叉树。
- 只有一个根节点
- 节点下只有左右两个子节点，成为左子树和右子树。
- 没有左右子树的节点成为叶子节点
- 二叉树的层级称为二叉树的高

排序二叉树是二叉树的一种特殊形态。特殊在一个地方：
- 左子树的数值必须比节点数值小
- 右字数的数值必须比节点数值大

给个例子,这里不方便画出箭头，所以箭头需要大家自己脑补一下
```
      8
    6    9
  1  7     10
```

## 作用
一开始我也很难理解这玩意能派上什么用场。但是还是半信半疑地学着。（毕竟它不像排序数组那么直观）

### 中序遍历
排序二叉树能派上什么用场重点在于中序遍历的结合使用。利用中序遍历可以将排序二叉树按照从小到大的顺序进行输出。这里讲一下中序遍历的规则，然后建议是模拟着去读一个排序二叉树，理解它的思想。
- 先遍历节点左子树，并进行输出
- 再遍历节点本身，进行输出
- 最后遍历右子树进行输出

结合这个规则，和上面的子树，这里演示一下代码流程
```
8 -> 6 -> 1 (输出) -> 6(输出) -> 7(输出) -> 8(输出) -> 9(输出) -> 10(输出)
```
要对比出这个二叉树到底多优化，其实在编写代码的时候就很明显了。由于有排序的作用在，能够避免很多无用的对比查询。

### 前序遍历
作用是按照排序二叉树的顺序依次读出，作用一般用于克隆一个二叉树。（克隆带来的效率要比重新构建快得多）
- 先读取节点本身，进行输出
- 节点的左子树，进行输出
- 节点的右子树，进行输出

### 后续遍历
作用主要运用于文件系统的遍历
- 先读取左子树，进行输出
- 再读取右子树，进行输出
- 最后读取节点本身进行输出

其实在排序上它的作用没有特别明显吧我柑橘。

## 源码
主要实现的是增删改查，大部分功能都实现了。所以大家直接看源码吧~
```
var BinaryTree = function() {
  this.root = null;

  var Node = function(key) {
    this.left = null;
    this.right = null;
    this.data = key;
  }

  var _insertNode = function(node, key) {
    if (node) {
      if(key < node.data) {
        node.left = _insertNode(node.left, key);
      } else if(key > node.data) {
        node.right = _insertNode(node.right, key);
      }
    } else {
        return new Node(key);
    }
    return node;
  }

  this.insertNode = function(key) {
    if (!this.root) {
      this.root = new Node(key);
    } else {
      _insertNode(this.root, key);
    }
  }

  var _inOrderTraversal = function(node, callback) {
    if (node.left){
      _inOrderTraversal(node.left, callback);
    }
    callback(node.data);
    if (node.right) {
      _inOrderTraversal(node.right, callback);
    }
  }
  // 中序遍历——先遍历左 中 右  可以顺序遍历出节点
  this.inOrderTraversal = function(callback) {
    _inOrderTraversal(this.root, callback);
  }

  // 前序遍历--先遍历该节点  然后左  之后 右 -- 用于复制
  var _preOrderTraversal = function(node, callback) {
    callback(node.data);
    if (node.left) {
      _preOrderTraversal(node.left, callback);
    }
    if (node.right) {
      _preOrderTraversal(node.right, callback);
    }
  }
  this.preOrderTraversal = function(callback) {
    _preOrderTraversal(this.root, callback);
  }

  // 后序遍历-- 先遍历左右节点  然后遍历中
  var _postOrderTraversal = function(node, callback) {
    if (node.left) {
      _postOrderTraversal(node.left, callback);
    }
    if (node.right) {
      _postOrderTraversal(node.right, callback);
    }
    callback(node.data);
  }
  this.postOrderTraversal = function(callback) {
    _postOrderTraversal(this.root, callback);
  }

  var _search = function(node, key) {
    var _node = null;
    if (node) {
      if (node.data === key) {
        return node;
      } else if (node.data > key){
        _node = _search(node.left, key);
      } else if (node.data < key) {
        _node = _search(node.right, key);
      }
    }
    return _node;
  }
  // 查找指定结点
  this.search = function(key) {
    return _search(this.root, key);
  }

  // 返回最小结点
  var _getMinNode = function(node) {
    var _node = null;
    if (node.left) {
      _node = _getMinNode(node.left);
    } else {
      _node = node;
    }
    return _node;

  }
  this.getMinNode = function(node) {
    if (node) {
      return _getMinNode(node);
    }
    return _getMinNode(this.root);
  }

  // 删除节点
  // - 删除叶子节点
  // - 删除只有左右一个子树的中间节点
  // - 删除有两个子树的中间节点
  var _deleteNode = function(node, key) {
    // 叶子节点
    var _node = null;
    if (node) {
      if (node.data === key) {
        if (node.left === null && node.right === null) {
          return null;
        } else if (node.right && node.left === null) {
          _node = node.right;
          node = null;
          return _node;
        } else if (node.left && node.right === null) {
          _node = node.left;
          node = null;
          return _node;
        } else {
          _node = _getMinNode(node.right);
          node.data = _node.data;
          _deleteNode(node.right, _node.data);
          return node;
        }
      } else {
        if (node.data > key) {
          node.left = _deleteNode(node.left, key);
        } else {
          node.right = _deleteNode(node.right, key);
        }
        return node;
      }
    } else {
      return null
    }
}
this.deleteNode = function(key) {
  _deleteNode(this.root, key);
}
}

// test area
var dataArr = [8, 2, 9, 6, 10, 11, 5, 4, 3, 1];
var binaryTree = new BinaryTree();
dataArr.forEach((val, index) => {
  binaryTree.insertNode(val);
});
```
