---
title: Binary Search Trees in C++
date: 2025-06-22 12:00:00 +0530
categories: [Coding, Data Stuctures and Algorithms]
tags: [data structures, algorithms, c++]     # TAG names should always be lowercase
---
[![graph-2.png](https://i.postimg.cc/SRQ5XQ6Y/graph-2.png)](https://postimg.cc/sQ8Kqr03)

## Introduction

A Binary Search Tree (BST) is a special type of binary tree where each input value is positioned based on a simple rule: values less than a given node are placed on its left, while values greater than the node go to its right. The central node is referred to as the parent or root, and the values branching off to its left and right are called its children. These children can themselves have children, forming what are known as subtrees.

The main goal of using a BST is to organize data in a way that makes key operations like sorting, searching, inserting, deleting, and traversing more efficient. The time complexity of operations on the binary search tree is linear with respect to the height of the tree.

Binary search trees are also a fundamental data structure used in construction of abstract data structures such as sets, multisets, and associative arrays.
## Setup

Each node possesses a node value and two children, left and right. To facilitate more broader operations and easier implementations we will also store pointer to parent in the nodes. Node structure in code is as follows:
```cpp
struct Node
{
	int val;
	Node *left;
	Node *right;
	Node *parent;
	//various constructors
};
```
Ultimate bst class looks as follows:
```cpp
class BST
{
public:
	Node *root = nullptr;
    /*
    All other bst functionalities
    */
};
```
Important functionalities discussed in more detail in later sections

## Operations
### Searching
Searching can be done recursively or iteratively. 
Searching in a Binary Search Tree starts at the root node. If the tree is empty (i.e., the root is nullptr), the key you're looking for isn't present. If the root node contains the key, the search is successful and that node is returned.

If the key is smaller than the root's value, the search continues in the left subtree. If it's larger, the search moves to the right subtree. This process repeats moving left or right based on comparisons until the key is found or a nullptr is reached.

Reaching a nullptr means the key doesn’t exist in the tree.
```cpp
bool search(Node *trav, Node *&temp, int val)
{
    if (trav == nullptr)
        return false;

    if (val == trav->val)
    {
        //val searched found, set temp to it
        temp = trav;
        return true;
    }
    else if (val < trav->val)
        return search(trav->left, temp, val);
    else
        return search(trav->right, temp, val);
}
```
#### Time Complexity of Search in a Binary Search Tree

Searching in a Binary Search Tree (BST) leverages its sorted structure: for each comparison, the algorithm eliminates half of the remaining tree. This makes the search operation efficient — but only under certain conditions.

- In the **best-case scenario**, the tree is **balanced**, meaning the left and right subtrees are roughly equal in height. This allows the search to skip large portions of the tree, similar to binary search.

- In the **worst-case scenario**, the BST behaves like a **linked list** — for example, when nodes are inserted in sorted order. In this case, each comparison only skips one node.

#### Time Complexities:
- **Best/Average Case (Balanced Tree):** `O(log n)`
- **Worst Case (Unbalanced Tree):** `O(n)`

> To ensure logarithmic performance, we can consider using **self-balancing trees** like AVL or Red-Black Trees.

#### Minimum and Maximum Node

In a Binary Search Tree:

- The **minimum node** is found by continuously moving to the **left child** until we reach the leftmost node.
- The **maximum node** is found by continuously moving to the **right child** until we reach the rightmost node.

These utility functions help in operations like finding a node's successor or predecessor.

```cpp
Node* minNode(Node* x)
{
	while (x->left)
		x = x->left;
	return x;
}

Node* maxNode(Node* x)
{
	while (x->right)
		x = x->right;
	return x;
}
```
#### Successor

The **successor** of a node in a Binary Search Tree is the node with the **smallest key greater than** the current node’s key.

- If the node has a right subtree, the successor is the **leftmost node** in the right subtree.
- If the node has no right subtree, we move up using the parent pointer until we find a node that is the **left child of its parent**, that parent is the successor.

```cpp
Node* successor(Node* x)
{
	if (x->right)
		return minNode(x->right);

	Node* y = x->parent;
	while (y && x == y->right)
	{
		x = y;
		y = y->parent;
	}
	return y;
}
```
#### Predecessor

The **predecessor** of a node in a Binary Search Tree is the node with the **largest value that is less than** the given node’s value.

There are two possible cases to find the predecessor:

1. **Left Subtree Exists:**  
   If the node has a left child, the predecessor is the **rightmost node** (i.e., the maximum) in its left subtree.

2. **No Left Subtree:**  
   If the node has no left child, we move **upward** using the parent pointer until we find a node that is a **right child of its parent**. That parent is the predecessor.

```cpp
Node* predecessor(Node* x)
{
	if (x->left)
		return maxNode(x->left);

	Node* y = x->parent;
	while (y && x == y->left)
	{
		x = y;
		y = y->parent;
	}
	return y;
}
```
### Insertion

During insertion in a Binary Search Tree we place the new node at the correct position based on its value, preserving the BST property: values less than the current node go to the left; values greater go to the right.

```cpp
void insertion(Node* z)
{
	Node* y = nullptr;
	Node* x = root;
	while (x)
	{
		y = x;
		if (x->val > z->val)
			x = x->left;
		else
			x = x->right;
	}
	z->parent = y;
	if (!y)
		root = z;
	else if (z->val < y->val)
		y->left = z;
	else
		y->right = z;
}
```
#### Time Complexity

- **Best/Average Case:** `O(log n)` — when the tree is balanced  
- **Worst Case:** `O(n)` — when the tree becomes skewed (like a linked list)


### Deletion

Deleting a node from a Binary Search Tree involves handling three distinct cases to maintain the BST properties:

#### Deletion Cases

1. **No children (leaf node):**  
   Remove the node directly.

2. **One child:**  
   Replace the node with its only child.

3. **Two children:**  
   Find the node’s in-order successor (smallest node in the right subtree), replace the node with the successor, and update child and parent links accordingly.

```cpp
void delete_node(int val)
{
    Node *z = get_node(val);
    if (!z)
        return;
    if (z->left == nullptr)
    {
        shift_nodes(z, z->right);
    }
    else if (z->right == nullptr)
    {
        shift_nodes(z, z->left);
    }
    else
    {
        Node *y = successor(z);
        if (y->parent != z)
        {
            shift_nodes(y, y->right);
            y->right = z->right;
            if (y->right)
                y->right->parent = y;
        }
        shift_nodes(z, y);
        y->left = z->left;
        if (y->left)
            y->left->parent = y;
    }
}
```

Shift node helper function is as follows:
```cpp
void shift_nodes(Node *u, Node *v)
{
    if (u->parent == nullptr)
        root = v;
    else if (u == u->parent->left)
        u->parent->left = v;
    else
        u->parent->right = v;
    if (!v)
        v->parent = u->parent;
}
```
#### Time Complexity

- **Best/Average Case:** `O(log n)` — when the tree is balanced  
- **Worst Case:** `O(n)` — when the tree is skewed (all nodes in one branch)

The time complexity is determined by the height of the tree, as both searching for the node and adjusting pointers depend on traversal depth.


### Ending notes

Without balancing, repeated insertions or deletions in a binary search tree can cause it to degrade into a linear structure with height equal to the number of nodes `n`. This results in search performance becoming no better than linear search. To maintain efficient lookup times, it's important to keep the tree's height bounded by `O(log n)`. This is why one can look into self-balancing techniques, such as height-based or weight-based balancing.

You can find the code [here](https://github.com/dhruvkumaragrawal85/Data-Structures-Algorithms/blob/main/misc/11_bst.cpp).

Learn more about Binary Search Trees and Self Balancing trees [here](https://en.wikipedia.org/wiki/Binary_search_tree).
