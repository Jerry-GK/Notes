# Java环境



<img src="/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221009154447799.png" alt="image-20221009154447799" style="zoom: 50%;" />

Java先将代码视为字节码，交给JVM进行编译。

# Java项目结构

项目project > 模块module > 包package > 类class

类是最小编译单元，编译形成.class文件。



# Java类

![image-20221009154920992](/Users/jerryliterm/Library/Application Support/typora-user-images/image-20221009154920992.png)

在C++中没有包的概念，Java中同一个包中的类相关性往往较强，default权限可以允许本包类访问。



类构建流程：

静态成员构建与初始化

非静态成员变量初始化

构造函数执行



final关键字：

修饰成员变量：变量不可改变，即常量，常跟static搭配。

修饰成员方法：不能被子类override，自身必须有函数体。

修饰类：不能被继承的，功能完整的类。



static关键字（共享，只有一份数据）：

修饰成员变量：表示该变量被所有对象共享。

修饰成员方法：不依赖具体对象调用，无法访问非静态成员变量



# 接口Interface

抽象：

Java没有virtual关键字，大多数情况下默认函数均为virtual（动态绑定，除非不可能被重写的情况是静态绑定）

c++用virtual = 0表示一个函数是纯虚函数，必须被子类重写，且类为抽象类，不能制造对象，必须有将纯虚函数全部重写完毕的子类。

Java中abstract关键字可以实现上述功能。

abstract修饰类，表示抽象类，抽象类中可以定义抽象函数，也用abstract修饰，无函数体，必须被子类重写，切不能private。

一般的抽象类也可以有正常的非抽象函数，子类可以选择是否重写。



接口（完全抽象类):

接口也是一种类，内部所有成员函数均为抽象函数，没有实现。

具体性质是：

- 所有成员方法都是抽象的，且是public的
- 所有成员变量都是**public static final**的（常量）

有单独的关键字interface替代abstract class

public interface InterfaceName extends BaseInterfaces

接口也可以继承。

注意，如果有**子类想要实现接口**

用关键字implements

class ClassName implements interfaces

虽然Java不允许多继承，但是允许一个类同时实现多个接口。



# 内部类





























































