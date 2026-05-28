@[toc]
## 一、访问者模式的介绍
&emsp;&emsp;访问者模式是一种行为型设计模式，它将数据结构与对数据的操作分离。通过在不改变数据结构的前提下，定义作用于这些数据结构的新操作。

通俗点说：
>  - 数据结构固定（比如一堆不同类型的对象），但是可能会有很多种不同的操作需要对这些对象进行处理。
>  - 与其在类中不断增加新方法，不如把操作抽出来，放到“访问者”对象中，这样类结构和操作逻辑就解耦了。

**主要角色：**
>  1. Visitor（访问者接口）：定义对数据结构中不同元素访问的操作接口。
>  2. ConcreteVisitor（具体访问者）：实现不同的访问操作。
>  3. Element（元素接口）：声明接受访问者的方法 accept(Visitor v)。
>  4. ConcreteElement（具体元素）：实现 accept 方法，把自己传递给访问者。
>  5. ObjectStructure（对象结构）：维护一组元素对象，并且可以让访问者访问这些元素。

**访问者模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/91b04ad7e29745f5a0ace38168a786e8.png)
## 二、实例分析
**问题：**
假如你的团队开发了一款能够使用巨型图像中地理信息的应用程序。 图像中的每个节点既能代表复杂实体 （例如一座城市）， 也能代表更精细的对象 （例如工业区和旅游景点等）。 如果节点代表的真实对象之间存在公路， 那么这些节点就会相互连接。 在程序内部， 每个节点的类型都由其所属的类来表示， 每个特定的节点则是一个对象。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/79cfced967bb4f3f9c0242eb9a5d1416.png)
一段时间后， 你接到了实现将图像导出到 XML 文件中的任务。 这些工作最初看上去非常简单。 你计划为每个节点类添加导出函数， 然后递归执行图像中每个节点的导出函数。 解决方案简单且优雅： 使用多态机制可以让导出方法的调用代码不会和具体的节点类相耦合。

但你不太走运， 系统架构师拒绝批准对已有节点类进行修改。 他认为这些代码已经是产品了， 不想冒险对其进行修改， 因为修改可能会引入潜在的缺陷。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b40d64d18e184288a8b1976195fa6381.png)
此外， 他还质疑在节点类中包含导出 XML 文件的代码是否有意义。 这些类的主要工作是处理地理数据。 导出 XML 文件的代码放在这里并不合适。

还有另一个原因， 那就是在此项任务完成后， 营销部门很有可能会要求程序提供导出其他类型文件的功能， 或者提出其他奇怪的要求。 这样你很可能会被迫再次修改这些重要但脆弱的类。

**解决方案：**
访问者模式建议将新行为放入一个名为访问者的独立类中， 而不是试图将其整合到已有类中。 现在， 需要执行操作的原始对象将作为参数被传递给访问者中的方法， 让方法能访问对象所包含的一切必要数据。

如果现在该操作能在不同类的对象上执行会怎么样呢？ 比如在我们的示例中， 各节点类导出 XML 文件的实际实现很可能会稍有不同。 因此， 访问者类可以定义一组 （而不是一个） 方法， 且每个方法可接收不同类型的参数，

但我们究竟应该如何调用这些方法 （尤其是在处理整个图像方面） 呢？ 这些方法的签名各不相同， 因此我们不能使用多态机制。 为了可以挑选出能够处理特定对象的访问者方法， 我们需要对它的类进行检查。 这是不是听上去像个噩梦呢？

你可能会问， 我们为什么不使用方法重载呢？ 就是使用相同的方法名称， 但它们的参数不同。 不幸的是， 即使我们的编程语言 （例如 Java 和 C#） 支持重载也不行。 由于我们无法提前知晓节点对象所属的类， 所以重载机制无法执行正确的方法。 方法会将节点基类作为输入参数的默认类型。

但是， 访问者模式可以解决这个问题。 它使用了一种名为双分派的技巧， 不使用累赘的条件语句也可下执行正确的方法。 与其让客户端来选择调用正确版本的方法， 不如将选择权委派给作为参数传递给访问者的对象。 由于该对象知晓其自身的类， 因此能更自然地在访问者中选出正确的方法。 它们会 “接收” 一个访问者并告诉其应执行的访问者方法。

