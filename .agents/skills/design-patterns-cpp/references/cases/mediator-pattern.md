@[toc]
## 一、模式定义
&emsp;&emsp;在软件系统中，对象之间经常会发生复杂的交互。如果每个对象都和其他对象直接通信，就会形成 网状结构，导致系统难以维护和扩展。中介者模式是一种行为设计模式，它的核心思想是：引入一个中介者对象（Mediator） 来封装对象之间的交互。对象不再直接引用彼此，而是通过中介者通信，从而减少对象之间的耦合。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6aa917f78e8b48479a35d9c53bdea556.png)
**主要角色：**
>  1. Mediator（抽象中介者）：定义对象与中介者通信的接口。
>  2. ConcreteMediator（具体中介者）：实现协调各个同事对象的交互逻辑。
>  3. Colleague（同事类抽象）：各个对象只和中介者通信，不直接相互调用。
>  4. ConcreteColleague（具体同事类）：各自实现业务逻辑，当需要与其他对象交互时，通过中介者完成。

**中介者模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fbfef620fed941e38d729d1f846adfd3.png)
## 二、实例分析
**问题：**
假如你有一个创建和修改客户资料的对话框， 它由各种控件组成， 例如文本框 （Text­Field）、 复选框 （Checkbox） 和按钮 （Button） 等。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/567ccc104388484e9672c7836765dfc7.png)
某些表单元素可能会直接进行互动。 例如， 选中 “我有一只狗” 复选框后可能会显示一个隐藏文本框用于输入狗狗的名字。 另一个例子是提交按钮必须在保存数据前校验所有输入内容。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bf740f8d34864f80a72da6bf51a83a1f.png)
如果直接在表单元素代码中实现业务逻辑， 你将很难在程序其他表单中复用这些元素类。 例如， 由于复选框类与狗狗的文本框相耦合， 所以将无法在其他表单中使用它。 你要么使用渲染资料表单时用到的所有类， 要么一个都不用。

**解决方案：**
中介者模式建议你停止组件之间的直接交流并使其相互独立。 这些组件必须调用特殊的中介者对象， 通过中介者对象重定向调用行为， 以间接的方式进行合作。 最终， 组件仅依赖于一个中介者类， 无需与多个其他组件相耦合。

在资料编辑表单的例子中， 对话框 （Dialog） 类本身将作为中介者， 其很可能已知自己所有的子元素， 因此你甚至无需在该类中引入新的依赖关系。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7e7d6363157f435fb5fe0633b72c3491.png)
绝大部分重要的修改都在实际表单元素中进行。 让我们想想提交按钮。 之前， 当用户点击按钮后， 它必须对所有表单元素数值进行校验。 而现在它的唯一工作是将点击事件通知给对话框。 收到通知后， 对话框可以自行校验数值或将任务委派给各元素。 这样一来， 按钮不再与多个表单元素相关联， 而仅依赖于对话框类。

你还可以为所有类型的对话框抽取通用接口， 进一步削弱其依赖性。 接口中将声明一个所有表单元素都能使用的通知方法， 可用于将元素中发生的事件通知给对话框。 这样一来， 所有实现了该接口的对话框都能使用这个提交按钮了。

采用这种方式， 中介者模式让你能在单个中介者对象中封装多个对象间的复杂关系网。 类所拥有的依赖关系越少， 就越易于修改、 扩展或复用。

**需求分析：**
>  1. 用户输入用户名/密码（TextField）。
>  2. 用户点击“记住我”（Checkbox）。
>  3. 用户点击“登录”（Button）。
>  4. 各个控件不直接交互，统一通过 Dialog 中介者进行验证与控制。

