@[toc]
## 一、装饰模式的介绍
装饰模式（Decorator Pattern） 是一种结构型设计模式，它的核心思想是：在不修改原有类代码的前提下，动态地给对象添加新的功能。它通过对象组合而不是继承，来实现功能扩展。这样既能保持类的简洁性，又能在运行时灵活地扩展功能。

**角色组成：**
>  1. Component（抽象组件）：定义对象的接口，可以给这些对象动态地添加职责。
>  2. ConcreteComponent（具体组件）：实现 Component 接口，是被装饰的原始对象。
>  3. Decorator（抽象装饰类）：继承 Component，并持有一个 Component 对象的引用，用来调用被装饰对象的方法。
>  4. ConcreteDecorator（具体装饰类）：在 Decorator 的基础上扩展功能，实现对原始对象的功能增强。

**使用场景：**
>  - 在不修改现有代码的前提下，给类动态扩展功能。
>  - 避免“继承爆炸”，如果有大量的子类仅仅是为了增加一些功能，可以用装饰模式代替。

**UML 类图：**
![**加粗样式**](https://i-blog.csdnimg.cn/direct/c3e9e0f00bb54a89b7ed2a9705d363d8.png)
**装饰模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/692178504774405abbf10c9b732a5399.png)
## 二、实例分析
**问题：**
假设你正在开发一个提供通知功能的库， 其他程序可使用它向用户发送关于重要事件的通知。

库的最初版本基于通知器Notifier类， 其中只有很少的几个成员变量， 一个构造函数和一个 send发送方法。 该方法可以接收来自客户端的消息参数， 并将该消息发送给一系列的邮箱， 邮箱列表则是通过构造函数传递给通知器的。 作为客户端的第三方程序仅会创建和配置通知器对象一次， 然后在有重要事件发生时对其进行调用。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/037dc034ada44d779943e41092512fb8.png)

此后某个时刻， 你会发现库的用户希望使用除邮件通知之外的功能。 许多用户会希望接收关于紧急事件的手机短信， 还有些用户希望在微信上接收消息， 而公司用户则希望在 QQ 上接收消息。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/86dfcc823c684c28a9a1fa163f33474f.png)
这有什么难的呢？ 首先扩展通知器类， 然后在新的子类中加入额外的通知方法。 现在客户端要对所需通知形式的对应类进行初始化， 然后使用该类发送后续所有的通知消息。

但是很快有人会问： ​ “为什么不同时使用多种通知形式呢？ 如果房子着火了， 你大概会想在所有渠道中都收到相同的消息吧。”

你可以尝试创建一个特殊子类来将多种通知方法组合在一起以解决该问题。 但这种方式会使得代码量迅速膨胀， 不仅仅是程序库代码， 客户端代码也会如此。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0dfebda9f6f64ed6bf989ee4fa208dac.png)

**解决方案：**
当你需要更改一个对象的行为时， 第一个跳入脑海的想法就是扩展它所属的类。 但是， 你不能忽视继承可能引发的几个严重问题。
>  - 继承是静态的。 你无法在运行时更改已有对象的行为， 只能使用由不同子类创建的对象来替代当前的整个对象。
>  - 子类只能有一个父类。 大部分编程语言不允许一个类同时继承多个类的行为。

其中一种方法是用聚合或组合 ， 而不是继承。 两者的工作方式几乎一模一样： 一个对象包含指向另一个对象的引用， 并将部分工作委派给引用对象； 继承中的对象则继承了父类的行为， 它们自己能够完成这些工作。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a78aea8d3cee4107bc5269c8bfffc842.png)
封装器是装饰模式的别称， 这个称谓明确地表达了该模式的主要思想。 ​ “封装器” 是一个能与其他 “目标” 对象连接的对象。 封装器包含与目标对象相同的一系列方法， 它会将所有接收到的请求委派给目标对象。 但是， 封装器可以在将请求委派给目标前后对其进行处理， 所以可能会改变最终结果。

那么什么时候一个简单的封装器可以被称为是真正的装饰呢？ 正如之前提到的， 封装器实现了与其封装对象相同的接口。 因此从客户端的角度来看， 这些对象是完全一样的。 封装器中的引用成员变量可以是遵循相同接口的任意对象。 这使得你可以将一个对象放入多个封装器中， 并在对象中添加所有这些封装器的组合行为。

