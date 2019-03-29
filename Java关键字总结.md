# Java关键字总结

一共 个关键字



八大基

| short       | int        | long        | float      | double        | byte          | char        | boolean        |
| ----------- | ---------- | ----------- | ---------- | ------------- | ------------- | ----------- | -------------- |
| **Integer** | **Float**  | **Double**  | **Byte**   | **Character** | **Boolean**   | **String**  | **BigInteger** |
| BigDecimal  | **import** | **package** | **public** | **default**   | **protected** | **private** | **final**      |
| class       | abstract   | extends     | implements |               |               |             |                |
|             |            |             |            |               |               |             |                |
|             |            |             |            |               |               |             |                |
|             |            |             |            |               |               |             |                |

八大数据类型：

- short

- int
- long
- float
- double
- byte
- char
- boolean

引用数据类型：

- Integer
- Float
- Double
- Byte
- Character
- String

访问修饰符：

- public
- default
- protected
- private

包管理：

- import
- package



## final

final修饰类、方法和变量。

修饰类、不可被继承。

修饰方法、不可被重写（覆盖）。

***当final修饰一个基本数据类型时，表示该基本数据类型的值一旦在初始化后便不能发生变化；如果final修饰一个引用类型时，则在对其初始化之后便不能再让其指向其他对象了，但该引用所指向的对象的内容是可以发生变化的***。

**final修饰一个成员变量（属性），必须要显示初始化。****这里有两种初始化方式**，一种是在变量声明的时候初始化；第二种方法是在声明变量的时候不赋初值，但是要在这个变量所在的类的所有的构造函数中对这个变量赋初值。

***Java中常见的final类***

Integer、Float、Double、Byte、Character、String。

***用final修饰成员变量可以保证线程的安全性***。

***使用final的好处：***

1. 被final修饰的常量，在编译阶段会存入调用类的常量池中。
2. 被final修饰的方法，JVM会尝试为之寻求内联，这对于提升Java的效率是非常重要的。因此，假如能确定方法不会被继承，那么尽量将方法定义为final的。
3. final变量可以安全的在多线程环境下进行共享，而不需要额外的同步开销。

- volatile

- synchronized

