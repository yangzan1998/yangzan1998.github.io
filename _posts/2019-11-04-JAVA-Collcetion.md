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
<# 测试Collection接口方法的方法
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
   c.contains("高老二")；//返回一个boolean。

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
