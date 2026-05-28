@[toc]
## 一、概念
&emsp;&emsp;建造者模式(亦称：生成器模式)是一种创建型设计模式， 使你能够分步骤创建复杂对象。 该模式允许你使用相同的创建代码生成不同类型和形式的对象。 将一个复杂对象的构建过程与它的表示 分离，使得同样的构建过程可以创建不同的表示。

>  - 意图：处理复杂对象的创建问题，尤其是需要分步骤组装的对象。
>  - 核心思想：把复杂对象的创建过程抽象出来，使用不同的“建造者”来构造不同的表示。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e9c3c8c27779412fa1727b30fb3dcf3c.png)
**使用场景：**
>  - 对象构建复杂：对象有很多组成部分，且构建步骤固定，但具体实现不同。
>  - 同样的构建过程，不同的表示：比如：
>  		- 游戏中创建不同风格的角色（武士、法师）。
> 	 	- 生成不同格式的报表（PDF、HTML、Excel）。
> 	 - 组装汽车（跑车、越野车）。

**总结：**
>  - 建造者模式更适合“复杂对象的分步骤组装”。
>  - 相比工厂模式，建造者模式更关注“组装过程”。


**建造者模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f82648cde14345a2a682827c45cdcbf8.png)
> - Product（产品类）：最终要创建的复杂对象；由多个部件组成。
>  - Builder（抽象建造者）：规定创建产品的抽象步骤（通常有 buildPartA()、buildPartB() 等）。不涉及具体部件的实现。
>  - ConcreteBuilder（具体建造者）：实现 Builder 的接口，具体完成产品部件的创建。最终返回一个完整的 Product。
>  - Director（指挥者）：调用建造者的步骤方法，控制构建过程。不关心具体的产品细节，只负责组装。

**UML类图：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/16ebd9fa685f4a49bba5eb38439009e7.png)
说明：
> - Product（抽象产品）：定义了产品的公共接口。
>  - ConcreteProductA / ConcreteProductB（具体产品）：实现 Product 接口，不同的产品有不同的行为。
>  - Factory（工厂类）：提供一个静态方法 createProduct(type)，根据参数决定创建哪一种 Product 实例。

## 二、实例分析
**问题：**
假设有这样一个复杂对象， 在对其进行构造时需要对诸多成员变量和嵌套对象进行繁复的初始化工作。 这些初始化代码通常深藏于一个包含众多参数且让人基本看不懂的构造函数中； 甚至还有更糟糕的情况， 那就是这些代码散落在客户端代码的多个位置。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a96638346c6a42feae0b72a2ee6dc9a7.png)
例如， 我们来思考如何创建一个 房屋House对象。 建造一栋简单的房屋， 首先你需要建造四面墙和地板， 安装房门和一套窗户， 然后再建造一个屋顶。 但是如果你想要一栋更宽敞更明亮的房屋， 还要有院子和其他设施 （例如暖气、 排水和供电设备）， 那又该怎么办呢？

最简单的方法是扩展 房屋基类， 然后创建一系列涵盖所有参数组合的子类。 但最终你将面对相当数量的子类。 任何新增的参数 （例如门廊类型） 都会让这个层次结构更加复杂。

另一种方法则无需生成子类。 你可以在 房屋基类中创建一个包括所有可能参数的超级构造函数， 并用它来控制房屋对象。 这种方法确实可以避免生成子类， 但它却会造成另外一个问题。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bd16cfac7b354e86844e30aab5e40967.png)
通常情况下， 绝大部分的参数都没有使用， 这使得对于构造函数的调用十分不简洁。 例如， 只有很少的房子有游泳池， 因此与游泳池相关的参数十之八九是毫无用处的。

