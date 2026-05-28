@[toc]
## 一、桥接模式的定义
&emsp;&emsp;桥接模式是一种结构型设计模式，它的主要作用是将抽象部分与实现部分分离，使它们能够独立变化。换句话说，就是把“抽象”和“实现”放到两个独立的类层次中，通过组合而不是继承来连接它们。

**桥接模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c5ac6392d4d84c118c75f8c14527b3e1.png)
## 二、为什么需要桥接模式？
**问题：**
假如有一个几何形状Shape类， 从它能扩展出两个子类： ​ 圆形Circle和方形Square 。 你希望对这样的类层次结构进行扩展以使其包含颜色， 所以你打算创建名为红色Red和蓝色Blue的形状子类。 但是， 由于你已有两个子类， 所以总共需要创建四个类才能覆盖所有组合， 例如蓝色圆形Blue­Circle和红色方形Red­Square 。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/826a1f88e3a74a2ea289520c5a0bc761.png)


在层次结构中新增形状和颜色将导致代码复杂程度指数增长。 例如添加三角形状， 你需要新增两个子类， 也就是每种颜色一个； 此后新增一种新颜色需要新增三个子类， 即每种形状一个。 如此以往， 情况会越来越糟糕。

**解决方案：**
问题的根本原因是我们试图在两个独立的维度——形状与颜色——上扩展形状类。 这在处理类继承时是很常见的问题。
桥接模式通过将继承改为组合的方式来解决这个问题。 具体来说， 就是抽取其中一个维度并使之成为独立的类层次， 这样就可以在初始类中引用这个新层次的对象， 从而使得一个类不必拥有所有的状态和行为。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a4f4919fa231413eb81a1639a638a5ef.png)
根据该方法， 我们可以将颜色相关的代码抽取到拥有红色和蓝色两个子类的颜色类中， 然后在形状类中添加一个指向某一颜色对象的引用成员变量。 现在， 形状类可以将所有与颜色相关的工作委派给连入的颜色对象。 这样的引用就成为了形状和颜色之间的桥梁。 此后， 新增颜色将不再需要修改形状的类层次， 反之亦然。

桥接模式 UML 类图：
```cpp
        Abstraction（形状）
            |
            v
   RefinedAbstraction（圆形/矩形）
            |
            | has-a
            v
     Implementor（颜色接口）
            |
   ---------------------
   |                   |
ConcreteImplementorA   ConcreteImplementorB
 （红色）                 （蓝色）
```


**桥接模式的角色：**
>  1. 抽象类（Abstraction）：定义抽象接口，包含一个实现类的引用。
>  2. 扩展抽象类（RefinedAbstraction）：在抽象类的基础上扩展功能。
>  3. 实现接口（Implementor）：定义实现部分的接口。
>  4. 具体实现类（ConcreteImplementor）：实现具体的实现逻辑。

**示例：形状 + 颜色**
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 实现部分（Implementor）
class Color {
public:
    virtual void applyColor() = 0;
    virtual ~Color() = default;
};

// 具体实现类
class Red : public Color {
public:
    void applyColor() override {
        cout << "Red" << endl;
    }
};

class Blue : public Color {
public:
    void applyColor() override {
        cout << "Blue" << endl;
    }
};

// 抽象部分（Abstraction）
class Shape {
protected:
    shared_ptr<Color> color; // 桥接
public:
    Shape(shared_ptr<Color> c) : color(c) {}
    virtual void draw() = 0;
    virtual ~Shape() = default;
};

// 扩展抽象类
class Circle : public Shape {
public:
    Circle(shared_ptr<Color> c) : Shape(c) {}
    void draw() override {
        cout << "Drawing Circle in ";
        color->applyColor();
    }
};

class Rectangle : public Shape {
public:
    Rectangle(shared_ptr<Color> c) : Shape(c) {}
    void draw() override {
        cout << "Drawing Rectangle in ";
        color->applyColor();
    }
};

// 测试
int main() {
    shared_ptr<Color> red = make_shared<Red>();
    shared_ptr<Color> blue = make_shared<Blue>();

    shared_ptr<Shape> circle = make_shared<Circle>(red);
    shared_ptr<Shape> rectangle = make_shared<Rectangle>(blue);

    circle->draw();     // Drawing Circle in Red
    rectangle->draw();  // Drawing Rectangle in Blue
    return 0;
}
```
## 三、示例代码
**示例说明：**
用图形（圆形、矩形） × 绘制引擎（QPainter 绘制、QSvgGenerator 绘制）
>  - 抽象部分（Shape）：图形（Circle、Rectangle）
>  - 实现部分（DrawAPI）：绘制引擎（PainterDraw、SvgDraw）
>  - 桥接：Shape 持有 DrawAPI 的指针，可以自由组合。

代码示例：
```cpp
#include <QApplication>
#include <QWidget>
#include <QPainter>
#include <QSvgGenerator>
#include <memory>
#include <iostream>

