# Markdown快速入门（typora）

## 1.代码块：

~~~java
//代码块语法：
​~~~+语言类型
~~~

**1.java代码**

~~~java
public class Programmer extends Employee {
    private int memberId;
    private Status status = FREE;
    private Equipment equipment;

    public Programmer(int id, String name, int age, double salary, Equipment equipment) {
        super(id, name, age, salary);
        this.equipment = equipment;
    }

    public Programmer(int id, String name, int age, double salary, int memberId, Equipment equipment) {
        super(id, name, age, salary);
        this.memberId = memberId;
        this.equipment = equipment;
    }

    public int getMemberId() {
        return memberId;
    }

    public Status getStatus() {
        return status;
    }

    public Equipment getEquipment() {
        return equipment;
    }

    public void setMemberId(int memberId) {
        this.memberId = memberId;
    }

    public void setStatus(Status status) {
        this.status = status;
    }

    public void setEquipment(Equipment equipment) {
        this.equipment = equipment;
    }

    @Override
    public String toString() {
        return  super.toString()+"\t\t"+ status + "\t\t\t\t\t\t\t\t" + equipment;
    }
}
~~~

**2.shell脚本**

~~~shell
//linux下spring项目的启动命令
# java -jar blog start
~~~

## 2.标题

~~~java
//标题语法
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
~~~

# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题



## 3.字体样式

~~~java
//加粗
**加粗内容**
//代码高亮显示
==高亮内容==
//删除线
~~被删除的文字~~
//斜体
*斜体内容*
~~~

//加粗
**加粗内容**
//代码高亮显示
***==高亮内容==***
//删除线
~~被删除的文字~~
//斜体
*斜体内容*

## 4.引用

~~~java
//引用语法
>作者：点点君
>>作者：点点君
>>>作者：点点君
~~~

>作者：点点君
>>作者：点点君
>>>作者：点点君



## 5.分割线

~~~java
//分割线
---
//分割线2
***

~~~

//分割线1：

---

//分割线2：

***

---

---



## 6.图片插入

~~~java
//在线图片/本地图片
![我的照片](/image/me.png)--图片路径
~~~

![照片](https://exp-picture.cdn.bcebos.com/ba274f598540102a561fc116b142a07aa110c551.jpg?x-bce-process=image%2Fresize%2Cm_lfit%2Cw_500%2Climit_1%2Fformat%2Cf_jpg%2Fquality%2Cq_80)

![照片](D:\Typora\text\U202112038熊嘉晟.jpg)



## 7.超链接

~~~java
//超链接语法
[我的GitHub](https://github.com/yerenping)
~~~

//超链接语法
[我的GitHub](https://github.com/yerenping)



## 8.列表语法

~~~java
//无序列表
- 目录1
- 目录2
- 目录3
//有序列表
数字+.+名称
~~~

- 目录1
- 目录2
- 目录3
- 无序列表

1. 首页
2. 标签
3. 分类

## 9.表格

| 成绩   | 语文   | 数学   |
| ---- | ---- | ---- |
|      | 78   | 88   |
|      |      |      |
|      |      |      |

