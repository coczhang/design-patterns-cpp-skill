@[toc]
## 一、普通工厂方法模式的概念
它的核心思想是：由一个工厂类决定创建哪一种具体产品对象。
**特点：**
>  - 有一个抽象产品基类（或接口）。
>  - 有多个具体产品类（继承自产品基类）。
>  - 有一个工厂类，通过传入参数来决定实例化哪个产品。

**使用场景：**
>  - 产品种类较少；
>  - 对象创建逻辑不复杂；
>  - 需要屏蔽对象创建细节，让用户专注于使用。

该模式对对象创建管理方式最为简单，因为其仅仅简单的对不同类对象的创建进行了一层薄薄的封装。该模式通过向工厂传递类型来指定要创建的对象，其UML类图如下：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/271c97d412094f01be3233ef4133bc03.png)

示例代码：
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 1. 抽象产品
class Product {
public:
    virtual void show() = 0;
    virtual ~Product() = default;
};

// 2. 具体产品 A
class ProductA : public Product {
public:
    void show() override {
        cout << "This is Product A" << endl;
    }
};

// 3. 具体产品 B
class ProductB : public Product {
public:
    void show() override {
        cout << "This is Product B" << endl;
    }
};

// 4. 工厂类
class SimpleFactory {
public:
    static unique_ptr<Product> createProduct(const string& type) {
        if (type == "A") {
            return make_unique<ProductA>();
        } else if (type == "B") {
            return make_unique<ProductB>();
        }
        return nullptr;
    }
};

// 5. 客户端使用
int main() {
    auto p1 = SimpleFactory::createProduct("A");
    auto p2 = SimpleFactory::createProduct("B");

    if (p1) p1->show();
    if (p2) p2->show();

    return 0;
}
```

**优点：**
>  - 客户端不需要知道具体类名，只需要传入参数即可。
>  - 创建和使用分离，降低耦合度。
>  易于维护：修改产品创建逻辑时只需改动工厂，不影响客户端代码。


**缺点：**
>  - 违背开闭原则（OCP）：新增产品时需要修改工厂代码（新增 else if）。
>  - 工厂类会越来越臃肿。所有产品都集中到一个工厂，不利于扩展和维护。


**使用场景：**
>  - 小型项目，产品种类不多；
>  - 只需要一个工厂类管理对象创建；
>  - 对象创建过程可能包含一些额外逻辑（如日志、权限、缓存）。

例如：
>  - 数据库连接工厂：根据配置文件创建 MySQL 或 SQLite 连接；
>  - 图形界面控件工厂：根据字符串返回不同控件对象；
>  - 多媒体播放器：根据文件扩展名返回不同解码器对象。


**普通工厂方法模式的具体实例**
系统需要支持不同品牌的 NVR（大华、海康、TP-LINK），但客户端代码不应该直接依赖具体品牌类，而是通过一个统一的接口来操作。

实现步骤：
**1. 定义抽象产品接口**
```cpp
// NVR抽象接口
class INvr {
public:
    virtual ~INvr() {}
    virtual void connect() = 0;
    virtual void preview() = 0;
};
```
**2. 定义具体产品**
```cpp
#include <QDebug>

// 大华NVR
class DahuaNvr : public INvr {
public:
    void connect() override { qDebug() << "连接大华 NVR 成功"; }
    void preview() override { qDebug() << "大华 NVR 正在实时预览"; }
};

// 海康NVR
class HikvisionNvr : public INvr {
public:
    void connect() override { qDebug() << "连接海康 NVR 成功"; }
    void preview() override { qDebug() << "海康 NVR 正在实时预览"; }
};