// 实现部分接口（DrawAPI）
class DrawAPI {
public:
    virtual void drawCircle(QWidget *widget, int x, int y, int r) = 0;
    virtual void drawRect(QWidget *widget, int x, int y, int w, int h) = 0;
    virtual ~DrawAPI() = default;
};

// 具体实现1：用 QPainter 绘制
class PainterDraw : public DrawAPI {
public:
    void drawCircle(QWidget *widget, int x, int y, int r) override {
        QPainter p(widget);
        p.setPen(Qt::red);
        p.drawEllipse(QPoint(x, y), r, r);
    }
    void drawRect(QWidget *widget, int x, int y, int w, int h) override {
        QPainter p(widget);
        p.setPen(Qt::blue);
        p.drawRect(x, y, w, h);
    }
};

// 具体实现2：绘制到 SVG 文件
class SvgDraw : public DrawAPI {
public:
    void drawCircle(QWidget *widget, int x, int y, int r) override {
        QSvgGenerator generator;
        generator.setFileName("circle.svg");
        generator.setSize(QSize(widget->width(), widget->height()));

        QPainter p(&generator);
        p.setPen(Qt::red);
        p.drawEllipse(QPoint(x, y), r, r);
    }
    void drawRect(QWidget *widget, int x, int y, int w, int h) override {
        QSvgGenerator generator;
        generator.setFileName("rect.svg");
        generator.setSize(QSize(widget->width(), widget->height()));

        QPainter p(&generator);
        p.setPen(Qt::blue);
        p.drawRect(x, y, w, h);
    }
};

// 抽象部分（Shape）
class Shape {
protected:
    std::shared_ptr<DrawAPI> drawApi;
public:
    Shape(std::shared_ptr<DrawAPI> api) : drawApi(api) {}
    virtual void draw(QWidget *widget) = 0;
    virtual ~Shape() = default;
};

// 扩展抽象：圆形
class Circle : public Shape {
    int x, y, r;
public:
    Circle(int _x, int _y, int _r, std::shared_ptr<DrawAPI> api)
        : Shape(api), x(_x), y(_y), r(_r) {}
    void draw(QWidget *widget) override {
        drawApi->drawCircle(widget, x, y, r);
    }
};

// 扩展抽象：矩形
class Rectangle : public Shape {
    int x, y, w, h;
public:
    Rectangle(int _x, int _y, int _w, int _h, std::shared_ptr<DrawAPI> api)
        : Shape(api), x(_x), y(_y), w(_w), h(_h) {}
    void draw(QWidget *widget) override {
        drawApi->drawRect(widget, x, y, w, h);
    }
};

// 测试窗口
class BridgeWidget : public QWidget {
    std::shared_ptr<Shape> shape1;
    std::shared_ptr<Shape> shape2;
public:
    BridgeWidget(QWidget *parent = nullptr) : QWidget(parent) {
        auto painter = std::make_shared<PainterDraw>();
        auto svg = std::make_shared<SvgDraw>();

        // 圆形用 Painter 画在窗口
        shape1 = std::make_shared<Circle>(100, 100, 50, painter);
        // 矩形输出到 rect.svg
        shape2 = std::make_shared<Rectangle>(50, 150, 120, 80, svg);
    }

protected:
    void paintEvent(QPaintEvent *event) override {
        shape1->draw(this);  // 窗口绘制
        shape2->draw(this);  // 同时生成 rect.svg
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    BridgeWidget w;
    w.resize(300, 300);
    w.show();
    return app.exec();
}
```
实现效果：
> - 窗口中会显示一个红色的圆形（QPainter 绘制）。
>  - 同时会在程序目录下生成 rect.svg 文件，里面保存一个蓝色矩形（SvgDraw 绘制）。
>  - Shape（抽象部分） 和 DrawAPI（实现部分） 解耦。
>  - 想要支持新的绘制方式（比如 OpenGLDraw），只需要新建一个实现类，不影响 Shape。
