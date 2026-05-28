@[toc]
## 一、策略模式的介绍
&emsp;&emsp;策略模式是一种行为型设计模式，它定义了一系列算法，把它们一个个封装起来，并且使它们可以相互替换。这样，算法的变化不会影响到使用算法的客户。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/51d31e604f27419bb84d8c517b2e52b0.png)
通俗理解：
>  - 有很多解决问题的“方法”（策略），比如支付方式：支付宝、微信、银行卡。
>  - 你可以在运行时灵活选择某一个策略，而不需要在代码里写很多 if-else 来判断。

**角色组成：**
>  - Context（环境类）：持有一个 Strategy 对象的引用，最终由它来调用具体的算法。
>  - Strategy（抽象策略类/接口）：定义所有支持的算法的公共接口。
>  - ConcreteStrategy（具体策略类）：实现具体的算法。

**策略模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b22ee1c5a562435793d8886de7bfb6c9.png)

## 二、实例分析
**问题：**
一天， 你打算为游客们创建一款导游程序。 该程序的核心功能是提供美观的地图， 以帮助用户在任何城市中快速定位。

用户期待的程序新功能是自动路线规划： 他们希望输入地址后就能在地图上看到前往目的地的最快路线。

程序的首个版本只能规划公路路线。 驾车旅行的人们对此非常满意。 但很显然， 并非所有人都会在度假时开车。 因此你在下次更新时添加了规划步行路线的功能。 此后， 你又添加了规划公共交通路线的功能。

而这只是个开始。 不久后， 你又要为骑行者规划路线。 又过了一段时间， 你又要为游览城市中的所有景点规划路线。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/525a6a938c27472982c0524ebf83ac8a.png)
尽管从商业角度来看， 这款应用非常成功， 但其技术部分却让你非常头疼： 每次添加新的路线规划算法后， 导游应用中主要类的体积就会增加一倍。 终于在某个时候， 你觉得自己没法继续维护这堆代码了。

无论是修复简单缺陷还是微调街道权重， 对某个算法进行任何修改都会影响整个类， 从而增加在已有正常运行代码中引入错误的风险。

此外， 团队合作将变得低效。 如果你在应用成功发布后招募了团队成员， 他们会抱怨在合并冲突的工作上花费了太多时间。 在实现新功能的过程中， 你的团队需要修改同一个巨大的类， 这样他们所编写的代码相互之间就可能会出现冲突。

**解决方案：**
策略模式建议找出负责用许多不同方式完成特定任务的类， 然后将其中的算法抽取到一组被称为策略的独立类中。

名为上下文的原始类必须包含一个成员变量来存储对于每种策略的引用。 上下文并不执行任务， 而是将工作委派给已连接的策略对象。

上下文不负责选择符合任务需要的算法——客户端会将所需策略传递给上下文。 实际上， 上下文并不十分了解策略， 它会通过同样的通用接口与所有策略进行交互， 而该接口只需暴露一个方法来触发所选策略中封装的算法即可。

因此， 上下文可独立于具体策略。 这样你就可在不修改上下文代码或其他策略的情况下添加新算法或修改已有算法了。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9b41c54f039749499c6ce1c561aa41e9.png)
在导游应用中， 每个路线规划算法都可被抽取到只有一个 build­Route生成路线方法的独立类中。 该方法接收起点和终点作为参数， 并返回路线中途点的集合。

即使传递给每个路径规划类的参数一模一样， 其所创建的路线也可能完全不同。 主要导游类的主要工作是在地图上渲染一系列中途点， 不会在意如何选择算法。 该类中还有一个用于切换当前路径规划策略的方法， 因此客户端 （例如用户界面中的按钮） 可用其他策略替换当前选择的路径规划行为。
## 三、示例代码
**示例一：支付方式**

```cpp
#include <iostream>
#include <memory>
using namespace std;

// 抽象策略类
class PaymentStrategy {
public:
    virtual ~PaymentStrategy() {}
    virtual void pay(int amount) = 0;
};

// 具体策略类 - 支付宝
class Alipay : public PaymentStrategy {
public:
    void pay(int amount) override {
        cout << "使用支付宝支付 " << amount << " 元" << endl;
    }
};

// 具体策略类 - 微信
class WeChatPay : public PaymentStrategy {
public:
    void pay(int amount) override {
        cout << "使用微信支付 " << amount << " 元" << endl;
    }
};

// 具体策略类 - 银行卡
class BankCardPay : public PaymentStrategy {
public:
    void pay(int amount) override {
        cout << "使用银行卡支付 " << amount << " 元" << endl;
    }
};

// 环境类
class PaymentContext {
private:
    unique_ptr<PaymentStrategy> strategy;
public:
    PaymentContext(PaymentStrategy* s) : strategy(s) {}
    void setStrategy(PaymentStrategy* s) { strategy.reset(s); }
    void pay(int amount) {
        strategy->pay(amount);
    }
};

// 测试
int main() {
    PaymentContext ctx(new Alipay());
    ctx.pay(100);   // 支付宝支付

    ctx.setStrategy(new WeChatPay());
    ctx.pay(200);   // 微信支付

    ctx.setStrategy(new BankCardPay());
    ctx.pay(300);   // 银行卡支付

    return 0;
}
```

**示例二：**
需求：旅游导航应用需要支持多种“路线规划”方式，比如：
>  - 开车路线
>  - 步行路线
>  - 骑行路线
>  - 公交路线
>  - 景点游览路线
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 路线规划策略接口
class RouteStrategy {
public:
    virtual void buildRoute(const string& start, const string& end) = 0;
    virtual ~RouteStrategy() = default;
};

// 具体策略：开车路线
class CarRoute : public RouteStrategy {
public:
    void buildRoute(const string& start, const string& end) override {
        cout << "规划汽车路线: " << start << " -> " << end << endl;
    }
};

// 具体策略：步行路线
class WalkRoute : public RouteStrategy {
public:
    void buildRoute(const string& start, const string& end) override {
        cout << "规划步行路线: " << start << " -> " << end << endl;
    }
};

// 具体策略：公交路线
class BusRoute : public RouteStrategy {
public:
    void buildRoute(const string& start, const string& end) override {
        cout << "规划公交路线: " << start << " -> " << end << endl;
    }
};

// 具体策略：骑行路线
class BikeRoute : public RouteStrategy {
public:
    void buildRoute(const string& start, const string& end) override {
        cout << "规划骑行路线: " << start << " -> " << end << endl;
    }
};

// 上下文类：导航
class Navigator {
private:
    unique_ptr<RouteStrategy> strategy; // 使用智能指针管理策略
public:
    void setStrategy(RouteStrategy* s) {
        strategy.reset(s); // 替换当前策略
    }

    void buildRoute(const string& start, const string& end) {
        if (strategy) {
            strategy->buildRoute(start, end);
        } else {
            cout << "未设置路线规划策略！" << endl;
        }
    }
};

// ================== 测试 ==================
int main() {
    Navigator nav;

    // 开车导航
    nav.setStrategy(new CarRoute());
    nav.buildRoute("上海", "杭州");

    // 步行导航
    nav.setStrategy(new WalkRoute());
    nav.buildRoute("北京", "故宫");

    // 公交导航
    nav.setStrategy(new BusRoute());
    nav.buildRoute("深圳", "广州");

    // 骑行导航
    nav.setStrategy(new BikeRoute());
    nav.buildRoute("成都", "都江堰");

    return 0;
}
```
