@[toc]
## 一、模式定义
&emsp;&emsp;在软件开发中，我们常常需要保存对象某个时刻的状态，以后可以恢复。比如文本编辑器中的 撤销 / 重做 功能。如果直接暴露对象的内部实现来保存和恢复，会破坏封装性。备忘录模式是一种行为设计模式，它在不破坏对象封装的前提下，捕获并保存对象的内部状态，以便在以后恢复。

&emsp;&emsp;备忘录类没有声明任何公有的成员变量、 获取器 （getter） 和设置器， 因此没有对象可以修改其内容。 备忘录与创建自己的编辑器相连接， 这使得备忘录能够通过编辑器对象的设置器传递数据， 恢复与其相连接的编辑器的状态。 由于备忘录与特定的编辑器对象相连接， 程序可以使用中心化的撤销栈实现对多个独立编辑器窗口的支持。

**主要角色：**
> 1. Originator（发起人）：负责创建一个备忘录，用于记录当前状态。也可以使用备忘录恢复以前的状态。
>  2. Memento（备忘录）：存储发起人的内部状态。不允许外部直接访问。
>  3. Caretaker（管理者）：负责保存备忘录，但不能修改备忘录内容。
>  
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/8225939ec7534561a05a1f73831938b6.png)
**备忘录模式结构：**
**基于嵌套类的实现**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0c19ec25e95d4b2e84082dcd06ef7f03.png)
**封装更加严格的实现**
如果你不想让其他类有任何机会通过备忘录来访问原发器的状态， 那么还有另一种可用的实现方式。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/54fdc50e49c64e0c9f3085dc018802cb.png)
## 二、实例分析
**问题：**
假如你正在开发一款文字编辑器应用程序。 除了简单的文字编辑功能外， 编辑器中还要有设置文本格式和插入内嵌图片等功能。

后来， 你决定让用户能撤销施加在文本上的任何操作。 这项功能在过去几年里变得十分普遍， 因此用户期待任何程序都有这项功能。 你选择采用直接的方式来实现该功能： 程序在执行任何操作前会记录所有的对象状态， 并将其保存下来。 当用户此后需要撤销某个操作时， 程序将从历史记录中获取最近的快照， 然后使用它来恢复所有对象的状态。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f1e4c07a3622454799a5a5938d5ca332.png)
让我们来思考一下这些状态快照。 首先， 到底该如何生成一个快照呢？ 很可能你会需要遍历对象的所有成员变量并将其数值复制保存。 但只有当对象对其内容没有严格访问权限限制的情况下， 你才能使用该方式。 不过很遗憾， 绝大部分对象会使用私有成员变量来存储重要数据， 这样别人就无法轻易查看其中的内容。

现在我们暂时忽略这个问题， 假设对象都像嬉皮士一样： 喜欢开放式的关系并会公开其所有状态。 尽管这种方式能够解决当前问题， 让你可随时生成对象的状态快照， 但这种方式仍存在一些严重问题。 未来你可能会添加或删除一些成员变量。 这听上去很简单， 但需要对负责复制受影响对象状态的类进行更改。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f32e67cb877b43b986c7b9172691c024.png)
还有更多问题。 让我们来考虑编辑器 （Editor） 状态的实际 “快照”， 它需要包含哪些数据？ 至少必须包含实际的文本、 光标坐标和当前滚动条位置等。 你需要收集这些数据并将其放入特定容器中， 才能生成快照。

你很可能会将大量的容器对象存储在历史记录列表中。 这样一来， 容器最终大概率会成为同一个类的对象。 这个类中几乎没有任何方法， 但有许多与编辑器状态一一对应的成员变量。 为了让其他对象能保存或读取快照， 你很可能需要将快照的成员变量设为公有。 无论这些状态是否私有， 其都将暴露一切编辑器状态。 其他类会对快照类的每个小改动产生依赖， 除非这些改动仅存在于私有成员变量或方法中， 而不会影响外部类。

我们似乎走进了一条死胡同： 要么会暴露类的所有内部细节而使其过于脆弱； 要么会限制对其状态的访问权限而无法生成快照。 那么， 我们还有其他方式来实现 “撤销” 功能吗？

**解决方案：**
我们刚才遇到的所有问题都是封装 “破损” 造成的。 一些对象试图超出其职责范围的工作。 由于在执行某些行为时需要获取数据， 所以它们侵入了其他对象的私有空间， 而不是让这些对象来完成实际的工作。

备忘录模式将创建状态快照 （Snapshot） 的工作委派给实际状态的拥有者原发器 （Originator） 对象。 这样其他对象就不再需要从 “外部” 复制编辑器状态了， 编辑器类拥有其状态的完全访问权， 因此可以自行生成快照。

模式建议将对象状态的副本存储在一个名为备忘录 （Memento） 的特殊对象中。 除了创建备忘录的对象外， 任何对象都不能访问备忘录的内容。 其他对象必须使用受限接口与备忘录进行交互， 它们可以获取快照的元数据 （创建时间和操作名称等）， 但不能获取快照中原始对象的状态。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2e507c61fcf041f184319c5fb0751262.png)
这种限制策略允许你将备忘录保存在通常被称为负责人 （Caretakers） 的对象中。 由于负责人仅通过受限接口与备忘录互动， 故其无法修改存储在备忘录内部的状态。 同时， 原发器拥有对备忘录所有成员的访问权限， 从而能随时恢复其以前的状态。

在文字编辑器的示例中， 我们可以创建一个独立的历史 （History） 类作为负责人。 编辑器每次执行操作前， 存储在负责人中的备忘录栈都会生长。 你甚至可以在应用的 UI 中渲染该栈， 为用户显示之前的操作历史。