比如在消息通知示例中， 我们可以将简单邮件通知行为放在基类 通知器中， 但将所有其他通知方法放入装饰中。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/31b78a4c6b9344f88bfe8e04b48a246d.png)
客户端代码必须将基础通知器放入一系列自己所需的装饰中。 因此最后的对象将形成一个栈结构。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6c0df3b6236a475e9b1712eb92f49cb8.png)
实际与客户端进行交互的对象将是最后一个进入栈中的装饰对象。 由于所有的装饰都实现了与通知基类相同的接口， 客户端的其他代码并不在意自己到底是与 “纯粹” 的通知器对象， 还是与装饰后的通知器对象进行交互。

我们可以使用相同方法来完成其他行为 （例如设置消息格式或者创建接收人列表）。 只要所有装饰都遵循相同的接口， 客户端就可以使用任意自定义的装饰来装饰对象。

综上所述，我要开发一个 通知功能库，支持基础的通知方式（例如邮件），然后通过 装饰模式 扩展出 短信、微信、QQ 等通知方式。这样程序就可以灵活组合所需的通知功能。

**类设计：**
> - Notifier（抽象通知接口）：定义 send(const std::string& message) 接口。
>  - ConcreteNotifier（具体通知类）：比如 EmailNotifier，实现最基础的通知方式。
>  - BaseDecorator（装饰器基类）：持有一个 Notifier*，转发 send() 调用。
>  - 具体装饰器（Concrete Decorator）：
>  		- SMSDecorator：增加短信通知
>  		- WeChatDecorator：增加微信通知
> 	 - QQDecorator：增加 QQ 通知

**代码实现：**
```cpp
#include <iostream>
#include <memory>
#include <string>
using namespace std;

// 抽象通知接口
class Notifier {
public:
    virtual void send(const string& message) = 0;
    virtual ~Notifier() = default;
};

// 基础通知：邮件
class EmailNotifier : public Notifier {
public:
    void send(const string& message) override {
        cout << "发送邮件: " << message << endl;
    }
};

// 装饰器基类
class BaseDecorator : public Notifier {
protected:
    shared_ptr<Notifier> wrappee;
public:
    BaseDecorator(shared_ptr<Notifier> notifier) : wrappee(notifier) {}
    void send(const string& message) override {
        if (wrappee) wrappee->send(message);
    }
};

// 短信装饰器
class SMSDecorator : public BaseDecorator {
public:
    SMSDecorator(shared_ptr<Notifier> notifier) : BaseDecorator(notifier) {}
    void send(const string& message) override {
        BaseDecorator::send(message);
        cout << "发送短信: " << message << endl;
    }
};

// 微信装饰器
class WeChatDecorator : public BaseDecorator {
public:
    WeChatDecorator(shared_ptr<Notifier> notifier) : BaseDecorator(notifier) {}
    void send(const string& message) override {
        BaseDecorator::send(message);
        cout << "发送微信: " << message << endl;
    }
};

// QQ装饰器
class QQDecorator : public BaseDecorator {
public:
    QQDecorator(shared_ptr<Notifier> notifier) : BaseDecorator(notifier) {}
    void send(const string& message) override {
        BaseDecorator::send(message);
        cout << "发送QQ消息: " << message << endl;
    }
};

// 应用程序使用通知功能
class Application {
    shared_ptr<Notifier> notifier;
public:
    void setNotifier(shared_ptr<Notifier> n) {
        notifier = n;
    }

    void doSomething() {
        if (notifier) {
            notifier->send("系统警告：磁盘空间不足！");
        }
    }
};

int main() {
    // 基础通知：邮件
    shared_ptr<Notifier> notifier = make_shared<EmailNotifier>();

    // 动态添加装饰器
    bool smsEnabled = true;
    bool wechatEnabled = true;
    bool qqEnabled = false;

    if (smsEnabled) {
        notifier = make_shared<SMSDecorator>(notifier);
    }
    if (wechatEnabled) {
        notifier = make_shared<WeChatDecorator>(notifier);
    }
    if (qqEnabled) {
        notifier = make_shared<QQDecorator>(notifier);
    }

    Application app;
    app.setNotifier(notifier);
    app.doSomething();

    return 0;
}
```
**优点：**
>  - 使用装饰模式，你可以随意组合不同的通知方式。
>  - 如果以后要增加 钉钉通知 / Slack 通知，只需要写一个新的 Decorator 类，而不用改已有代码。

