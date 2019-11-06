---
layout:   post
title:    java Collection方法
subtitle: Hello World
date:     2019-11-04
author:   BY 人间喜剧
header-img: img/post-bg-2015.jpg
catalog: ture
tags:
     - java
---
# Collection概念：
![avatar](img/1.jpg)
## List 是有序的，可重复的容器。
 list常用的实现类有三个：**ArrayList,Linked,Vector。**
 ArrayList，Vector的底层实现方式是数组，Linked的底层实现是链表。
## 测试Collection接口方法的方法
``` java
Collection<String> c =new ArrayList();
    
    c.size（）；//返回多少元素
    c.isEmpety();//是否为空
    c.add("杨老大")；
    c.add("杨老二");//加元素
    remove("杨老二");//移除，并不是删除，对象还在，只是删除了地址 
```
![img](http://b338.photo.store.qq.com/psb?/V122tq581jjO6d/WcCysEfWbVrfsoTHUokWozheynIFTaPjYMxRmpwaC6Y!/c/dFIBAAAAAAAA&bo=QgINAkICDQIRADc!)
   ``` java
   c.clear（）；//移除所有元素；
  Object[] objs=c.toArray();//转化出一个object数组；
   c.contains("高老二")；//是否包含这个元素，返回一个boolean。

	List<String> list1=new ArrayList<>();
	list1.add("aa");
	list1.add("bb");
	list1.add("cc");
	
	List<String> list2=new ArrayList<>();
	list2.add("aa");
	list2.add("dd");
	list2.add("ee");
	System.out.println("List1:"+list1);
	list1.addAll(list2);//把两个所有List中的所有元素都想合成一块。
	System.out.println("List1:"+list1);
```
![img](http://b190.photo.store.qq.com/psb?/V122tq581jjO6d/IGAeiS2qVGK9v0QtYTn*QUIXLZy7wsVSMXv0CM89qy0!/b/dL4AAAAAAAAA&bo=WgFFAFoBRQARADc!&rf=viewer_311)
  ```java
  list1.removeAll(list2);//取并集
  System.out.println("List1:"+list1);
 ``` 
 ![img](http://b318.photo.store.qq.com/psb?/V122tq581jjO6d/cO8zqQ2iIjw4ZHVkbGRBkiIyyxu3eq8oB4fbHnpJXPY!/b/dD4BAAAAAAAA&bo=FgGYABYBmAARADc!&rf=viewer_311)
   ```java
   list1.retainAll(list2);//取交集
   System.out.println("List1:"+list1);
  ```
  ![img](http://b340.photo.store.qq.com/psb?/V122tq581jjO6d/d2mEUMHaqcG*c9klSOh7B97HnqZSzaxUtAYpdt7HbRw!/b/dFQBAAAAAAAA&amp;bo=*gB9AP4AfQARADc!&rf=viewer_311)
  ```java
  System.out.println(list1.containsAll(list2));//判断是否全部包含。
  ```
  ![img](http://a3.qpic.cn/psb?/V122tq581jjO6d/ncNl3TQ.Gqx9AKIQFfBg*lIeHbLZxjh5Jge*h5FTPk0!/b/dFIBAAAAAAAA&ek=1&kp=1&pt=0&bo=*gBkAP4AZAARADc!&tl=3&vuin=1337734586&tm=1572858000&sce=60-2-2&rf=viewer_311)


 ArrayList，Vector的底层实现方式是数组，Linked的底层实现是链表。
 #### 以下是关于数组索引有关的方法
 在Arraylist中还存在一个add（）的重写方法，存在两个参数：add（int arg0，string arg1）在这个方法中，我们可以指定位置加入一个数组元素。
 ``` java
 List<String> list =new ArrayList<>();
		list.add("A");
		list.add("B");
		list.add("C");
		list.add("D");
		
		System.out.println(list);
		
		list.add(2,"杨赞");
		System.out.println(list);
```
这样我们就可以在数组下标位置为2的地方加入一个“杨赞”的字符串。如果想要删除这个字符串可以：
``` java
  list.remove(2);
 ```
 如果我们想改变一个元素，把我们的“C”元素换成杨老二我们可以
 ```java
 list.set(2,"杨老二")；
 ```
 z这样我们的数组元素就变成了**A,B,杨老二,D**；
 
如果我想找一个元素的索引位置。我们可以用index（object o）;来查找。如果没有这个要查找的object对象，那么返回-1.
## ArrayList 底层源码分析：
数组是长度有限的，数组的特点是：查询效率高，增删效率低，线程不安全。但是ArrayList可以存放任意数量的对像，长度不受限制。原因如下：
![img](http://b309.photo.store.qq.com/psb?/V122tq581jjO6d/9yPeW14fXeo4vb8KaxHNqfxjILdoL5QW6kRjoDrE14c!/b/dDUBAAAAAAAA&bo=hAJcAYQCXAERADc!&rf=viewer_311)
#### 自定义实现ArrayList：
```java
public class SxtArrayList {
	private Object[] elementData;
	private int size;
	private static final int DEFALT_CAPACITY=10;
	public SxtArrayList(){
		elementData=new Object[DEFALT_CAPACITY];
	}
	public SxtArrayList(int capacity){
		elementData=new Object[capacity];
	}
	//这里加一个add方法，测试一下我们的我们的ArrayList。
	public void add(Object obj){
		elementData[size++]=obj;
	}
	
	public String toString() {
		StringBuilder sbBuilder=new StringBuilder();
		sbBuilder.append("[");
		for (int i = 0; i < size; i++) {
			sbBuilder.append(elementData[i]+",");
		}
		sbBuilder.setCharAt(sbBuilder.length()-1,']');
		return sbBuilder.toString();
		
	}
	
	
	public static void main(String[] args){
		SxtArrayList s1=new SxtArrayList();
		SxtArrayList s2=new SxtArrayList(20);
		s1.add("aa");
		s1.add("bb");
		System.out.println(s1);//这样我们只能输出数组名与地址。要输出数组我们可以重写toString方法
	}

}
```
#### 运行出的结果就是[aa,bb]
在这里我们就先不介绍数组扩容 索引问题了。
## LinkedList链表问题：
LinkedList是底层双向链表实现存储。特点是：查询效率低，增删效率高，线程不安全。
#### 双向链表介绍：
数据节点两个指针，分别指向前一个结点还有后一个结点。
![img](http://b340.photo.store.qq.com/psb?/V122tq581jjO6d/Zn2P.NdXj1U85uk4Xkpi7GbK0LKlWmXkoQ0GL2V4c2o!/b/dFQBAAAAAAAA&bo=egPrAHoD6wARADc!&rf=viewer_311)
#### 结点
根据连链表的规则，我们的每个结点应该有三个内容：
```java
class Node{
    Node previous;  //前一个结点
    Object element;  //本结点保存的数据
    Node next;  //后一个结点
    public Node(Node previous, Node next, Object element) {
		super();
		this.previous = previous;
		this.next = next;
		this.element = element;
	}


	public Node(Object element) {
		super();
		this.element = element;
	}
	
	
}
}
```
定义好Node类以后，我们就开始手动实现LinkedList:
```java
public class SxtLinkedList {

	private Node first;
	private Node last;
	
	private int size;
	
	//目前链表为空
	public void add(Object object){
		Node node=new Node(object);
		
		if(first==null){
			//node.previous=null
			
			
			first=node;
			last=node;
		}else {
			//当我们加入新元素的时候。节点的前一个属性就是我们没加入属性列表的最后最一个。
			node.previous=last;
			node.next=null;
			//新的node加入后 last改变
			last.next=node;
			last=node;
		}
	}
	public String toString() {
		//[a,b,c]  first=a last=c;
		//temp指向first 就是a然后到a.next....直到c.next 为空，不执行
		Node temp=first;
		while (temp!=null) {
			System.out.println(temp.element);
			temp=temp.next;
		}
		return"";

	
	
}
	public static void main(String[] args) {
		SxtLinkedList linkedList=new SxtLinkedList();
		linkedList.add("a");
		linkedList.add("b");
		linkedList.add("c");
		System.out.println(linkedList);
	}
}
```
重写toString函数后，我们得到程序的结果是：
a
b
c