## 三、示例代码
**示例一：**
```cpp
#include <iostream>
#include <vector>
#include <memory>
using namespace std;

// 前向声明
class ConcreteElementA;
class ConcreteElementB;

// 访问者接口
class Visitor {
public:
    virtual void visit(ConcreteElementA* element) = 0;
    virtual void visit(ConcreteElementB* element) = 0;
    virtual ~Visitor() = default;
};

// 元素接口
class Element {
public:
    virtual void accept(Visitor* visitor) = 0;
    virtual ~Element() = default;
};

// 具体元素A
class ConcreteElementA : public Element {
public:
    void accept(Visitor* visitor) override {
        visitor->visit(this);
    }
    void operationA() { cout << "ElementA operation\n"; }
};

// 具体元素B
class ConcreteElementB : public Element {
public:
    void accept(Visitor* visitor) override {
        visitor->visit(this);
    }
    void operationB() { cout << "ElementB operation\n"; }
};

// 具体访问者1
class ConcreteVisitor1 : public Visitor {
public:
    void visit(ConcreteElementA* element) override {
        cout << "Visitor1 processing ElementA -> ";
        element->operationA();
    }
    void visit(ConcreteElementB* element) override {
        cout << "Visitor1 processing ElementB -> ";
        element->operationB();
    }
};

// 具体访问者2
class ConcreteVisitor2 : public Visitor {
public:
    void visit(ConcreteElementA* element) override {
        cout << "Visitor2 adds extra logic for ElementA\n";
    }
    void visit(ConcreteElementB* element) override {
        cout << "Visitor2 adds extra logic for ElementB\n";
    }
};

// 对象结构
class ObjectStructure {
    vector<shared_ptr<Element>> elements;
public:
    void add(shared_ptr<Element> element) {
        elements.push_back(element);
    }
    void accept(Visitor* visitor) {
        for (auto& e : elements) {
            e->accept(visitor);
        }
    }
};

// 测试
int main() {
    ObjectStructure os;
    os.add(make_shared<ConcreteElementA>());
    os.add(make_shared<ConcreteElementB>());

    ConcreteVisitor1 v1;
    ConcreteVisitor2 v2;

    cout << "=== Use Visitor1 ===\n";
    os.accept(&v1);

    cout << "\n=== Use Visitor2 ===\n";
    os.accept(&v2);

    return 0;
}
```

**示例二：Qt 控件访问者模式**
假设我们有一组控件，需求是：
>  - 对 QLabel，把文字设置成红色。
>  - 对 QPushButton，改变按钮背景颜色。
>  - 我们通过访问者来定义这些操作，而不用在控件类里到处加逻辑。

```cpp
#include <QApplication>
#include <QLabel>
#include <QPushButton>
#include <QVBoxLayout>
#include <QWidget>
#include <QDebug>

// 前向声明
class ElementLabel;
class ElementButton;

// 访问者接口
class Visitor {
public:
    virtual void visit(ElementLabel* label) = 0;
    virtual void visit(ElementButton* button) = 0;
    virtual ~Visitor() {}
};

// 元素接口
class Element {
public:
    virtual void accept(Visitor* visitor) = 0;
    virtual ~Element() {}
};

// QLabel 的元素包装
class ElementLabel : public QLabel, public Element {
public:
    ElementLabel(const QString& text, QWidget* parent = nullptr) 
        : QLabel(text, parent) {}
    void accept(Visitor* visitor) override {
        visitor->visit(this);
    }
};

// QPushButton 的元素包装
class ElementButton : public QPushButton, public Element {
public:
    ElementButton(const QString& text, QWidget* parent = nullptr) 
        : QPushButton(text, parent) {}
    void accept(Visitor* visitor) override {
        visitor->visit(this);
    }
};

// 具体访问者1：设置控件样式
class StyleVisitor : public Visitor {
public:
    void visit(ElementLabel* label) override {
        label->setStyleSheet("color: red; font-weight: bold;");
    }
    void visit(ElementButton* button) override {
        button->setStyleSheet("background-color: lightblue; font-size: 16px;");
    }
};

// 具体访问者2：打印控件信息
class DebugVisitor : public Visitor {
public:
    void visit(ElementLabel* label) override {
        qDebug() << "Label text:" << label->text();
    }
    void visit(ElementButton* button) override {
        qDebug() << "Button text:" << button->text();
    }
};

// 演示窗口
class VisitorDemo : public QWidget {
public:
    VisitorDemo() {
        QVBoxLayout* layout = new QVBoxLayout(this);

        // 创建元素
        ElementLabel* label = new ElementLabel("我是一个标签");
        ElementButton* button = new ElementButton("我是一个按钮");

        // 添加到布局
        layout->addWidget(label);
        layout->addWidget(button);

        // 使用访问者
        StyleVisitor styleVisitor;
        DebugVisitor debugVisitor;

        // 对每个控件应用访问者
        label->accept(&styleVisitor);
        button->accept(&styleVisitor);

        label->accept(&debugVisitor);
        button->accept(&debugVisitor);
    }
};
 
int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    VisitorDemo demo;
    demo.show();

    return app.exec();
}
```

