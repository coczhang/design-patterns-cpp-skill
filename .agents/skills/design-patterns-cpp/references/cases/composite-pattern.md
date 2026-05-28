@[toc]
## 一、组合模式的介绍
&emsp;&emsp;组合模式是一种结构型设计模式， 它将对象组合成树形结构以表示“整体-部分”的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。

换句话说：
>  - 可以把“一个对象”和“多个对象的组合”统一对待。
>  - 客户端代码不需要区分处理的是“叶子节点”还是“容器节点”。

## 二、组合模式的结构
组合模式一般包含以下角色：
>  - 抽象组件（Component）：定义了对象的通用接口，比如 add、remove、display 等。
>  - 叶子节点（Leaf）：表示最小的个体对象，没有子节点。
>  - 组合节点（Composite）：作为容器，可以包含叶子节点和其他组合节点，实现了 add、remove 等方法。
>  - 客户端（Client）：通过组件接口与对象交互，不关心是叶子还是组合。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c888d1ce94804c46b95a9f8e9fb5ee98.png)
**优点：**
>  - 统一了叶子节点和组合节点的接口，客户端使用简单。
>  - 可以方便地扩展树结构（新增叶子或组合类）。
>  - 符合“开闭原则”。

**缺点：**
> - 设计更复杂，需要抽象类/接口。
>  - 在层次较深时，可能影响性能。

**使用场景：**
>  - 文件系统（文件夹和文件）。
>  - GUI 界面（窗口中包含按钮、文本框等）。
>  - 公司组织架构（部门、员工）。
>  - 游戏中的场景树（节点包含子节点）。

## 三、实例分析
**问题：**
如果应用的核心模型能用树状结构表示， 在应用中使用组合模式才有价值。

例如， 你有两类对象： ​ 产品和盒子 。 一个盒子中可以包含多个 产品或者几个较小的盒子 。 这些小盒子中同样可以包含一些 产品或更小的 盒子 ， 以此类推。

假设你希望在这些类的基础上开发一个定购系统。 订单中可以包含无包装的简单产品， 也可以包含装满产品的盒子…… 以及其他盒子。 此时你会如何计算每张订单的总价格呢？
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/19a44e28979e49b290c5c747ac2949ff.png)
你可以尝试直接计算： 打开所有盒子， 找到每件产品， 然后计算总价。 这在真实世界中或许可行， 但在程序中， 你并不能简单地使用循环语句来完成该工作。 你必须事先知道所有 产品和 盒子的类别， 所有盒子的嵌套层数以及其他繁杂的细节信息。 因此， 直接计算极不方便， 甚至完全不可行。

**解决方案：**
组合模式建议使用一个通用接口来与产品和盒子进行交互， 并且在该接口中声明一个计算总价的方法。

那么方法该如何设计呢？ 对于一个产品， 该方法直接返回其价格； 对于一个盒子， 该方法遍历盒子中的所有项目， 询问每个项目的价格， 然后返回该盒子的总价格。 如果其中某个项目是小一号的盒子， 那么当前盒子也会遍历其中的所有项目， 以此类推， 直到计算出所有内部组成部分的价格。 你甚至可以在盒子的最终价格中增加额外费用， 作为该盒子的包装费用。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e009bfdebe4c4130a4aec9e557649d69.png)
该方式的最大优点在于你无需了解构成树状结构的对象的具体类。 你也无需了解对象是简单的产品还是复杂的盒子。 你只需调用通用接口以相同的方式对其进行处理即可。 当你调用该方法后， 对象会将请求沿着树结构传递下去。
## 三、示例代码
**假设我们要表示一个文件夹-文件系统：**
```cpp
#include <iostream>
#include <vector>
#include <memory>
using namespace std;

// 抽象组件
class FileSystemNode {
public:
    virtual void show(int depth = 0) const = 0;
    virtual ~FileSystemNode() = default;
};

// 叶子节点：文件
class File : public FileSystemNode {
    string name;
public:
    File(const string& n) : name(n) {}
    void show(int depth = 0) const override {
        cout << string(depth, '-') << "File: " << name << endl;
    }
};

// 组合节点：文件夹
class Folder : public FileSystemNode {
    string name;
    vector<shared_ptr<FileSystemNode>> children;
public:
    Folder(const string& n) : name(n) {}

    void add(shared_ptr<FileSystemNode> node) {
        children.push_back(node);
    }

    void show(int depth = 0) const override {
        cout << string(depth, '-') << "Folder: " << name << endl;
        for (auto& child : children) {
            child->show(depth + 2);
        }
    }
};

// 客户端
int main() {
    auto root = make_shared<Folder>("root");
    auto folder1 = make_shared<Folder>("docs");
    auto folder2 = make_shared<Folder>("images");

    auto file1 = make_shared<File>("a.txt");
    auto file2 = make_shared<File>("b.txt");
    auto file3 = make_shared<File>("photo.jpg");

    folder1->add(file1);
    folder1->add(file2);
    folder2->add(file3);

    root->add(folder1);
    root->add(folder2);

    root->show();
    return 0;
}

```

