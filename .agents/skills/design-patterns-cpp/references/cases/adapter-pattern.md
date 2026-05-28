@[toc]
## 一、适配器模式简介
适配器模式是一种结构型设计模式， 它能使接口不兼容的对象能够相互合作。
**它的主要作用是：**
>  - 将一个类的接口转换成客户端希望的另一个接口。
>  - 让原本接口不兼容的类能够一起工作。
>  
类似于现实中的“插头适配器”：电源插座是两孔的，但你的设备插头是三孔的，需要一个转换头来兼容。


**适配器模式的角色：**
>  1. 目标接口（Target）：客户端期望使用的接口。
>  2. 适配者（Adaptee）：已有的接口或类，但接口格式不符合目标接口。
>  3. 适配器（Adapter）：中间的桥梁，把 Adaptee 的接口转成 Target 的接口。

**适配器模式的实现方式：**
>  1. 类适配器（使用继承）：通过多继承来适配（C++ 支持）。缺点：受限于 C++ 单继承/多继承的复杂性。
>  2. 对象适配器（使用组合）：通过持有一个 Adaptee 对象，在 Adapter 中转发调用（推荐）。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3dc2f339eee6445b8fa5246acac4e4d6.png)
## 二、具体实例分析
**问题：**
假如你正在开发一款股票市场监测程序， 它会从不同来源下载 XML 格式的股票数据， 然后向用户呈现出美观的图表。

在开发过程中， 你决定在程序中整合一个第三方智能分析函数库。 但是遇到了一个问题， 那就是分析函数库只兼容 JSON 格式的数据。 无法 “直接” 使用分析函数库， 因为它所需的输入数据格式与你的程序不兼容。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d0fbf0e4afb44505977cd1431ecf2f19.png)
可以修改程序库来支持 XML。 但是， 这可能需要修改部分依赖该程序库的现有代码。 甚至还有更糟糕的情况， 你可能根本没有程序库的源代码， 从而无法对其进行修改。

**解决方案：**
你可以创建一个适配器。 这是一个特殊的对象， 能够转换对象接口， 使其能与其他对象进行交互。

适配器模式通过封装对象将复杂的转换过程隐藏于幕后。 被封装的对象甚至察觉不到适配器的存在。 例如， 你可以使用一个将所有数据转换为英制单位 （如英尺和英里） 的适配器封装运行于米和千米单位制中的对象。

适配器不仅可以转换不同格式的数据， 其还有助于采用不同接口的对象之间的合作。 它的运作方式如下：
>  1. 适配器实现与其中一个现有对象兼容的接口。
>  2. 现有对象可以使用该接口安全地调用适配器方法。
>  3. 适配器方法被调用后将以另一个对象兼容的格式和顺序将请求传递给该对象。

有时你甚至可以创建一个双向适配器来实现双向转换调用。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9892097eda054295815d93497e93ad3d.png)
让我们回到股票市场程序。 为了解决数据格式不兼容的问题， 你可以为分析函数库中的每个类创建将 XML 转换为 JSON 格式的适配器， 然后让客户端仅通过这些适配器来与函数库进行交流。 当某个适配器被调用时， 它会将传入的 XML 数据转换为 JSON 结构， 并将其传递给被封装分析对象的相应方法。

**适配器模式结构：**
实现时使用了构成原则： 适配器实现了其中一个对象的接口， 并对另一个对象进行封装。 所有流行的编程语言都可以实现适配器。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/74b4275cb02a413ab61a1891aef532ba.png)
**说明：**
> 1. Client Interface（Target：目标接口）：客户端期望使用的接口。
>  2. Service（Adaptee：适配者）：已有的类，功能正确，但接口与 Target 不兼容。
>  3. Adapter（适配器）：作为桥梁，把 Adaptee 的 serviceMethod(specialData) 转换为 Target 的 method(data)。一般通过 组合（持有一个 Adaptee 对象）来实现。

**示例代码：**
需求：客户端需要统一的 MediaPlayer 接口来播放音频，但现有的 AdvancedMediaPlayer 只支持 MP4 和 VLC 格式。用适配器模式来让 AdvancedMediaPlayer 适配到 MediaPlayer。
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 目标接口 (客户端期望使用的接口)
class MediaPlayer {
public:
    virtual void play(const string& filename) = 0;
    virtual ~MediaPlayer() = default;
};

// 适配者 (已有的接口，但不兼容)
class AdvancedMediaPlayer {
public:
    void playVlc(const string& filename) {
        cout << "Playing vlc file: " << filename << endl;
    }
    void playMp4(const string& filename) {
        cout << "Playing mp4 file: " << filename << endl;
    }
};

