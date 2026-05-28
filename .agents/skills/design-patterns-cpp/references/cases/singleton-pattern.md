@[toc]
## 一、单例模式的概念
单例模式（Singleton Pattern）是一种创建型设计模式，它的核心思想是：**保证在一个进程中，某个类仅有一个实例，并提供全局访问点。**

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d2c479a98b4f49edafdd79c7df58b8e9.png)
**常见应用场景：**
>  - 日志系统：全局唯一的日志记录器。
>  - 配置管理：全局读取配置文件。
>  - 线程池、数据库连接池：全局资源管理。
>  - 设备驱动对象：保证唯一控制入口。

**单例模式的关键要素：**
> - 构造函数私有化：防止外部随意 new。
>  - 拷贝构造与赋值运算符删除：防止复制对象。
>  - 静态成员指针/对象：存储唯一实例。
>  - 公共静态方法：提供获取实例的入口。

## 二、实例分析
**问题：**
 单例模式同时解决了两个问题， 所以违反了单一职责原则：
1.**保证一个类只有一个实例。** 为什么会有人想要控制一个类所拥有的实例数量？ 最常见的原因是控制某些共享资源 （例如数据库或文件） 的访问权限。
 
 它的运作方式是这样的： 如果你创建了一个对象， 同时过一会儿后你决定再创建一个新对象， 此时你会获得之前已创建的对象， 而不是一个新对象。

注意， 普通构造函数无法实现上述行为， 因为构造函数的设计决定了它必须总是返回一个新对象。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c1afbda45e9648178078ad7cc406661f.png)
<center> 客户端甚至可能没有意识到它们一直都在使用同一个对象。

**2.为该实例提供一个全局访问节点。** 还记得你用过的那些存储重要对象的全局变量吗？ 它们在使用上十分方便， 但同时也非常不安全， 因为任何代码都有可能覆盖掉那些变量的内容， 从而引发程序崩溃。

和全局变量一样， 单例模式也允许在程序的任何地方访问特定对象。 但是它可以保护该实例不被其他代码覆盖。

还有一点： 你不会希望解决同一个问题的代码分散在程序各处的。 因此更好的方式是将其放在同一个类中， 特别是当其他代码已经依赖这个类时更应该如此。


**解决方案：**
所有单例的实现都包含以下两个相同的步骤：
>  - 将默认构造函数设为私有， 防止其他对象使用单例类的new运算符。
>  - 新建一个静态构建方法作为构造函数。 该函数会 “偷偷” 调用私有构造函数来创建对象， 并将其保存在一个静态成员变量中。 此后所有对于该函数的调用都将返回这一缓存对象。
>  
如果你的代码能够访问单例类， 那它就能调用单例类的静态方法。 无论何时调用该方法， 它总是会返回相同的对象。

## 三、单例模式的结构
所有单例的实现都包含以下两个相同的步骤：
>  - 将默认构造函数设为私有， 防止其他对象使用单例类的 new运算符。
>  - 新建一个静态构建方法作为构造函数。 该函数会 “偷偷” 调用私有构造函数来创建对象， 并将其保存在一个静态成员变量中。 此后所有对于该函数的调用都将返回这一缓存对象。

如果你的代码能够访问单例类， 那它就能调用单例类的静态方法。 无论何时调用该方法， 它总是会返回相同的对象。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0860c9870b8142f2b722e4fdb164aae8.png)
## 四、常见实现方式
>  1. 在类中添加一个私有静态成员变量用于保存单例实例。
>  2. 声明一个公有静态构建方法用于获取单例实例。
>  3. 在静态方法中实现"延迟初始化"。 该方法会在首次被调用时创建一个新对象， 并将其存储在静态成员变量中。 此后该方法每次被调用时都返回该实例。
>  4. 将类的构造函数设为私有。 类的静态方法仍能调用构造函数， 但是其他对象不能调用。
>  5. 检查客户端代码， 将对单例的构造函数的调用替换为对其静态构建方法的调用。

**函数内静态变量初始化的线程安全问题：**
在 C++98/03 中：
>  - 函数内的静态局部变量在第一次调用函数时初始化。
>  - 多线程调用时，如果两个线程同时进入函数，就可能同时执行初始化，造成多次构造，属于竞态条件（Race Condition）。

