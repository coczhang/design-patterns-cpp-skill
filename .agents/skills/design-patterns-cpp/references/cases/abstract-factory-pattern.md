@[toc]
## 一、概念
&emsp;&emsp;抽象工厂模式是一种创建型设计模式。 提供一个接口用于创建一系列相关或相互依赖的对象，而无需指定它们的具体类。
相比于工厂方法模式，抽象工厂模式不仅仅是创建单一产品，而是一族产品（产品族）。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/686b0191aed946d5810ce35f53e22836.png)
抽象工厂模式结构：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0e706990f1e64d2aabbdab583c81a9cf.png)

## 二、实例分析
**问题：**
假设你正在开发一款家具商店模拟器。 你的代码中包括一些类， 用于表示：
>  1. 一系列相关产品， 例如椅子Chair 、 ​ 沙发Sofa和咖啡桌Coffee­Table 。
>  2. 系列产品的不同变体。 例如， 你可以使用现代Modern 、 ​ 维多利亚Victorian 、 ​ 装饰风艺术Art­Deco等风格生成 椅子 、沙发和 咖啡桌 。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/1c5b4394fe65455babcbf79d98da3ff5.png)
你需要设法单独生成每件家具对象， 这样才能确保其风格一致。 如果顾客收到的家具风格不一样， 他们可不会开心。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f172c1c99427428b845e94f36c1113a1.png)
此外， 你也不希望在添加新产品或新风格时修改已有代码。 家具供应商对于产品目录的更新非常频繁， 你不会想在每次更新时都去修改核心代码的。

**解决方案：**
首先， 抽象工厂模式建议为系列中的每件产品明确声明接口 （例如椅子、 沙发或咖啡桌）。 然后， 确保所有产品变体都继承这些接口。 例如， 所有风格的椅子都实现椅子接口； 所有风格的咖啡桌都实现咖啡桌接口， 以此类推。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f40c85cac73d4747a5651231ee36e16f.png)
接下来， 我们需要声明抽象工厂——包含系列中所有产品构造方法的接口。 例如 create­Chair创建椅子 、 ​ create­Sofa创建沙发和 create­Coffee­Table创建咖啡桌 。 这些方法必须返回抽象产品类型， 即我们之前抽取的那些接口： ​ 椅子 ， ​ 沙发和 咖啡桌等等。

那么该如何处理产品变体呢？ 对于系列产品的每个变体， 我们都将基于 抽象工厂接口创建不同的工厂类。 每个工厂类都只能返回特定类别的产品， 例如， ​ 现代家具工厂Modern­Furniture­Factory只能创建 现代椅子Modern­Chair 、 ​ 现代沙发Modern­Sofa和 现代咖啡桌Modern­Coffee­Table对象。

客户端代码可以通过相应的抽象接口调用工厂和产品类。 你无需修改实际客户端代码， 就能更改传递给客户端的工厂类， 也能更改客户端代码接收的产品变体。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/532d604b0df546d6953ceeaa00ada8b7.png)
设客户端想要工厂创建一把椅子。 客户端无需了解工厂类， 也不用管工厂类创建出的椅子类型。 无论是现代风格， 还是维多利亚风格的椅子， 对于客户端来说没有分别， 它只需调用抽象 椅子接口就可以了。 这样一来， 客户端只需知道椅子以某种方式实现了 sit­On坐下方法就足够了。 此外， 无论工厂返回的是何种椅子变体， 它都会和由同一工厂对象创建的沙发或咖啡桌风格一致。

最后一点说明： 如果客户端仅接触抽象接口， 那么谁来创建实际的工厂对象呢？ 一般情况下， 应用程序会在初始化阶段创建具体工厂对象。 而在此之前， 应用程序必须根据配置文件或环境设定选择工厂类别。

**代码示例：**
**抽象产品接口**
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 椅子
class Chair {
public:
    virtual void sitOn() = 0;
    virtual ~Chair() = default;
};

// 沙发
class Sofa {
public:
    virtual void lieOn() = 0;
    virtual ~Sofa() = default;
};

// 咖啡桌
class CoffeeTable {
public:
    virtual void putCoffee() = 0;
    virtual ~CoffeeTable() = default;
};
```

**具体产品实现**
```cpp
// ---------- Art Deco 风格 ----------
class ArtDecoChair : public Chair {
public:
    void sitOn() override { cout << "坐在 Art Deco 椅子上\n"; }
};

class ArtDecoSofa : public Sofa {
public:
    void lieOn() override { cout << "躺在 Art Deco 沙发上\n"; }
};

class ArtDecoCoffeeTable : public CoffeeTable {
public:
    void putCoffee() override { cout << "在 Art Deco 咖啡桌上放咖啡\n"; }
};

// ---------- Victorian 风格 ----------
class VictorianChair : public Chair {
public:
    void sitOn() override { cout << "坐在 Victorian 椅子上\n"; }
};