// 适配器 (桥梁)
class MediaAdapter : public MediaPlayer {
public:
    MediaAdapter(const string& type) {
        player = make_unique<AdvancedMediaPlayer>();
        format = type;
    }

    void play(const string& filename) override {
        if (format == "vlc")
            player->playVlc(filename);
        else if (format == "mp4")
            player->playMp4(filename);
        else
            cout << "Invalid format: " << format << endl;
    }

private:
    unique_ptr<AdvancedMediaPlayer> player;
    string format;
};

// 客户端使用
class AudioPlayer : public MediaPlayer {
public:
    void play(const string& filename) override {
        // 简单判断扩展名
        if (filename.find(".mp3") != string::npos) {
            cout << "Playing mp3 file: " << filename << endl;
        } else if (filename.find(".mp4") != string::npos) {
            adapter = make_unique<MediaAdapter>("mp4");
            adapter->play(filename);
        } else if (filename.find(".vlc") != string::npos) {
            adapter = make_unique<MediaAdapter>("vlc");
            adapter->play(filename);
        } else {
            cout << "Unsupported format: " << filename << endl;
        }
    }

private:
    unique_ptr<MediaAdapter> adapter;
};

int main() {
    unique_ptr<MediaPlayer> player = make_unique<AudioPlayer>();

    player->play("song.mp3");
    player->play("movie.mp4");
    player->play("video.vlc");
    player->play("unknown.avi");

    return 0;
}
```

**类适配器（使用继承） 结构图：**
这一实现使用了继承机制： 适配器同时继承两个对象的接口。 请注意， 这种方式仅能在支持多重继承的编程语言中实现， 例如 C++。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2c639dde2440428c9ee39237e87f54fe.png)
**示例代码：**
```cpp
#include <QCoreApplication>
#include <QDebug>

// 目标接口（客户端需要的接口）
class Printer {
public:
    virtual void print(const QString &text) = 0;
    virtual ~Printer() {}
};

// 适配者（已有的老接口）
class OldPrinter {
public:
    void oldPrint(const QByteArray &data) {
        qDebug() << "OldPrinter printing:" << data;
    }
};

// 类适配器（继承 Target 和 Adaptee）
class ClassAdapter : public Printer, public OldPrinter {
public:
    void print(const QString &text) override {
        QByteArray data = text.toUtf8();
        oldPrint(data);  // 调用旧接口
    }
};

// 使用示例
void testClassAdapter() {
    Printer *printer = new ClassAdapter();
    printer->print("Hello Class Adapter!");
    delete printer;
}
```

## 三、示例代码
**场景说明：**
假设我们有一个 老打印机类 OldPrinter，只能打印 QByteArray，而新的客户端需要一个接口 Printer 能打印 QString。我们用适配器把旧接口适配给新接口。

**① 目标接口（Target）**
```cpp
class Printer {
public:
    virtual void print(const QString &text) = 0;
    virtual ~Printer() {}
};
```

**② 被适配类（Adaptee）**
```cpp
class OldPrinter {
public:
    void oldPrint(const QByteArray &data) {
        qDebug() << "OldPrinter printing:" << data;
    }
};
```

**③ 适配器（Adapter，对象适配器）**
```cpp
class PrinterAdapter : public Printer {
public:
    PrinterAdapter(OldPrinter *oldPrinter) : m_oldPrinter(oldPrinter) {}

    void print(const QString &text) override {
        // 将 QString 转成 QByteArray，调用旧打印机
        QByteArray data = text.toUtf8();
        m_oldPrinter->oldPrint(data);
    }

private:
    OldPrinter *m_oldPrinter;  // 组合关系
};
```

**④ 客户端使用**
```cpp
int main(int argc, char *argv[]) {
    QCoreApplication a(argc, argv);

    OldPrinter oldPrinter;

    Printer *printer = new PrinterAdapter(&oldPrinter);
    printer->print("Hello Adapter Pattern!");
    
    delete printer;
    return a.exec();
}
```

分析：
>  1. Printer 是客户端期望的接口（Target）。
>  2. OldPrinter 是已有的类（Adaptee），接口不兼容。
>  3. PrinterAdapter 是适配器（Adapter），组合了 OldPrinter 对象，将 QString 转为 QByteArray 并调用旧接口。
>  4. 客户端只依赖 Printer，无需知道底层 OldPrinter 的存在。
