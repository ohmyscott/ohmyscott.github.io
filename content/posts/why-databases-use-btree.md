---
title: "为什么每个数据库都在用B树"
date: 2025-11-30
draft: false
slug: "why-databases-use-btree"
categories: ["技术"]
tags: ["数据库", "B-Tree"]
ShowToc: true
TocOpen: true
---

作为开发者，我经常好奇为什么几乎所有数据库都在使用B-Tree这种看似古老的数据结构。今天我来深入探究一下这个技术选择的底层逻辑。

## 核心问题：磁盘I/O瓶颈

数据库性能的瓶颈从来不是CPU，而是磁盘I/O。让我用数据来说明：

| 存储层次 | 访问延迟 | 相对内存倍数 |
|---------|---------|-------------|
| 内存     | ~100ns  | 1x          |
| SSD     | ~100μs  | 1000x       |
| HDD     | ~10ms   | 100,000x    |

更关键的是，磁盘的最小访问单位是块（通常4KB-16KB），即使你只需要1个字节，也要读取整个块。

## 传统二叉搜索树的问题

假设我们要在10亿条记录中查找数据：

```bash
# 二叉搜索树需要访问的层数
log₂(1,000,000,000) ≈ 30次磁盘I/O

# 在HDD上的耗时
30 × 10ms = 300ms  # 用户明显能感受到的延迟
```

每次访问一个节点都需要一次磁盘I/O，性能完全无法接受。

## B-Tree的精妙设计

B-Tree的核心思想很简单：**充分利用每次磁盘I/O必须读取的整个块**。

### 高扇出（High Fanout）特性

B-Tree每个节点存储大量键值，实现高扇出：

```
传统二叉树:     B-Tree (扇出100):
      O                O
     / \              / | ... \
    O   O           O   O ...   O   (100个子节点)
```

**性能对比**：
- 二叉搜索树：30次I/O = 300ms
- B-Tree（扇出100）：5次I/O = 50ms
- **性能提升：6倍！**

### 节点结构优化

```go
// B-Tree节点结构（简化版）
type BTreeNode struct {
    keys     []Key           // 键值对（Key-Value pairs）
    children []*BTreeNode    // 子节点指针（Child node pointers）
    data     interface{}     // 叶子节点存储数据（Leaf node data storage）
}
```

每个节点大小≈一个磁盘块，内部节点只存储分隔键（Separator Keys），数据全部存储在叶子节点。

## 实际应用中的B-Tree

所有主流数据库都在使用：

| 数据库 | 实现方式 | 特点 |
|--------|---------|------|
| MySQL InnoDB | B+Tree | 主键索引（Primary Key）和二级索引（Secondary Index） |
| PostgreSQL | B-Tree | 默认索引类型（Default Index Type） |
| SQLite | B-Tree | 表和索引都用B-Tree |
| MongoDB WiredTiger | B-Tree | 索引结构（Index Structure） |

### 自平衡（Self-Balancing）机制

B-Tree通过分裂（Split）和合并（Merge）操作维护平衡：

```python
def insert(key, data):
    if node_is_full(node):
        split_node(node)  # 节点分裂（Node Splitting）
    insert_recursively(node, key, data)

def delete(key):
    delete_from_node(node, key)
    if node_is_too_empty(node):
        merge_with_sibling(node)  # 节点合并（Node Merging）
```

## 为什么50年后仍然是主流

B-Tree的成功在于**完美匹配硬件特性**：

1. **最大化单次I/O收益** - 读取一个块获得大量信息
2. **最小化树高度** - 减少磁盘访问次数
3. **保持有序性** - 支持高效的范围查询

### 适用场景分析

**选择B-Tree的情况**：
- ✅ 磁盘基础存储（Disk-based Storage）
- ✅ 频繁读取，适度写入（Read-heavy with moderate writes）
- ✅ 范围查询需求（Range Queries）
- ✅ 通用OLTP工作负载（Online Transaction Processing）

**考虑替代方案**：
- ❌ 写入密集型 → LSM-Tree（Log-Structured Merge Tree）
- ❌ 全内存数据 → 哈希索引（Hash Index）、跳表（Skip List）
- ❌ 分析查询 → 列式存储（Columnar Storage）

## 总结

B-Tree之所以能在50年后的今天仍然统治数据库世界，不是因为理论有多么复杂，而是因为它的设计哲学与磁盘物理特性完美契合。每一次磁盘I/O都被充分利用，每一层树结构都经过精心优化。

这种对硬件特性的深度理解和适配，正是系统设计的精髓所在。

## 参考资料

- [B-Trees: Why Every Database Uses Them](https://mehmetgoekce.substack.com/p/b-trees-why-every-database-uses-them)