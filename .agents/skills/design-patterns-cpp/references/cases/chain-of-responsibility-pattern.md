@[toc]
## 一、责任链模式的概念
&emsp;&emsp;责任链模式是一种行为设计模式， 允许你将请求沿着处理者链进行发送。 收到请求后， 每个处理者均可对请求进行处理， 或将其传递给链上的下个处理者。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b90e4037336d4c34bd713b71d902212c.png)

**核心思想：**
>  - 每个处理者（Handler）只处理自己负责的部分。
>  - 如果当前处理者不能处理，就把请求交给下一个处理者。
>  - 请求从链头发出，直到链上的某个处理者处理完成或者到达链尾。

**责任链模式的结构：**
>  - 抽象处理者（Handler）：定义一个处理请求的接口。持有下一个处理者的引用。
>  - 具体处理者（ConcreteHandler）：实现处理逻辑。如果不能处理，就交给下一个处理者。
>  - 客户端（Client）：创建链条，并将请求发送到链头。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9212a8cc9af047a0b127ae928b4ce2d4.png)
## 二、实例分析
**问题：**
假如你正在开发一个在线订购系统。 你希望对系统访问进行限制， 只允许认证用户创建订单。 此外， 拥有管理权限的用户也拥有所有订单的完全访问权限。

简单规划后， 你会意识到这些检查必须依次进行。 只要接收到包含用户凭据的请求， 应用程序就可尝试对进入系统的用户进行认证。 但如果由于用户凭据不正确而导致认证失败， 那就没有必要进行后续检查了。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8ddce31e6f9c49fcb95afdd097bc5200.png)
在接下来的几个月里， 你实现了后续的几个检查步骤。
>  - 一位同事认为直接将原始数据传递给订购系统存在安全隐患。 因此你新增了额外的验证步骤来清理请求中的数据。
>  - 过了一段时间， 有人注意到系统无法抵御暴力密码破解方式的攻击。 为了防范这种情况， 你立刻添加了一个检查步骤来过滤来自同一 IP 地址的重复错误请求。
>  - 又有人提议你可以对包含同样数据的重复请求返回缓存中的结果， 从而提高系统响应速度。 因此， 你新增了一个检查步骤， 确保只有没有满足条件的缓存结果时请求才能通过并被发送给系统。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bb1e6ed62349435d94dadba3bb0d7562.png)
检查代码本来就已经混乱不堪， 而每次新增功能都会使其更加臃肿。 修改某个检查步骤有时会影响其他的检查步骤。 最糟糕的是， 当你希望复用这些检查步骤来保护其他系统组件时， 你只能复制部分代码， 因为这些组件只需部分而非全部的检查步骤。

系统会变得让人非常费解， 而且其维护成本也会激增。 你在艰难地和这些代码共处一段时间后， 有一天终于决定对整个系统进行重构。

**解决方案：**
与许多其他行为设计模式一样， 责任链会将特定行为转换为被称作处理者的独立对象。 在上述示例中， 每个检查步骤都可被抽取为仅有单个方法的类， 并执行检查操作。 请求及其数据则会被作为参数传递给该方法。

模式建议你将这些处理者连成一条链。 链上的每个处理者都有一个成员变量来保存对于下一处理者的引用。 除了处理请求外， 处理者还负责沿着链传递请求。 请求会在链上移动， 直至所有处理者都有机会对其进行处理。

最重要的是： 处理者可以决定不再沿着链传递请求， 这可高效地取消所有后续处理步骤。

在我们的订购系统示例中， 处理者会在进行请求处理工作后决定是否继续沿着链传递请求。 如果请求中包含正确的数据， 所有处理者都将执行自己的主要行为， 无论该行为是身份验证还是数据缓存。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ddbe5859c3284535af7c8a7667348e4a.png)
不过还有一种稍微不同的方式 （也是更经典一种）， 那就是处理者接收到请求后自行决定是否能够对其进行处理。 如果自己能够处理， 处理者就不再继续传递请求。 因此在这种情况下， 每个请求要么最多有一个处理者对其进行处理， 要么没有任何处理者对其进行处理。 在处理图形用户界面元素栈中的事件时， 这种方式非常常见。

例如， 当用户点击按钮时， 按钮产生的事件将沿着 GUI 元素链进行传递， 最开始是按钮的容器 （如窗体或面板）， 直至应用程序主窗口。 链上第一个能处理该事件的元素会对其进行处理。 此外， 该例还有另一个值得我们关注的地方： 它表明我们总能从对象树中抽取出链来。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/da07a5148d7145629641211cdde0f968.png)
所有处理者类均实现同一接口是关键所在。 每个具体处理者仅关心下一个包含 execute执行方法的处理者。 这样一来， 你就可以在运行时使用不同的处理者来创建链， 而无需将相关代码与处理者的具体类进行耦合。
## 三、示例代码
**示例一：请假审批流程**
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 抽象处理者
class Handler {
protected:
    Handler* next = nullptr;  // 指向下一个处理者
public:
    virtual ~Handler() = default;
    void setNext(Handler* handler) { next = handler; }

    virtual void handleRequest(int days) {
        if (next) next->handleRequest(days);
    }
};

// 具体处理者：组长
class TeamLeader : public Handler {
public:
    void handleRequest(int days) override {
        if (days <= 2) {
            cout << "组长批准了 " << days << " 天请假" << endl;
        } else if (next) {
            next->handleRequest(days);
        }
    }
};

// 具体处理者：经理
class Manager : public Handler {
public:
    void handleRequest(int days) override {
        if (days <= 5) {
            cout << "经理批准了 " << days << " 天请假" << endl;
        } else if (next) {
            next->handleRequest(days);
        }
    }
};

// 具体处理者：总监
class Director : public Handler {
public:
    void handleRequest(int days) override {
        if (days <= 10) {
            cout << "总监批准了 " << days << " 天请假" << endl;
        } else {
            cout << "请假天数太多，无法批准！" << endl;
        }
    }
};

// 客户端
int main() {
    TeamLeader leader;
    Manager manager;
    Director director;

    // 组装责任链：组长 -> 经理 -> 总监
    leader.setNext(&manager);
    manager.setNext(&director);

    cout << "员工请假 1 天:" << endl;
    leader.handleRequest(1);

    cout << "员工请假 4 天:" << endl;
    leader.handleRequest(4);

    cout << "员工请假 8 天:" << endl;
    leader.handleRequest(8);

    cout << "员工请假 15 天:" << endl;
    leader.handleRequest(15);

    return 0;
}
```
