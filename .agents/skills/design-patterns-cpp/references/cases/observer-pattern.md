@[toc]
## 一、定义
&emsp;&emsp;观察者模式是一种行为型设计模式，它定义了一种一对多的依赖关系，使得当一个对象（被观察者 / Subject）的状态发生变化时，所有依赖于它的对象（观察者 / Observer）都会自动得到通知并更新。通俗理解：就像你在订阅微信公众号（被观察者），一旦公众号发布新文章，它会自动通知所有订阅它的用户（观察者），用户就能收到推送。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/98edfed71fae402d87aa83dea04c1053.png)
**角色组成：**
> 1. Subject（被观察者/主题）：保存观察者列表；提供注册、移除观察者的接口；状态改变时通知所有观察者
>  2. Observer（观察者）：定义更新接口；接收来自被观察者的状态更新
>  3. ConcreteSubject（具体被观察者）：实现具体的状态维护和通知逻辑
>  4. ConcreteObserver（具体观察者）：实现收到通知后的响应逻辑

**观察者模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/46deaf6d241944f8991a7ad09f1e2b03.png)
## 二、实例分析
**问题：**
假如你有两种类型的对象： ​ 顾客和商店 。 顾客对某个特定品牌的产品非常感兴趣 （例如最新型号的 iPhone 手机）， 而该产品很快将会在商店里出售。

顾客可以每天来商店看看产品是否到货。 但如果商品尚未到货时， 绝大多数来到商店的顾客都会空手而归。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2deb771f3c964b789f17d9fbe8da1f5b.png)
另一方面， 每次新产品到货时， 商店可以向所有顾客发送邮件 （可能会被视为垃圾邮件）。 这样， 部分顾客就无需反复前往商店了， 但也可能会惹恼对新产品没有兴趣的其他顾客。

我们似乎遇到了一个矛盾： 要么让顾客浪费时间检查产品是否到货， 要么让商店浪费资源去通知没有需求的顾客。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/498af250f0a24dcea47ce626af5cd50f.png)
**解决方案：**
拥有一些值得关注的状态的对象通常被称为目标， 由于它要将自身的状态改变通知给其他对象， 我们也将其称为发布者 （publisher）。 所有希望关注发布者状态变化的其他对象被称为订阅者 （subscribers）。

观察者模式建议你为发布者类添加订阅机制， 让每个对象都能订阅或取消订阅发布者事件流。 不要害怕！ 这并不像听上去那么复杂。 实际上， 该机制包括 1） 一个用于存储订阅者对象引用的列表成员变量； 2） 几个用于添加或删除该列表中订阅者的公有方法。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/af95f506ba19478e838645a9ddcb154b.png)
现在， 无论何时发生了重要的发布者事件， 它都要遍历订阅者并调用其对象的特定通知方法。

实际应用中可能会有十几个不同的订阅者类跟踪着同一个发布者类的事件， 你不会希望发布者与所有这些类相耦合的。 此外如果他人会使用发布者类， 那么你甚至可能会对其中的一些类一无所知。

因此， 所有订阅者都必须实现同样的接口， 发布者仅通过该接口与订阅者交互。 接口中必须声明通知方法及其参数， 这样发布者在发出通知时还能传递一些上下文数据。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/75d1fbddd1a34bc9bf1120fc03d66745.png)
如果你的应用中有多个不同类型的发布者， 且希望订阅者可兼容所有发布者， 那么你甚至可以进一步让所有发布者遵循同样的接口。 该接口仅需描述几个订阅方法即可。 这样订阅者就能在不与具体发布者类耦合的情况下通过接口观察发布者的状态。
## 三、示例代码
**示例一：**
```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// 抽象观察者
class Observer {
public:
    virtual void update(const string& message) = 0;
    virtual ~Observer() = default;
};

// 被观察者（主题）
class Subject {
private:
    vector<Observer*> observers;
public:
    void attach(Observer* obs) {
        observers.push_back(obs);
    }
    void detach(Observer* obs) {
        observers.erase(remove(observers.begin(), observers.end(), obs), observers.end());
    }
    void notify(const string& message) {
        for (auto& obs : observers) {
            obs->update(message);
        }
    }
};

// 具体观察者
class ConcreteObserver : public Observer {
private:
    string name;
public:
    ConcreteObserver(string n) : name(n) {}
    void update(const string& message) override {
        cout << "Observer [" << name << "] received: " << message << endl;
    }
};

// 使用示例
int main() {
    Subject subject;

    ConcreteObserver obs1("Alice");
    ConcreteObserver obs2("Bob");

    subject.attach(&obs1);
    subject.attach(&obs2);

    subject.notify("New event happened!");

    subject.detach(&obs1);

    subject.notify("Another event happened!");

    return 0;
}
```

**示例二：**
杂志订阅场景
>  - 出版社（Publisher） → 被观察者（Subject）
>  - 订阅者（Subscriber/Reader） → 观察者（Observer）
>  - 新一期刊物出版 → 状态变化/事件
>  - 邮寄杂志 → 通知订阅者

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

// 观察者接口（订阅者）
class Subscriber {
public:
    virtual void update(const string& magazineName, int issue) = 0;
    virtual ~Subscriber() = default;
};

// 被观察者接口（出版社）
class Publisher {
public:
    virtual void attach(Subscriber* subscriber) = 0;
    virtual void detach(Subscriber* subscriber) = 0;
    virtual void notify(int issue) = 0;
    virtual ~Publisher() = default;
};

// 具体被观察者（具体出版社）
class MagazinePublisher : public Publisher {
private:
    string name;                      // 杂志名称
    vector<Subscriber*> subscribers;  // 订阅者列表
public:
    MagazinePublisher(const string& n) : name(n) {}

    void attach(Subscriber* subscriber) override {
        subscribers.push_back(subscriber);
    }

    void detach(Subscriber* subscriber) override {
        subscribers.erase(remove(subscribers.begin(), subscribers.end(), subscriber), subscribers.end());
    }

    void notify(int issue) override {
        for (auto& sub : subscribers) {
            sub->update(name, issue);
        }
    }

    // 出版新一期杂志
    void publishNewIssue(int issue) {
        cout << "📢 《" << name << "》 第 " << issue << " 期出版啦！" << endl;
        notify(issue);
    }
};

// 具体观察者（具体订阅者）
class Reader : public Subscriber {
private:
    string name;
public:
    Reader(const string& n) : name(n) {}

    void update(const string& magazineName, int issue) override {
        cout << "📬 " << name << " 收到杂志：《" << magazineName 
             << "》 第 " << issue << " 期" << endl;
    }
};

// 使用示例
int main() {
    // 创建一个出版社
    MagazinePublisher publisher("程序员之路");

    // 创建订阅者
    Reader alice("Alice");
    Reader bob("Bob");
    Reader charlie("Charlie");

    // Alice 和 Bob 订阅了杂志
    publisher.attach(&alice);
    publisher.attach(&bob);

    // 出版第 1 期
    publisher.publishNewIssue(1);

    cout << "\n--- Bob 取消订阅 ---\n" << endl;
    publisher.detach(&bob);

    // 出版第 2 期
    publisher.publishNewIssue(2);

    // Charlie 订阅后出版第 3 期
    cout << "\n--- Charlie 加入订阅 ---\n" << endl;
    publisher.attach(&charlie);
    publisher.publishNewIssue(3);

    return 0;
}
```