## 三、示例代码
**实例一：**
在本例中， 装饰模式能够对敏感数据进行压缩和加密， 从而将数据从使用数据的代码中独立出来。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d903cfa090cb48c28a27f94edd8b3284.png)
程序使用一对装饰来封装数据源对象。 这两个封装器都改变了从磁盘读写数据的方式：
>  - 当数据即将被写入磁盘前， 装饰对数据进行加密和压缩。 在原始类对改变毫无察觉的情况下， 将加密后的受保护数据写入文件。
>  - 当数据刚从磁盘读出后， 同样通过装饰对数据进行解压和解密。

类设计：
>  - DataSource（接口）：定义 writeData(string) 和 readData() 方法。
>  - FileDataSource（具体组件）：实现基础的数据读写（写入文件，读取文件）。
>  - DataSourceDecorator（装饰器基类）：持有一个 DataSource*，转发 writeData() 和 readData()。
>  - EncryptionDecorator（加密装饰器）：在写入时加密，在读取时解密。
>  - CompressionDecorator（压缩装饰器）：在写入时压缩，在读取时解压。


**代码实现：**
```cpp
#include <iostream>
#include <fstream>
#include <memory>
#include <string>
#include <algorithm>
using namespace std;

// 抽象接口
class DataSource {
public:
    virtual void writeData(const string& data) = 0;
    virtual string readData() = 0;
    virtual ~DataSource() = default;
};

// 具体组件：文件数据源
class FileDataSource : public DataSource {
    string filename;
public:
    FileDataSource(const string& fname) : filename(fname) {}

    void writeData(const string& data) override {
        ofstream ofs(filename, ios::binary);
        ofs << data;
        ofs.close();
    }

    string readData() override {
        ifstream ifs(filename, ios::binary);
        string content((istreambuf_iterator<char>(ifs)), istreambuf_iterator<char>());
        ifs.close();
        return content;
    }
};

// 装饰器基类
class DataSourceDecorator : public DataSource {
protected:
    shared_ptr<DataSource> wrappee;
public:
    DataSourceDecorator(shared_ptr<DataSource> source) : wrappee(source) {}
    void writeData(const string& data) override {
        wrappee->writeData(data);
    }
    string readData() override {
        return wrappee->readData();
    }
};

// 加密装饰器
class EncryptionDecorator : public DataSourceDecorator {
public:
    EncryptionDecorator(shared_ptr<DataSource> source) : DataSourceDecorator(source) {}

    void writeData(const string& data) override {
        string encrypted = encrypt(data);
        wrappee->writeData(encrypted);
    }

    string readData() override {
        string data = wrappee->readData();
        return decrypt(data);
    }

private:
    string encrypt(const string& data) {
        string result = data;
        for (auto& ch : result) ch += 1; // 简单 Caesar 加密
        return result;
    }
    string decrypt(const string& data) {
        string result = data;
        for (auto& ch : result) ch -= 1;
        return result;
    }
};

// 压缩装饰器（这里只做简单“去空格”模拟压缩）
class CompressionDecorator : public DataSourceDecorator {
public:
    CompressionDecorator(shared_ptr<DataSource> source) : DataSourceDecorator(source) {}

    void writeData(const string& data) override {
        string compressed = compress(data);
        wrappee->writeData(compressed);
    }

    string readData() override {
        string data = wrappee->readData();
        return decompress(data);
    }

private:
    string compress(const string& data) {
        string result = data;
        result.erase(remove(result.begin(), result.end(), ' '), result.end()); // 模拟压缩：去掉空格
        return result;
    }
    string decompress(const string& data) {
        // 简单模拟：直接返回，不做真正解压
        return data;
    }
};

// 客户端
int main() {
    string filename = "data.txt";

    // 基础数据源
    shared_ptr<DataSource> source = make_shared<FileDataSource>(filename);

    // 添加装饰器：先加密 -> 再压缩
    shared_ptr<DataSource> decorated = make_shared<CompressionDecorator>(
                                           make_shared<EncryptionDecorator>(source));

    // 写入数据
    decorated->writeData("Hello World 装饰模式!");

    // 读取数据
    cout << "解压+解密后读取: " << decorated->readData() << endl;

    // 直接读取文件内容（可以看到压缩+加密后的数据）
    cout << "文件中的原始内容: " << source->readData() << endl;

    return 0;
}
```