**示例三：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/745f57aa899442c389402ddbebcd7c30.png)
假如有这样一位非常希望赢得新客户的资深保险代理人。 他可以拜访街区中的每栋楼， 尝试向每个路人推销保险。 所以， 根据大楼内组织类型的不同， 他可以提供专门的保单：
>  - 如果建筑是居民楼， 他会推销医疗保险。
>  - 如果建筑是银行， 他会推销失窃保险。
>  - 如果建筑是咖啡厅， 他会推销火灾和洪水保险。

UML 对应关系：
>  - Visitor（访问者接口）：保险代理人
>  - ConcreteVisitor（具体访问者）：健康险代理人、财产险代理人等
>  - Element（元素接口）：建筑物
>  - ConcreteElement（具体元素）：住宅、银行、咖啡厅
>  - ObjectStructure（对象结构）：街区，包含一堆建筑物

```cpp
#include <iostream>
#include <vector>
#include <memory>
using namespace std;

// 前向声明
class House;
class Bank;
class Cafe;

// 访问者接口（保险代理人）
class InsuranceAgent {
public:
    virtual void visit(House* house) = 0;
    virtual void visit(Bank* bank) = 0;
    virtual void visit(Cafe* cafe) = 0;
    virtual ~InsuranceAgent() = default;
};

// 元素接口（建筑物）
class Building {
public:
    virtual void accept(InsuranceAgent* agent) = 0;
    virtual ~Building() = default;
};

// 具体元素：住宅
class House : public Building {
public:
    void accept(InsuranceAgent* agent) override {
        agent->visit(this);
    }
};

// 具体元素：银行
class Bank : public Building {
public:
    void accept(InsuranceAgent* agent) override {
        agent->visit(this);
    }
};

// 具体元素：咖啡厅
class Cafe : public Building {
public:
    void accept(InsuranceAgent* agent) override {
        agent->visit(this);
    }
};

// 具体访问者：保险代理人，推销不同保险
class ConcreteInsuranceAgent : public InsuranceAgent {
public:
    void visit(House* house) override {
        cout << "向住宅推销医疗保险。\n";
    }
    void visit(Bank* bank) override {
        cout << "向银行推销失窃保险。\n";
    }
    void visit(Cafe* cafe) override {
        cout << "向咖啡厅推销火灾和洪水保险。\n";
    }
};

// 对象结构：街区，包含多个建筑
class Street {
    vector<shared_ptr<Building>> buildings;
public:
    void add(shared_ptr<Building> b) { buildings.push_back(b); }
    void accept(InsuranceAgent* agent) {
        for (auto& b : buildings) {
            b->accept(agent);
        }
    }
};

// 测试
int main() {
    Street street;
    street.add(make_shared<House>());
    street.add(make_shared<Bank>());
    street.add(make_shared<Cafe>());

    ConcreteInsuranceAgent agent;

    cout << "保险代理人开始拜访街区:\n";
    street.accept(&agent);

    return 0;
}
```
