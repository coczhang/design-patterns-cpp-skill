@[toc]
## 一、外观模式的介绍
&emsp;&emsp;外观模式（Facade Pattern） 是一种结构型设计模式。它的主要思想是：为复杂子系统提供一个统一的对外接口，简化外部对系统的访问。

这样做可以：  
>  - 隐藏子系统的复杂性，外部只需要通过一个简单接口调用即可。
>  - 降低耦合度，客户端与子系统之间不直接依赖，而是通过外观类解耦。
>  - 提高可维护性，如果子系统内部有变化，只需要在外观类中调整，而不用改客户端代码。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/1e3069a2581d4e108fe798977294e51c.png)

**外观模式的角色：**
>  - 外观类（Facade）：对外提供统一接口，封装子系统的调用逻辑。
>  - 子系统类（Subsystem）：实现实际功能，可能有很多类、很多方法。
>  - 客户端（Client）：只与外观类交互，不直接访问子系统。

**外观模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ed50def9aa54410db05f1f9c367b7b86.png)
## 二、实例分析
当你通过电话给商店下达订单时， 接线员就是该商店的所有服务和部门的外观。 接线员为你提供了一个同购物系统、 支付网关和各种送货服务进行互动的简单语音接口。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/96f4574e2ed342b09f3d88251b75726f.png)
上图中图展示了一个典型的 外观模式（Facade Pattern） 应用场景：
>  - 客户（Client） → 打电话下订单
>  - 接线员（Facade 外观类） → 统一对外接口
>  - 内部子系统 → 仓库、支付处理、供应商、包装、送货、纳税

示例代码：
```cpp
#include <iostream>
using namespace std;

// 子系统类：仓库
class Warehouse {
public:
    void checkInventory() {
        cout << "仓库：检查库存\n";
    }
};

// 子系统类：支付处理
class PaymentProcessor {
public:
    void processPayment() {
        cout << "支付处理：完成支付\n";
    }
};

// 子系统类：供应商
class Supplier {
public:
    void orderFromSupplier() {
        cout << "供应商：下单补货\n";
    }
};

// 子系统类：包装
class Packaging {
public:
    void packItem() {
        cout << "包装：商品已打包\n";
    }
};

// 子系统类：送货
class Shipping {
public:
    void shipItem() {
        cout << "送货：商品已发货\n";
    }
};

// 子系统类：纳税
class Tax {
public:
    void payTax() {
        cout << "纳税：完成订单税款缴纳\n";
    }
};

// 外观类：接线员（对客户提供统一接口）
class ShopFacade {
private:
    Warehouse warehouse;
    PaymentProcessor payment;
    Supplier supplier;
    Packaging packaging;
    Shipping shipping;
    Tax tax;

public:
    void placeOrder() {
        cout << "接线员：为您处理订单...\n";
        warehouse.checkInventory();
        supplier.orderFromSupplier();
        payment.processPayment();
        tax.payTax();
        packaging.packItem();
        shipping.shipItem();
        cout << "接线员：您的订单已完成！\n";
    }
};

// 客户端
int main() {
    ShopFacade shop;
    cout << "客户：打电话下订单...\n";
    shop.placeOrder();
    return 0;
}
```
## 三、示例代码
比如我们有一个 家庭影院系统，包括：DVD 播放器、投影仪、音响等。用户只需要点击一个按钮，就能“看电影”，而不用单独操作每个设备。
```cpp
#include <iostream>
using namespace std;

// 子系统A：DVD播放器
class DVDPlayer {
public:
    void on() { cout << "DVD播放器打开\n"; }
    void play() { cout << "DVD开始播放\n"; }
    void off() { cout << "DVD播放器关闭\n"; }
};

// 子系统B：投影仪
class Projector {
public:
    void on() { cout << "投影仪打开\n"; }
    void off() { cout << "投影仪关闭\n"; }
};

// 子系统C：音响
class Stereo {
public:
    void on() { cout << "音响打开\n"; }
    void setVolume(int v) { cout << "音量调到 " << v << endl; }
    void off() { cout << "音响关闭\n"; }
};

// 外观类：家庭影院
class HomeTheaterFacade {
private:
    DVDPlayer* dvd;
    Projector* projector;
    Stereo* stereo;
public:
    HomeTheaterFacade(DVDPlayer* d, Projector* p, Stereo* s)
        : dvd(d), projector(p), stereo(s) {}

    void watchMovie() {
        cout << "准备看电影...\n";
        dvd->on();
        projector->on();
        stereo->on();
        stereo->setVolume(10);
        dvd->play();
    }

    void endMovie() {
        cout << "电影结束，关闭设备...\n";
        dvd->off();
        projector->off();
        stereo->off();
    }
};

// 客户端
int main() {
    DVDPlayer dvd;
    Projector projector;
    Stereo stereo;

    HomeTheaterFacade homeTheater(&dvd, &projector, &stereo);
    homeTheater.watchMovie();
    cout << "...... 正在看电影 ......\n";
    homeTheater.endMovie();

    return 0;
}
```