**实例二：**
在 Qt 里，一个 QWidget（比如 QLabel）是基础组件，相当于 ConcreteComponent。我们通过继承 QWidget 并持有一个内部 QWidget*，在 paintEvent 中绘制额外效果，就相当于 Decorator。另外让装饰器支持 动态开关功能。这样在运行时就可以决定是否显示边框、阴影，而不需要重新创建对象。
```cpp
#include <QApplication>
#include <QLabel>
#include <QVBoxLayout>
#include <QPainter>
#include <QWidget>
#include <QPushButton>
#include <QHBoxLayout>

// 抽象组件（在 Qt 中 QWidget 就是基类组件）

// 装饰器基类
class WidgetDecorator : public QWidget {
    Q_OBJECT
protected:
    QWidget* m_widget; // 被装饰的组件
public:
    WidgetDecorator(QWidget* widget, QWidget* parent = nullptr)
        : QWidget(parent), m_widget(widget)
    {
        QVBoxLayout* layout = new QVBoxLayout(this);
        layout->setContentsMargins(0, 0, 0, 0);
        layout->addWidget(m_widget);
    }
};

// 边框装饰器
class BorderDecorator : public WidgetDecorator {
    Q_OBJECT
    bool m_enabled = true;
public:
    BorderDecorator(QWidget* widget, QWidget* parent = nullptr)
        : WidgetDecorator(widget, parent) {}

    void setEnabledBorder(bool enabled) {
        m_enabled = enabled;
        update();
    }

protected:
    void paintEvent(QPaintEvent* event) override {
        WidgetDecorator::paintEvent(event);

        if (!m_enabled) return;

        QPainter painter(this);
        painter.setPen(QPen(Qt::red, 3));
        painter.drawRect(rect().adjusted(1, 1, -2, -2));
    }
};

// 阴影装饰器
class ShadowDecorator : public WidgetDecorator {
    Q_OBJECT
    bool m_enabled = true;
public:
    ShadowDecorator(QWidget* widget, QWidget* parent = nullptr)
        : WidgetDecorator(widget, parent) {}

    void setEnabledShadow(bool enabled) {
        m_enabled = enabled;
        update();
    }

protected:
    void paintEvent(QPaintEvent* event) override {
        WidgetDecorator::paintEvent(event);

        if (!m_enabled) return;

        QPainter painter(this);
        painter.setPen(Qt::NoPen);
        painter.setBrush(QColor(0, 0, 0, 50));
        painter.drawRect(rect().adjusted(5, 5, 0, 0));
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QLabel* label = new QLabel("Hello 装饰模式 in Qt!");
    label->setAlignment(Qt::AlignCenter);
    label->setStyleSheet("background:white; font-size:18px;");

    // 装饰器链：先阴影，再边框
    ShadowDecorator* shadow = new ShadowDecorator(label);
    BorderDecorator* border = new BorderDecorator(shadow);

    // 主窗口
    QWidget window;
    QVBoxLayout* layout = new QVBoxLayout(&window);

    layout->addWidget(border);

    // 控制按钮
    QHBoxLayout* btnLayout = new QHBoxLayout();
    QPushButton* btnToggleShadow = new QPushButton("切换阴影");
    QPushButton* btnToggleBorder = new QPushButton("切换边框");
    btnLayout->addWidget(btnToggleShadow);
    btnLayout->addWidget(btnToggleBorder);

    layout->addLayout(btnLayout);

    // 信号槽连接
    QObject::connect(btnToggleShadow, &QPushButton::clicked, [&]() {
        static bool shadowOn = true;
        shadowOn = !shadowOn;
        shadow->setEnabledShadow(shadowOn);
    });

    QObject::connect(btnToggleBorder, &QPushButton::clicked, [&]() {
        static bool borderOn = true;
        borderOn = !borderOn;
        border->setEnabledBorder(borderOn);
    });

    window.resize(400, 200);
    window.show();

    return app.exec();
}
#include "main.moc"
```