**解决方案：**
生成器模式建议将对象构造代码从产品类中抽取出来， 并将其放在一个名为生成器的独立对象中。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fb1aaee832074539a6ff5029f9b448e2.png)
该模式会将对象构造过程划分为一组步骤， 比如 build­Walls创建墙壁和 build­Door创建房门创建房门等。 每次创建对象时， 你都需要通过生成器对象执行一系列步骤。 重点在于你无需调用所有步骤， 而只需调用创建特定对象配置所需的那些步骤即可。

当你需要创建不同形式的产品时， 其中的一些构造步骤可能需要不同的实现。 例如， 木屋的房门可能需要使用木头制造， 而城堡的房门则必须使用石头制造。

在这种情况下， 你可以创建多个不同的生成器， 用不同方式实现一组相同的创建步骤。 然后你就可以在创建过程中使用这些生成器 （例如按顺序调用多个构造步骤） 来生成不同类型的对象。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/75bc020ec22e4057ab6d1377395a40d9.png)
例如， 假设第一个建造者使用木头和玻璃制造房屋， 第二个建造者使用石头和钢铁， 而第三个建造者使用黄金和钻石。 在调用同一组步骤后， 第一个建造者会给你一栋普通房屋， 第二个会给你一座小城堡， 而第三个则会给你一座宫殿。 但是， 只有在调用构造步骤的客户端代码可以通过通用接口与建造者进行交互时， 这样的调用才能返回需要的房屋。

**示例代码：**
**产品类：House**
```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

class House {
public:
    vector<string> parts;

    void addPart(const string& part) {
        parts.push_back(part);
    }

    void show() const {
        cout << "房子建造完成，包含: ";
        for (auto& p : parts) {
            cout << p << " ";
        }
        cout << endl;
    }
};
```

**抽象建造者接口**
```cpp
class HouseBuilder {
public:
    virtual void buildWalls() = 0;
    virtual void buildDoors() = 0;
    virtual void buildWindows() = 0;
    virtual void buildRoof() = 0;
    virtual void buildGarage() = 0;
    virtual House* getResult() = 0;

    virtual ~HouseBuilder() = default;
};
```

**具体建造者**
```cpp
class SimpleHouseBuilder : public HouseBuilder {
private:
    House* house;
public:
    SimpleHouseBuilder() { house = new House(); }

    void buildWalls() override { house->addPart("简单的墙"); }
    void buildDoors() override { house->addPart("简单的门"); }
    void buildWindows() override { house->addPart("简单的窗户"); }
    void buildRoof() override { house->addPart("简单的屋顶"); }
    void buildGarage() override { house->addPart("无车库"); }

    House* getResult() override { return house; }
};

class LuxuryHouseBuilder : public HouseBuilder {
private:
    House* house;
public:
    LuxuryHouseBuilder() { house = new House(); }

    void buildWalls() override { house->addPart("大理石墙"); }
    void buildDoors() override { house->addPart("红木门"); }
    void buildWindows() override { house->addPart("落地窗"); }
    void buildRoof() override { house->addPart("豪华屋顶"); }
    void buildGarage() override { house->addPart("双车位车库"); }

    House* getResult() override { return house; }
};
```

**指挥者（Director）**
指挥者的作用：按照一定的步骤组织建造者来建造产品。
```cpp
class Director {
public:
    void construct(HouseBuilder* builder) {
        builder->buildWalls();
        builder->buildDoors();
        builder->buildWindows();
        builder->buildRoof();
        builder->buildGarage();
    }
};
```