代码实现：
```cpp
#include <QApplication>
#include <QWidget>
#include <QPushButton>
#include <QLineEdit>
#include <QCheckBox>
#include <QVBoxLayout>
#include <QLabel>
#include <QMessageBox>

// 抽象中介者
class Mediator {
public:
    virtual void notify(QWidget* sender, const QString& event) = 0;
    virtual ~Mediator() = default;
};

// 登录对话框作为中介者
class LoginDialog : public QWidget, public Mediator {
    Q_OBJECT
private:
    QLineEdit* usernameField;
    QLineEdit* passwordField;
    QCheckBox* rememberCheck;
    QPushButton* loginBtn;
    QLabel* statusLabel;

public:
    LoginDialog() {
        QVBoxLayout* layout = new QVBoxLayout(this);

        usernameField = new QLineEdit();
        passwordField = new QLineEdit();
        passwordField->setEchoMode(QLineEdit::Password);
        rememberCheck = new QCheckBox("记住我");
        loginBtn = new QPushButton("登录");
        statusLabel = new QLabel("请输入用户名和密码");

        layout->addWidget(new QLabel("用户名:"));
        layout->addWidget(usernameField);
        layout->addWidget(new QLabel("密码:"));
        layout->addWidget(passwordField);
        layout->addWidget(rememberCheck);
        layout->addWidget(loginBtn);
        layout->addWidget(statusLabel);

        // 绑定事件
        connect(usernameField, &QLineEdit::textChanged, [=]() {
            notify(usernameField, "textChanged");
        });
        connect(passwordField, &QLineEdit::textChanged, [=]() {
            notify(passwordField, "textChanged");
        });
        connect(rememberCheck, &QCheckBox::stateChanged, [=]() {
            notify(rememberCheck, "stateChanged");
        });
        connect(loginBtn, &QPushButton::clicked, [=]() {
            notify(loginBtn, "clicked");
        });

        // 初始化按钮状态
        loginBtn->setEnabled(false);
    }

    // 中介者实现
    void notify(QWidget* sender, const QString& event) override {
        if ((sender == usernameField || sender == passwordField) && event == "textChanged") {
            // 检查输入框是否为空
            bool ready = !usernameField->text().isEmpty() && !passwordField->text().isEmpty();
            loginBtn->setEnabled(ready);
            statusLabel->setText(ready ? "可以尝试登录" : "请输入用户名和密码");
        }
        else if (sender == rememberCheck && event == "stateChanged") {
            statusLabel->setText(rememberCheck->isChecked() ? "已勾选记住我" : "未勾选记住我");
        }
        else if (sender == loginBtn && event == "clicked") {
            if (usernameField->text() == "admin" && passwordField->text() == "1234") {
                QMessageBox::information(this, "登录成功", "欢迎 " + usernameField->text());
            } else {
                QMessageBox::warning(this, "登录失败", "用户名或密码错误");
            }
        }
    }
};

// 主程序
int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    LoginDialog dlg;
    dlg.setWindowTitle("登录对话框（中介者模式）");
    dlg.show();

    return app.exec();
}
```

## 三、示例代码
**示例一：**
以聊天室为例：用户之间不直接发消息，而是通过聊天室（中介者）。
```cpp
#include <iostream>
#include <string>
#include <vector>
#include <memory>
using namespace std;

// 抽象中介者
class ChatMediator {
public:
    virtual void sendMessage(const string& message, const string& user) = 0;
    virtual ~ChatMediator() = default;
};

// 抽象同事类
class User {
protected:
    string name;
    ChatMediator* mediator;
public:
    User(const string& n, ChatMediator* m) : name(n), mediator(m) {}
    virtual void send(const string& message) = 0;
    virtual void receive(const string& message, const string& from) = 0;
    string getName() const { return name; }
    virtual ~User() = default;
};

// 具体中介者
class ChatRoom : public ChatMediator {
private:
    vector<User*> users;
public:
    void addUser(User* user) { users.push_back(user); }

    void sendMessage(const string& message, const string& user) override {
        for (auto u : users) {
            if (u->getName() != user) {
                u->receive(message, user);
            }
        }
    }
};

// 具体同事类
class ConcreteUser : public User {
public:
    ConcreteUser(const string& n, ChatMediator* m) : User(n, m) {}

    void send(const string& message) override {
        cout << name << " 发送: " << message << endl;
        mediator->sendMessage(message, name);
    }

    void receive(const string& message, const string& from) override {
        cout << name << " 收到来自 " << from << " 的消息: " << message << endl;
    }
};

// 使用示例
int main() {
    ChatRoom chat;

    ConcreteUser u1("Alice", &chat);
    ConcreteUser u2("Bob", &chat);
    ConcreteUser u3("Charlie", &chat);

    chat.addUser(&u1);
    chat.addUser(&u2);
    chat.addUser(&u3);

    u1.send("大家好！");
    u2.send("你好 Alice！");
    u3.send("欢迎来到聊天室！");

    return 0;
}
```

