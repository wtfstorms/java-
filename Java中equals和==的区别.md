## Java中equals和==的区别
java中的数据类型，可分为两类：
### 1.基本数据类型，也称原始数据类型。
byte,short,char,int,long,float,double,boolean
他们之间的比较，应用双等号(==),比较的是他们的值。

### 2.复合数据类型(类)
当他们用双等号(==)进行比较的时候，比较的是他们在内存中的存放地址，所以，除非是同一个new出来的对象，他们的比较后的结果为true，否则比较后结果为false。

#### JAVA当中所有的类都是继承于Object这个基类的，在Object中的基类中定义了一个equals的方法，这个方法的初始行为是比较对象的内存地 址，但在一些类库当中这个方法被覆盖掉了，如String,Integer,Date在这些类当中equals有其自身的实现，而不再是比较类在堆内存中的存放地址了。
对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是基于他们在内存中的存放位置的地址值的，因为Object的equals方法也是用双等号（）进行比较的，所以比较后的结果跟双等号（==）的结果相同。

```
public class TestString {
public static void main(String[] args) {
String s1 = "Monday";
String s2 = "Monday";
if (s1 == s2)
{
System.out.println("s1 == s2");}
else{
System.out.println("s1 != s2");}
}
}
```

编译并运行程序，输出：s1 == s2说明：s1 与 s2 引用同一个 String 对象 – “Monday”!

### 2.再稍微改动一下程序，会有更奇怪的发现：
```
public class TestString {
public static void main(String[] args) {
String s1 = "Monday";
String s2 = new String("Monday");
if (s1 == s2)
{System.out.println("s1 == s2");}
else
{System.out.println("s1 != s2");}
if (s1.equals(s2)) {System.out.println("s1 equals s2");}
else{
System.out.println("s1 not equals s2");}
}
}
```
我们将s2用new操作符创建
程序输出：
s1 != s2
s1 equals s2
说明：s1 s2分别引用了两个"Monday"String对象

### 3. 字符串缓冲池
原来，程序在运行的时候会创建一个字符串缓冲池当使用 s1 = “Monday” 这样的表达是创建字符串的时候，程序首先会在这个String缓冲池中寻找相同值的对象，在第一个程序中，s1先被放到了池中，所以在s2被创建的时候，程序找到了具有相同值的 s1
将s2引用s1所引用的对象"Monday"
第二段程序中，使用了 new 操作符，他明白的告诉程序："我要一个新的！不要旧的！"于是一个新的"Monday"Sting对象被创建在内存中。他们的值相同，但是位置不同，一个在池中游泳一个在岸边休息。哎呀，真是资源浪费，明明是一样的非要分开做什么呢？

### 4.再次更改程序：
```
public class TestString {
public static void main(String[] args) {
String s1 = "Monday";
String s2 = new String("Monday");
s2 = s2.intern();
if (s1 == s2)
{System.out.println("s1 == s2");}
else
{System.out.println("s1 != s2");}
if (s1.equals(s2)) {System.out.println("s1 equals s2");}
else{
System.out.println("s1 not equals s2");}
}
}
```
这次加入：s2 = s2.intern();
程序输出：
s1 == s2
s1 equals s2

原 来，（java.lang.String的intern()方法"abc".intern()方法的返回值还是字符串"abc"，表面上看起来好像这个方法没什么用处。但实际上，它做了个小动作：检查字符串池里是否存在"abc"这么一个字符串，如果存在，就返回池里的字符串；如果不存在，该方法会 把"abc"添加到字符串池中，然后再返回它的引用。