**客户端使用**
```cpp
int main() {
    Director director;

    // 1. 建造一个简易房子
    HouseBuilder* simpleBuilder = new SimpleHouseBuilder();
    director.construct(simpleBuilder);
    House* simpleHouse = simpleBuilder->getResult();
    simpleHouse->show();

    // 2. 建造一个豪华房子
    HouseBuilder* luxuryBuilder = new LuxuryHouseBuilder();
    director.construct(luxuryBuilder);
    House* luxuryHouse = luxuryBuilder->getResult();
    luxuryHouse->show();

    delete simpleBuilder;
    delete luxuryBuilder;
    delete simpleHouse;
    delete luxuryHouse;

    return 0;
}
```
## 三、示例代码
**3.1基本示例**
```cpp
#include <iostream>
#include <string>
using namespace std;

// 产品类
class Product {
public:
    void setPartA(const string& part) { partA = part; }
    void setPartB(const string& part) { partB = part; }
    void show() {
        cout << "Product parts: " << partA << ", " << partB << endl;
    }
private:
    string partA;
    string partB;
};

// 抽象建造者
class Builder {
public:
    virtual ~Builder() {}
    virtual void buildPartA() = 0;
    virtual void buildPartB() = 0;
    virtual Product* getResult() = 0;
};

// 具体建造者
class ConcreteBuilder : public Builder {
public:
    ConcreteBuilder() { product = new Product(); }
    void buildPartA() override { product->setPartA("Part A"); }
    void buildPartB() override { product->setPartB("Part B"); }
    Product* getResult() override { return product; }
private:
    Product* product;
};

// 指挥者
class Director {
public:
    Director(Builder* b) : builder(b) {}
    Product* construct() {
        builder->buildPartA();
        builder->buildPartB();
        return builder->getResult();
    }
private:
    Builder* builder;
};

// 使用示例
int main() {
    Builder* builder = new ConcreteBuilder();
    Director director(builder);

    Product* product = director.construct();
    product->show();

    delete product;
    delete builder;
    return 0;
}
```

**3.2 构建一个复杂的 QWidget 界面（包含标题、按钮、输入框等）**
需求场景：
>  - 我们要创建一个 不同风格的界面（比如 登录界面、注册界面）。
>  - 构建过程是固定的：添加标题 → 添加输入框 → 添加按钮。
>  - 使用建造者模式来灵活组装。

示例代码：
```cpp
#include <QApplication>
#include <QWidget>
#include <QVBoxLayout>
#include <QLabel>
#include <QLineEdit>
#include <QPushButton>

// ================= Product =================
class ComplexWidget : public QWidget {
public:
    ComplexWidget(QWidget *parent = nullptr) : QWidget(parent) {
        layout = new QVBoxLayout(this);
        setLayout(layout);
    }

    void addTitle(const QString &text) {
        QLabel *label = new QLabel(text, this);
        label->setAlignment(Qt::AlignCenter);
        layout->addWidget(label);
    }

    void addInput(const QString &placeholder) {
        QLineEdit *edit = new QLineEdit(this);
        edit->setPlaceholderText(placeholder);
        layout->addWidget(edit);
    }

    void addButton(const QString &text) {
        QPushButton *btn = new QPushButton(text, this);
        layout->addWidget(btn);
    }

private:
    QVBoxLayout *layout;
};

// ================= Builder =================
class Builder {
public:
    virtual ~Builder() {}
    virtual void buildTitle() = 0;
    virtual void buildInputs() = 0;
    virtual void buildButtons() = 0;
    virtual ComplexWidget* getResult() = 0;
};

// ================= ConcreteBuilder: 登录界面 =================
class LoginBuilder : public Builder {
public:
    LoginBuilder() { widget = new ComplexWidget(); }
    void buildTitle() override {
        widget->addTitle("用户登录");
    }
    void buildInputs() override {
        widget->addInput("用户名");
        widget->addInput("密码");
    }
    void buildButtons() override {
        widget->addButton("登录");
        widget->addButton("取消");
    }
    ComplexWidget* getResult() override { return widget; }
private:
    ComplexWidget *widget;
};

// ================= ConcreteBuilder: 注册界面 =================
class RegisterBuilder : public Builder {
public:
    RegisterBuilder() { widget = new ComplexWidget(); }
    void buildTitle() override {
        widget->addTitle("用户注册");
    }
    void buildInputs() override {
        widget->addInput("用户名");
        widget->addInput("邮箱");
        widget->addInput("密码");
        widget->addInput("确认密码");
    }
    void buildButtons() override {
        widget->addButton("注册");
        widget->addButton("返回");
    }
    ComplexWidget* getResult() override { return widget; }
private:
    ComplexWidget *widget;
};

// ================= Director =================
class Director {
public:
    Director(Builder *b) : builder(b) {}
    ComplexWidget* construct() {
        builder->buildTitle();
        builder->buildInputs();
        builder->buildButtons();
        return builder->getResult();
    }
private:
    Builder *builder;
};

// ================= main =================
int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    // 选择不同的 Builder
    Builder *builder = nullptr;

    // 登录界面
    builder = new LoginBuilder();
    Director directorLogin(builder);
    ComplexWidget *loginWidget = directorLogin.construct();
    loginWidget->setWindowTitle("建造者模式 - 登录界面");
    loginWidget->show();
    delete builder;

    // 注册界面
    builder = new RegisterBuilder();
    Director directorRegister(builder);
    ComplexWidget *registerWidget = directorRegister.construct();
    registerWidget->setWindowTitle("建造者模式 - 注册界面");
    registerWidget->move(400, 100);
    registerWidget->show();
    delete builder;

    return app.exec();
}

```
**3.2.日常类比示例**
示例场景：
>  - 一辆汽车由发动机、车轮、车身 三部分组成。
>  - 我们可以用同样的构建步骤，来造不同类型的车（比如跑车、SUV）。
>  - 使用 建造者模式 来封装“组装过程”。