class VictorianSofa : public Sofa {
public:
    void lieOn() override { cout << "躺在 Victorian 沙发上\n"; }
};

class VictorianCoffeeTable : public CoffeeTable {
public:
    void putCoffee() override { cout << "在 Victorian 咖啡桌上放咖啡\n"; }
};

// ---------- Modern 风格 ----------
class ModernChair : public Chair {
public:
    void sitOn() override { cout << "坐在 Modern 椅子上\n"; }
};

class ModernSofa : public Sofa {
public:
    void lieOn() override { cout << "躺在 Modern 沙发上\n"; }
};

class ModernCoffeeTable : public CoffeeTable {
public:
    void putCoffee() override { cout << "在 Modern 咖啡桌上放咖啡\n"; }
};
```

**抽象工厂接口**
```cpp
class FurnitureFactory {
public:
    virtual Chair* createChair() = 0;
    virtual Sofa* createSofa() = 0;
    virtual CoffeeTable* createCoffeeTable() = 0;
    virtual ~FurnitureFactory() = default;
};
```

**具体工厂实现**
```cpp
class ArtDecoFactory : public FurnitureFactory {
public:
    Chair* createChair() override { return new ArtDecoChair(); }
    Sofa* createSofa() override { return new ArtDecoSofa(); }
    CoffeeTable* createCoffeeTable() override { return new ArtDecoCoffeeTable(); }
};

class VictorianFactory : public FurnitureFactory {
public:
    Chair* createChair() override { return new VictorianChair(); }
    Sofa* createSofa() override { return new VictorianSofa(); }
    CoffeeTable* createCoffeeTable() override { return new VictorianCoffeeTable(); }
};

class ModernFactory : public FurnitureFactory {
public:
    Chair* createChair() override { return new ModernChair(); }
    Sofa* createSofa() override { return new ModernSofa(); }
    CoffeeTable* createCoffeeTable() override { return new ModernCoffeeTable(); }
};
```

**客户端使用**
```cpp
void clientCode(FurnitureFactory* factory) {
    unique_ptr<Chair> chair(factory->createChair());
    unique_ptr<Sofa> sofa(factory->createSofa());
    unique_ptr<CoffeeTable> table(factory->createCoffeeTable());

    chair->sitOn();
    sofa->lieOn();
    table->putCoffee();
}

int main() {
    FurnitureFactory* factory = nullptr;

    cout << "=== 使用 Art Deco 风格 ===\n";
    factory = new ArtDecoFactory();
    clientCode(factory);
    delete factory;

    cout << "\n=== 使用 Victorian 风格 ===\n";
    factory = new VictorianFactory();
    clientCode(factory);
    delete factory;

    cout << "\n=== 使用 Modern 风格 ===\n";
    factory = new ModernFactory();
    clientCode(factory);
    delete factory;

    return 0;
}
```
## 三、完整示例
比如我们要实现一个跨平台的 UI 库，有两种风格：Windows 风格和 Mac 风格，每种风格都包含：
>  - 按钮（Button）
>  - 文本框（TextBox）

**抽象产品**
```cpp
// 抽象按钮
class Button {
public:
    virtual void paint() = 0;
    virtual ~Button() = default;
};

// 抽象文本框
class TextBox {
public:
    virtual void show() = 0;
    virtual ~TextBox() = default;
};
```

**具体产品**
```cpp
// Windows 按钮
class WinButton : public Button {
public:
    void paint() override { qDebug("Draw Windows Button"); }
};

// Windows 文本框
class WinTextBox : public TextBox {
public:
    void show() override { qDebug("Show Windows TextBox"); }
};

// Mac 按钮
class MacButton : public Button {
public:
    void paint() override { qDebug("Draw Mac Button"); }
};

// Mac 文本框
class MacTextBox : public TextBox {
public:
    void show() override { qDebug("Show Mac TextBox"); }
};
```

**抽象工厂**
```cpp
class GUIFactory {
public:
    virtual Button* createButton() = 0;
    virtual TextBox* createTextBox() = 0;
    virtual ~GUIFactory() = default;
};
```

**具体工厂**
```cpp
class WinFactory : public GUIFactory {
public:
    Button* createButton() override { return new WinButton(); }
    TextBox* createTextBox() override { return new WinTextBox(); }
};

class MacFactory : public GUIFactory {
public:
    Button* createButton() override { return new MacButton(); }
    TextBox* createTextBox() override { return new MacTextBox(); }
};
```

**客户端使用**
```cpp
void clientCode(GUIFactory* factory) {
    Button* btn = factory->createButton();
    TextBox* txt = factory->createTextBox();

    btn->paint();
    txt->show();

    delete btn;
    delete txt;
}

int main() {
    GUIFactory* factory = new WinFactory();
    clientCode(factory);
    delete factory;

    factory = new MacFactory();
    clientCode(factory);
    delete factory;

    return 0;
}
```
