# 链表（Linked List）

## 1. 概述

### 1.1 单向链表

<left><img src="images\01-singly-linked-list.png" style="zoom: 60%;" alt="单向链表"/></left>

### 1.2 双向链表

<left><img src="images\02-double-linked-list.png" style="zoom: 60%;" alt="单向链表"/></left>

## 2. 链表的实现

### 2.1 使用数组实现

```java
public class AList {
    public int[] data, next;

    public AList(int size) {
        data = new int[size];
        next = new int[size];
    }

    public void add(int preIndex, int curIndex, int val) {
        next[curIndex] = next[preIndex];
        next[preIndex] = curIndex;
        data[curIndex] = val;
    }

    public static void main(String[] args) {
        AList aList = new AList(10);
        int head = 3;
        aList.data[3] = 0;
        aList.add(3, 5, 1);
        aList.add(5, 2, 2);
        while (head != 0) {
            System.out.print(aList.data[head] + " ");
            head = aList.next[head];
        }
    }
}
```

### 2.2 使用链表实现

```java
public class NList {
    class Node {
        int val;
        Node next;

        Node(int val) {
            this.val = val;
        }
    }
    private Node head, cur;
    public NList() {
        head = new Node(0);
        cur = head;
    }
    public void add(int val) {
        cur.next = new Node(val);
        cur = cur.next;
    }
    public void print() {
        Node temp = head;
        while (temp.next != null) {
            System.out.print(temp.next.val + " ");
            temp = temp.next;
        }
    }
    public static void main(String[] args) {
        NList nList = new NList();
        nList.add(1);
        nList.add(2);
        nList.add(3);
        nList.print();
    }
}
```

## 3. 链表的应用场景

### 3.1 操作系统内的动态内存分配

<left><img src="images\03-malloc.png" style="zoom: 80%;" alt="内存分配"/></left>

### 3.2 LRU缓存淘汰算法

<left><img src="images\04-lru.png" style="zoom: 80%;" alt="内存分配"/></left>

## 4. 链表的操作