当用户触发撤销操作时， 历史类将从栈中取回最近的备忘录， 并将其传递给编辑器以请求进行回滚。 由于编辑器拥有对备忘录的完全访问权限， 因此它可以使用从备忘录中获取的数值来替换自身的状态。
## 三、示例代码
**示例一：**
场景设计：
>  - 一个 QTextEdit 输入框。
>  - 两个按钮：撤销、恢复。
>  - 使用 备忘录模式 管理文本的历史记录。
```cpp
#include <QApplication>
#include <QWidget>
#include <QTextEdit>
#include <QPushButton>
#include <QVBoxLayout>
#include <QHBoxLayout>
#include <stack>

// ---------- 备忘录 ----------
class Memento {
private:
    QString state;
public:
    Memento(const QString& s) : state(s) {}
    QString getState() const { return state; }
};

// ---------- 发起人 ----------
class TextEditor : public QTextEdit {
    Q_OBJECT
public:
    using QTextEdit::QTextEdit; // 继承构造

    Memento save() {
        return Memento(toPlainText());
    }

    void restore(const Memento& m) {
        this->setPlainText(m.getState());
    }
};

// ---------- 管理者 ----------
class Caretaker {
private:
    std::stack<Memento> undoStack;
    std::stack<Memento> redoStack;
public:
    void save(const Memento& m) {
        undoStack.push(m);
        // 新操作后清空 redo 栈
        while (!redoStack.empty()) redoStack.pop();
    }

    bool canUndo() const { return !undoStack.empty(); }
    bool canRedo() const { return !redoStack.empty(); }

    Memento undo(const Memento& current) {
        if (undoStack.empty()) return current;
        Memento last = undoStack.top();
        undoStack.pop();
        redoStack.push(current);
        return last;
    }

    Memento redo(const Memento& current) {
        if (redoStack.empty()) return current;
        Memento next = redoStack.top();
        redoStack.pop();
        undoStack.push(current);
        return next;
    }
};

// ---------- 主窗口 ----------
class EditorWindow : public QWidget {
    Q_OBJECT
private:
    TextEditor* editor;
    QPushButton* undoBtn;
    QPushButton* redoBtn;
    Caretaker caretaker;

public:
    EditorWindow() {
        QVBoxLayout* layout = new QVBoxLayout(this);

        editor = new TextEditor();
        layout->addWidget(editor);

        QHBoxLayout* btnLayout = new QHBoxLayout();
        undoBtn = new QPushButton("撤销");
        redoBtn = new QPushButton("恢复");
        btnLayout->addWidget(undoBtn);
        btnLayout->addWidget(redoBtn);
        layout->addLayout(btnLayout);

        // 初始化时保存一次
        caretaker.save(editor->save());

        // 文本变化时保存状态
        connect(editor, &QTextEdit::textChanged, this, [=]() {
            caretaker.save(editor->save());
            updateButtons();
        });

        // 撤销
        connect(undoBtn, &QPushButton::clicked, this, [=]() {
            editor->restore(caretaker.undo(editor->save()));
            updateButtons();
        });

        // 恢复
        connect(redoBtn, &QPushButton::clicked, this, [=]() {
            editor->restore(caretaker.redo(editor->save()));
            updateButtons();
        });

        updateButtons();
    }

    void updateButtons() {
        undoBtn->setEnabled(caretaker.canUndo());
        redoBtn->setEnabled(caretaker.canRedo());
    }
};

// ---------- main ----------
int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    EditorWindow window;
    window.setWindowTitle("备忘录模式 - 文本编辑器");
    window.resize(400, 300);
    window.show();

    return app.exec();
}
```

**示例二：**
严格封装版：备忘录模式实现
```cpp
#include <iostream>
#include <vector>
#include <memory>
using namespace std;

// ==================== 抽象接口 ====================
class Memento {
public:
    virtual void restore() = 0; // 允许恢复
    virtual ~Memento() = default;
};

class Originator {
public:
    virtual shared_ptr<Memento> save() = 0;   // 创建备忘录
    virtual void setState(const string& s) = 0;
    virtual string getState() const = 0;
    virtual ~Originator() = default;
};

// ==================== 具体实现 ====================
class ConcreteOriginator : public Originator {
private:
    string state;
public:
    void setState(const string& s) override {
        state = s;
        cout << "Originator: set state = " << state << endl;
    }

    string getState() const override {
        return state;
    }

    // 内部类 ConcreteMemento
    class ConcreteMemento : public Memento {
    private:
        ConcreteOriginator* originator; // 关联的发起人
        string state;
    public:
        ConcreteMemento(ConcreteOriginator* o, const string& s)
            : originator(o), state(s) {}

        void restore() override {
            cout << "Memento: restoring state -> " << state << endl;
            originator->setState(state);
        }
    };

    shared_ptr<Memento> save() override {
        return make_shared<ConcreteMemento>(this, state);
    }
};

// ==================== 管理者 ====================
class Caretaker {
private:
    vector<shared_ptr<Memento>> history;
public:
    void backup(shared_ptr<Memento> m) {
        history.push_back(m);
    }

    void undo() {
        if (history.empty()) {
            cout << "Caretaker: no history." << endl;
            return;
        }
        auto m = history.back();
        history.pop_back();
        m->restore();
    }
};

// ==================== 测试 ====================
int main() {
    ConcreteOriginator originator;
    Caretaker caretaker;

    originator.setState("State #1");
    caretaker.backup(originator.save());

    originator.setState("State #2");
    caretaker.backup(originator.save());

    originator.setState("State #3");
    cout << "Current state: " << originator.getState() << endl;

    // 撤销
    caretaker.undo();
    cout << "After undo: " << originator.getState() << endl;

    caretaker.undo();
    cout << "After second undo: " << originator.getState() << endl;

    return 0;
}
```