```cpp
#include <iostream>
#include <string>
using namespace std;

// ================= Product: 汽车 =================
class Car {
public:
    void setEngine(const string &e) { engine = e; }
    void setWheels(const string &w) { wheels = w; }
    void setBody(const string &b) { body = b; }

    void show() {
        cout << "这是一辆车: "
             << body << ", " << engine << ", " << wheels << endl;
    }
private:
    string engine;
    string wheels;
    string body;
};

// ================= Builder 抽象建造者 =================
class Builder {
public:
    virtual ~Builder() {}
    virtual void buildEngine() = 0;
    virtual void buildWheels() = 0;
    virtual void buildBody() = 0;
    virtual Car* getResult() = 0;
};

// ================= 具体建造者: 跑车 =================
class SportsCarBuilder : public Builder {
public:
    SportsCarBuilder() { car = new Car(); }
    void buildEngine() override { car->setEngine("V8 发动机"); }
    void buildWheels() override { car->setWheels("18寸跑车轮胎"); }
    void buildBody() override { car->setBody("流线型跑车车身"); }
    Car* getResult() override { return car; }
private:
    Car *car;
};

// ================= 具体建造者: SUV =================
class SUVBuilder : public Builder {
public:
    SUVBuilder() { car = new Car(); }
    void buildEngine() override { car->setEngine("V6 涡轮发动机"); }
    void buildWheels() override { car->setWheels("20寸越野轮胎"); }
    void buildBody() override { car->setBody("高底盘SUV车身"); }
    Car* getResult() override { return car; }
private:
    Car *car;
};

// ================= Director 指挥者 =================
class Director {
public:
    Director(Builder *b) : builder(b) {}
    Car* construct() {
        builder->buildEngine();
        builder->buildWheels();
        builder->buildBody();
        return builder->getResult();
    }
private:
    Builder *builder;
};

// ================= 使用示例 =================
int main() {
    // 生产跑车
    Builder *sportsBuilder = new SportsCarBuilder();
    Director director1(sportsBuilder);
    Car *sportsCar = director1.construct();
    sportsCar->show();

    // 生产SUV
    Builder *suvBuilder = new SUVBuilder();
    Director director2(suvBuilder);
    Car *suvCar = director2.construct();
    suvCar->show();

    delete sportsCar;
    delete sportsBuilder;
    delete suvCar;
    delete suvBuilder;
    return 0;
}
```
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/35542e61306c454c996b3c851f9b345d.png)