// TP-LINK NVR
class TpLinkNvr : public INvr {
public:
    void connect() override { qDebug() << "连接 TP-LINK NVR 成功"; }
    void preview() override { qDebug() << "TP-LINK NVR 正在实时预览"; }
};
```

**3. 工厂类（普通工厂）**
```cpp
class NvrFactory {
public:
    static INvr* createNvr(const QString& type) {
        if (type == "Dahua") {
            return new DahuaNvr();
        } else if (type == "Hikvision") {
            return new HikvisionNvr();
        } else if (type == "TP-Link") {
            return new TpLinkNvr();
        }
        return nullptr;
    }
};
```
**4. 客户端使用**
```cpp
int main() {
    // 客户端只需要告诉工厂想要哪个品牌的NVR
    INvr* nvr = NvrFactory::createNvr("Hikvision");

    if (nvr) {
        nvr->connect();
        nvr->preview();
        delete nvr; // 注意释放资源
    }
    return 0;
}
```
## 二、工厂方法模式的概念
&emsp;&emsp;工厂模式是一种创建型设计模式，主要解决对象创建的问题。它的核心思想是：把对象的创建和使用分离，让使用者不直接依赖具体类，而是通过工厂来获得对象。

**区别于普通工厂方法模式：**
>  - 简单工厂模式：一个工厂类负责所有产品的创建 → 新增产品时需要修改工厂类（不符合开闭原则）。
>  - 工厂方法模式：每个产品对应一个工厂类 → 新增产品时只需增加新的工厂类，无需修改已有工厂。

**工厂方法模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/774bc112dcfd442a97aab12de3b9d3d9.png)
该模式对对象创建管理方式最为简单，因为其仅仅简单的对不同类对象的创建进行了一层薄薄的封装。该模式通过向工厂传递类型来指定要创建的对象，其UML类图如下：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7c434597274441bca20b93e9934f952a.png)
示例代码：
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 1. 抽象产品
class Product {
public:
    virtual void operation() = 0;
    virtual ~Product() = default;
};

// 2. 具体产品 A
class ProductA : public Product {
public:
    void operation() override {
        cout << "Product A operation" << endl;
    }
};

// 3. 具体产品 B
class ProductB : public Product {
public:
    void operation() override {
        cout << "Product B operation" << endl;
    }
};

// 4. 抽象工厂
class Factory {
public:
    virtual unique_ptr<Product> createProduct() = 0;
    virtual ~Factory() = default;
};

// 5. 具体工厂 A
class FactoryA : public Factory {
public:
    unique_ptr<Product> createProduct() override {
        return make_unique<ProductA>();
    }
};

// 6. 具体工厂 B
class FactoryB : public Factory {
public:
    unique_ptr<Product> createProduct() override {
        return make_unique<ProductB>();
    }
};

// 7. 客户端
int main() {
    unique_ptr<Factory> factoryA = make_unique<FactoryA>();
    auto productA = factoryA->createProduct();
    productA->operation();

    unique_ptr<Factory> factoryB = make_unique<FactoryB>();
    auto productB = factoryB->createProduct();
    productB->operation();

    return 0;
}
```

**优点：**
>  1. 符合 开闭原则：新增产品时，只需新增具体产品类和对应工厂类，不改动已有代码。
>  2. 将对象创建逻辑下放到具体工厂，解耦了“使用者”和“产品创建者”。
>  3. 客户端只依赖抽象接口，增强可扩展性。

**缺点：**
>  - 每新增一个产品，就需要新增一个工厂类，类数量增加，结构更复杂。
>  - 如果产品种类非常多，可能会带来额外的维护成本。

**适用场景：**
>  - 系统需要满足 开闭原则，频繁扩展产品。
>  - 不同的产品创建过程可能有一定差异，不能集中放在一个简单工厂里。

例如：
>  - 不同品牌数据库连接（MySQLFactory、SQLiteFactory…）。
>  - 不同类型日志记录器（FileLoggerFactory、ConsoleLoggerFactory…）。
>  - 不同 NVR 接入厂商（DahuaFactory、HikvisionFactory、TplinkFactory…）。

**工厂方法模式的具体实例**
相比普通工厂方法模式，工厂方法模式的关键点是：
>  - 不再由一个工厂类统一生产所有产品；
>  - 而是每个产品有一个对应的工厂类；
>  - 这样当你新增一个 NVR 厂商时，不需要修改已有工厂，只需新增对应的工厂类，符合开闭原则。

实现步骤：
**1. 定义抽象产品接口**
```cpp
// NVR 抽象接口
class INvr {
public:
    virtual ~INvr() {}
    virtual void connect() = 0;
    virtual void preview() = 0;
};
```
**2. 定义具体产品**
```cpp
#include <QDebug>

// 大华NVR
class DahuaNvr : public INvr {
public:
    void connect() override { qDebug() << "连接大华 NVR 成功"; }
    void preview() override { qDebug() << "大华 NVR 正在实时预览"; }
};

// 海康NVR
class HikvisionNvr : public INvr {
public:
    void connect() override { qDebug() << "连接海康 NVR 成功"; }
    void preview() override { qDebug() << "海康 NVR 正在实时预览"; }
};

// TP-LINK NVR
class TpLinkNvr : public INvr {
public:
    void connect() override { qDebug() << "连接 TP-LINK NVR 成功"; }
    void preview() override { qDebug() << "TP-LINK NVR 正在实时预览"; }
};
```

**3. 定义工厂接口**
```cpp
// 工厂接口
class INvrFactory {
public:
    virtual ~INvrFactory() {}
    virtual INvr* createNvr() = 0;
};
```

**4. 定义具体工厂**
```cpp
// 大华工厂
class DahuaFactory : public INvrFactory {
public:
    INvr* createNvr() override {
        return new DahuaNvr();
    }
};

// 海康工厂
class HikvisionFactory : public INvrFactory {
public:
    INvr* createNvr() override {
        return new HikvisionNvr();
    }
};

// TP-LINK 工厂
class TpLinkFactory : public INvrFactory {
public:
    INvr* createNvr() override {
        return new TpLinkNvr();
    }
};
```

**5. 客户端使用**
```cpp
int main() {
    // 客户端只依赖工厂接口，不依赖具体NVR实现
    INvrFactory* factory = new HikvisionFactory();
    INvr* nvr = factory->createNvr();

    nvr->connect();
    nvr->preview();

    delete nvr;
    delete factory;

    return 0;
}
```