输出结果：
```cpp
Alice 发送: 大家好！
Bob 收到来自 Alice 的消息: 大家好！
Charlie 收到来自 Alice 的消息: 大家好！
Bob 发送: 你好 Alice！
Alice 收到来自 Bob 的消息: 你好 Alice！
Charlie 收到来自 Bob 的消息: 你好 Alice！
Charlie 发送: 欢迎来到聊天室！
Alice 收到来自 Charlie 的消息: 欢迎来到聊天室！
Bob 收到来自 Charlie 的消息: 欢迎来到聊天室！
```

**示例二：**
有三个按钮（Start、Stop、Reset），它们之间的状态逻辑由 中介者 统一协调。按下 Start → 其他按钮状态更新。
```cpp
#include <QApplication>
#include <QPushButton>
#include <QLabel>
#include <QVBoxLayout>
#include <QWidget>

// 抽象中介者
class Mediator : public QObject {
	Q_OBJECT
public:
    virtual void notify(QWidget* sender, const QString& event) = 0;
    virtual ~Mediator() = default;
};

// 具体中介者
class ConcreteMediator : public Mediator {
    Q_OBJECT
private:
    QPushButton* startBtn;
    QPushButton* stopBtn;
    QPushButton* resetBtn;
    QLabel* statusLabel;
public:
    ConcreteMediator(QPushButton* s, QPushButton* t, QPushButton* r, QLabel* l)
        : startBtn(s), stopBtn(t), resetBtn(r), statusLabel(l) {}

    void notify(QWidget* sender, const QString& event) override {
        if (sender == startBtn && event == "clicked") {
            statusLabel->setText("系统已启动");
            startBtn->setEnabled(false);
            stopBtn->setEnabled(true);
            resetBtn->setEnabled(true);
        }
        else if (sender == stopBtn && event == "clicked") {
            statusLabel->setText("系统已停止");
            startBtn->setEnabled(true);
            stopBtn->setEnabled(false);
            resetBtn->setEnabled(true);
        }
        else if (sender == resetBtn && event == "clicked") {
            statusLabel->setText("系统已重置");
            startBtn->setEnabled(true);
            stopBtn->setEnabled(false);
            resetBtn->setEnabled(false);
        }
    }
};

// 使用示例
int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QWidget window;
    QVBoxLayout* layout = new QVBoxLayout(&window);

    QPushButton* startBtn = new QPushButton("Start");
    QPushButton* stopBtn = new QPushButton("Stop");
    QPushButton* resetBtn = new QPushButton("Reset");
    QLabel* statusLabel = new QLabel("等待操作...");

    layout->addWidget(startBtn);
    layout->addWidget(stopBtn);
    layout->addWidget(resetBtn);
    layout->addWidget(statusLabel);

    // 初始化按钮状态
    stopBtn->setEnabled(false);
    resetBtn->setEnabled(false);

    // 创建中介者
    ConcreteMediator mediator(startBtn, stopBtn, resetBtn, statusLabel);

    QObject::connect(startBtn, &QPushButton::clicked, [&]() {
        mediator.notify(startBtn, "clicked");
    });
    QObject::connect(stopBtn, &QPushButton::clicked, [&]() {
        mediator.notify(stopBtn, "clicked");
    });
    QObject::connect(resetBtn, &QPushButton::clicked, [&]() {
        mediator.notify(resetBtn, "clicked");
    });

    window.show();
    return app.exec();
}
```