所以在 C++98/03 里，需要手动加锁才能保证安全。

C++11 标准明确规定：**函数内静态局部变量在第一次初始化时，初始化过程是线程安全的。**
也就是说：
>  - 即使多个线程同时调用 getInstance()，只有一个线程会执行构造函数。
>  - 其他线程会等待构造完成，然后使用同一个实例。

### 3.1 饿汉式单例
饿汉式的特点：
>  - 类加载时就创建实例，不管你用不用它，它都存在。
>  - 线程安全：因为实例在程序开始时就初始化了，不存在多线程并发创建的问题。
>  - 资源消耗：如果对象很大而一直没用到，会浪费内存。
>  - 适用场景：对象创建成本低，且在程序运行中几乎一定会用到。

**静态成员对象**
```cpp
#include <iostream>

class Singleton {
private:
    Singleton() { std::cout << "Singleton Created\n"; } // 构造函数私有化
    ~Singleton() { std::cout << "Singleton Destroyed\n"; }

    Singleton(const Singleton&) = delete;               // 禁止拷贝
    Singleton& operator=(const Singleton&) = delete;    // 禁止赋值

    static Singleton instance;   // 静态成员，类加载时即初始化

public:
    static Singleton& getInstance() {
        return instance;         // 返回唯一实例
    }

    void show() {
        std::cout << "Hello Hungry Singleton!" << std::endl;
    }
};

// 静态成员初始化（在程序启动时创建）
Singleton Singleton::instance;

int main() {
    Singleton& s1 = Singleton::getInstance();
    Singleton& s2 = Singleton::getInstance();

    s1.show();
    std::cout << "s1 addr = " << &s1 << ", s2 addr = " << &s2 << std::endl;
    return 0;
}
```
输出结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/37672665e0774c44b56e602bf152e4b3.png)
**实现原理（简要）：**

>  - 编译器在生成代码时，会在静态变量前加上一次性标志（guard variable）。
>  - 第一个线程进入函数时，检查标志：
>  		- 如果未初始化 → 执行构造函数 → 设置标志
>  		- 如果已初始化 → 直接返回实例
>  - 编译器会保证对标志的写入和检查是原子操作或通过内部锁完成，从而保证线程安全。

### 3.2 懒汉式单例
懒汉式的特点：
> - 延迟初始化：只有第一次调用 getInstance() 时才创建对象。
>  - 线程安全问题：多线程环境下，需要注意可能出现多个实例的问题。
>  - 优点：节省资源，只有在真正需要时才创建。
>  - 缺点：实现稍复杂，需要考虑多线程安全。

**懒汉式实现（C++11 推荐写法）**
C++11 后，使用 函数内静态变量 可以保证线程安全：
```cpp
#include <iostream>

class Singleton {
private:
    Singleton() { std::cout << "Singleton Created\n"; } // 构造私有
    ~Singleton() { std::cout << "Singleton Destroyed\n"; }

    Singleton(const Singleton&) = delete;               // 禁止拷贝
    Singleton& operator=(const Singleton&) = delete;    // 禁止赋值

public:
    static Singleton& getInstance() {
        static Singleton instance;  // 第一次调用时创建，C++11线程安全
        return instance;
    }

    void show() {
        std::cout << "Hello Lazy Singleton!" << std::endl;
    }
};

int main() {
    Singleton& s1 = Singleton::getInstance();
    Singleton& s2 = Singleton::getInstance();

    s1.show();
    std::cout << "s1 addr = " << &s1 << ", s2 addr = " << &s2 << std::endl;

    return 0;
}
```
输出结果：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/5cd4bcf07a3747dd91ffd9a771ea771e.png)
**懒汉式的多线程安全写法（C++11 前）**
如果在 C++11 之前，需要手动加锁防止多线程同时创建多个实例：
```cpp
#include <mutex>

class Singleton {
private:
    Singleton() {}
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;

    static Singleton* instance;
    static std::mutex mtx;

public:
    static Singleton* getInstance() {
        if (instance == nullptr) {
            std::lock_guard<std::mutex> lock(mtx);
            if (instance == nullptr) {
                instance = new Singleton();
            }
        }
        return instance;
    }
};

// 静态成员初始化
Singleton* Singleton::instance = nullptr;
std::mutex Singleton::mtx;
```