**Qt 组合模式示例：自定义 UI 组件树：**
我们要实现一个抽象组件 UIComponent，它可以是 控件（叶子节点） 或 容器（组合节点，包含子控件），最终在 Qt 界面上展示。
```cpp
#include <QApplication>
#include <QWidget>
#include <QPushButton>
#include <QVBoxLayout>
#include <QString>
#include <QDebug>

// 抽象组件
class UIComponent {
public:
    virtual QWidget* widget() = 0; // 返回 Qt 控件
    virtual void add(UIComponent* comp) { Q_UNUSED(comp); } // 默认不实现
    virtual ~UIComponent() = default;
};

// 叶子节点：具体控件（比如按钮）
class UIButton : public UIComponent {
    QPushButton* m_button;
public:
    UIButton(const QString& text) {
        m_button = new QPushButton(text);
    }

    QWidget* widget() override {
        return m_button;
    }
};

// 组合节点：容器（比如垂直布局的 QWidget）
class UIContainer : public UIComponent {
    QWidget* m_widget;
    QVBoxLayout* m_layout;
public:
    UIContainer(const QString& title = "") {
        m_widget = new QWidget;
        m_layout = new QVBoxLayout(m_widget);
        if (!title.isEmpty()) {
            m_widget->setWindowTitle(title);
        }
    }

    void add(UIComponent* comp) override {
        m_layout->addWidget(comp->widget());
    }

    QWidget* widget() override {
        return m_widget;
    }
};

// 客户端
int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    // 根容器
    UIContainer root("组合模式示例");

    // 子容器
    UIContainer* subContainer = new UIContainer;

    // 按钮（叶子节点）
    UIButton* btn1 = new UIButton("按钮 A");
    UIButton* btn2 = new UIButton("按钮 B");
    UIButton* btn3 = new UIButton("按钮 C");

    // 组合结构
    root.add(btn1);
    subContainer->add(btn2);
    subContainer->add(btn3);
    root.add(subContainer);

    // 显示
    root.widget()->show();

    return app.exec();
}
```
运行效果：
>  - 主窗口标题为 组合模式示例。
>  - 窗口中有一个按钮 A，以及一个子容器（里面放了按钮 B 和 C）。
>  - 客户端代码完全不区分“按钮”还是“容器”，都通过 UIComponent* 统一管理。

思路说明：
>  - UIComponent：抽象接口，类似 Component。
>  - UIButton：叶子节点（单个控件）。
>  - UIContainer：组合节点（内部有 QVBoxLayout，可以添加子节点）。
>  - 客户端 main：只调用 add，不关心是按钮还是容器。


**我们将借助组合模式帮助你在图形编辑器中实现一系列的几何图形：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/cbddb5e0af904d02b6c0347f6b18e1ee.png)
组合图形Compound­Graphic是一个容器， 它可以由多个包括容器在内的子图形构成。 组合图形与简单图形拥有相同的方法。 但是， 组合图形自身并不完成具体工作， 而是将请求递归地传递给自己的子项目， 然后 “汇总” 结果。

通过所有图形类所共有的接口， 客户端代码可以与所有图形互动。 因此， 客户端不知道与其交互的是简单图形还是组合图形。 客户端可以与非常复杂的对象结构进行交互， 而无需与组成该结构的实体类紧密耦合。

代码实现：
```cpp
#include <iostream>
#include <vector>
#include <memory>
using namespace std;

// 图形接口（Component）
class Graphic {
public:
    virtual ~Graphic() = default;
    virtual void move(int x, int y) = 0;
    virtual void draw() const = 0;
};

// 点（Leaf）
class Dot : public Graphic {
protected:
    int x, y;
public:
    Dot(int x, int y) : x(x), y(y) {}
    void move(int dx, int dy) override {
        x += dx;
        y += dy;
    }
    void draw() const override {
        cout << "Draw a Dot at (" << x << "," << y << ")\n";
    }
};

// 圆（Leaf 的扩展）
class Circle : public Dot {
    int radius;
public:
    Circle(int x, int y, int r) : Dot(x, y), radius(r) {}
    void draw() const override {
        cout << "Draw a Circle at (" << x << "," << y 
             << ") with radius " << radius << "\n";
    }
};

// 组合图形（Composite）
class CompoundGraphic : public Graphic {
    vector<shared_ptr<Graphic>> children;
public:
    void add(shared_ptr<Graphic> child) {
        children.push_back(child);
    }
    void remove(shared_ptr<Graphic> child) {
        children.erase(
            remove(children.begin(), children.end(), child), children.end());
    }
    void move(int dx, int dy) override {
        for (auto& child : children) {
            child->move(dx, dy);
        }
    }
    void draw() const override {
        cout << "Draw CompoundGraphic:\n";
        for (auto& child : children) {
            child->draw();
        }
    }
};

// 客户端：图像编辑器
class ImageEditor {
    shared_ptr<CompoundGraphic> all;
public:
    ImageEditor() {
        all = make_shared<CompoundGraphic>();
    }

    void load() {
        shared_ptr<Dot> dot = make_shared<Dot>(1, 2);
        shared_ptr<Circle> circle = make_shared<Circle>(5, 3, 10);
        all->add(dot);
        all->add(circle);
    }

    void groupSelected() {
        // 简单模拟：组合所有图形
        cout << "Group selected graphics:\n";
        all->draw();
    }
};

int main() {
    ImageEditor editor;
    editor.load();
    editor.groupSelected();

    cout << "\nMove all graphics by (2,3):\n";
    // 移动所有图形
    editor.groupSelected();
}
```
