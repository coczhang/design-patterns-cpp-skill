@[toc]
## 一、模式简介
&emsp;&emsp;命令模式是一种行为型设计模式，它将请求封装成一个对象，从而使你能够用不同的请求、队列、日志来参数化对象。简单理解就是命令模式把“请求的发出者”和“请求的执行者”分开，中间通过一个命令对象进行解耦。

**命令模式通常包含以下角色：**
>  - Command（命令接口）：定义一个统一的接口，声明执行命令的方法，例如 execute()。
>  - ConcreteCommand（具体命令）：持有一个接收者（Receiver） 对象。调用接收者的相关方法，完成具体的操作。
>  - Receiver（接收者）：真正执行命令的对象，知道“如何做”。
>  - Invoker（调用者）：负责调用命令对象的 execute() 方法，但并不知道命令的具体实现。
>  - Client（客户端）：创建具体命令对象，并设定它的接收者，然后交给调用者。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f6d05d839d35443ebcf126c5cbf442d0.png)

**命令模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/85c81301aa224b25b3262bbeabd6ba1c.png)
## 二、实例分析
**问题：**
假如你正在开发一款新的文字编辑器， 当前的任务是创建一个包含多个按钮的工具栏， 并让每个按钮对应编辑器的不同操作。 你创建了一个非常简洁的按钮类， 它不仅可用于生成工具栏上的按钮， 还可用于生成各种对话框的通用按钮。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/1127027fdedb4601a477662dc4a2dab9.png)
尽管所有按钮看上去都很相似， 但它们可以完成不同的操作 （打开、 保存、 打印和应用等）。 你会在哪里放置这些按钮的点击处理代码呢？ 最简单的解决方案是在使用按钮的每个地方都创建大量的子类。 这些子类中包含按钮点击后必须执行的代码。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ca20a53b94604a48890d8a89f1c95696.png)
你很快就意识到这种方式有严重缺陷。 首先， 你创建了大量的子类， 当每次修改基类按钮时， 你都有可能需要修改所有子类的代码。 简单来说， GUI 代码以一种拙劣的方式依赖于业务逻辑中的不稳定代码。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/dbbb1b8962694139aeb487fdde23b416.png)
还有一个部分最难办。 复制/粘贴文字等操作可能会在多个地方被调用。 例如用户可以点击工具栏上小小的 “复制” 按钮， 或者通过上下文菜单复制一些内容， 又或者直接使用键盘上的 Ctrl+C 。

我们的程序最初只有工具栏， 因此可以使用按钮子类来实现各种不同操作。 换句话来说， ​ 复制按钮Copy­Button子类包含复制文字的代码是可行的。 在实现了上下文菜单、 快捷方式和其他功能后， 你要么需要将操作代码复制进许多个类中， 要么需要让菜单依赖于按钮， 而后者是更糟糕的选择。

**解决方案：**
优秀的软件设计通常会将关注点进行分离， 而这往往会导致软件的分层。 最常见的例子： 一层负责用户图像界面； 另一层负责业务逻辑。 GUI 层负责在屏幕上渲染美观的图形， 捕获所有输入并显示用户和程序工作的结果。 当需要完成一些重要内容时 （比如计算月球轨道或撰写年度报告）， GUI 层则会将工作委派给业务逻辑底层。

这在代码中看上去就像这样： 一个 GUI 对象传递一些参数来调用一个业务逻辑对象。 这个过程通常被描述为一个对象发送请求给另一个对象。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/fd218307462241c2b770d7030de5eca8.png)
命令模式建议 GUI 对象不直接提交这些请求。 你应该将请求的所有细节 （例如调用的对象、 方法名称和参数列表） 抽取出来组成命令类， 该类中仅包含一个用于触发请求的方法。

命令对象负责连接不同的 GUI 和业务逻辑对象。 此后， GUI 对象无需了解业务逻辑对象是否获得了请求， 也无需了解其对请求进行处理的方式。 GUI 对象触发命令即可， 命令对象会自行处理所有细节工作。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/653d653da9864699ac87c7548c7681fd.png)
下一步是让所有命令实现相同的接口。 该接口通常只有一个没有任何参数的执行方法， 让你能在不和具体命令类耦合的情况下使用同一请求发送者执行不同命令。 此外还有额外的好处， 现在你能在运行时切换连接至发送者的命令对象， 以此改变发送者的行为。

你可能会注意到遗漏的一块拼图——请求的参数。 GUI 对象可以给业务层对象提供一些参数。 但执行命令方法没有任何参数， 所以我们如何将请求的详情发送给接收者呢？ 答案是： 使用数据对命令进行预先配置， 或者让其能够自行获取数据。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d01a0d8cc5a34472a8b3f77f5841945a.png)
让我们回到文本编辑器。 应用命令模式后， 我们不再需要任何按钮子类来实现点击行为。 我们只需在 按钮Button基类中添加一个成员变量来存储对于命令对象的引用， 并在点击后执行该命令即可。

你需要为每个可能的操作实现一系列命令类， 并且根据按钮所需行为将命令和按钮连接起来。

其他菜单、 快捷方式或整个对话框等 GUI 元素都可以通过相同方式来实现。 当用户与 GUI 元素交互时， 与其连接的命令将会被执行。 现在你很可能已经猜到了， 与相同操作相关的元素将会被连接到相同的命令， 从而避免了重复代码。

最后， 命令成为了减少 GUI 和业务逻辑层之间耦合的中间层。 而这仅仅是命令模式所提供的一小部分好处！
## 三、示例代码
**示例一：**
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 命令接口
class Command {
public:
    virtual void execute() = 0;
    virtual ~Command() = default;
};

// 接收者：真正执行逻辑的类
class Light {
public:
    void on() { cout << "Light is ON\n"; }
    void off() { cout << "Light is OFF\n"; }
};

// 具体命令：打开灯
class LightOnCommand : public Command {
private:
    Light* light;
public:
    LightOnCommand(Light* l) : light(l) {}
    void execute() override { light->on(); }
};

// 具体命令：关闭灯
class LightOffCommand : public Command {
private:
    Light* light;
public:
    LightOffCommand(Light* l) : light(l) {}
    void execute() override { light->off(); }
};

// 调用者
class RemoteControl {
private:
    Command* command = nullptr;
public:
    void setCommand(Command* cmd) { command = cmd; }
    void pressButton() {
        if (command) command->execute();
    }
};

// 客户端
int main() {
    Light light;

    LightOnCommand onCmd(&light);
    LightOffCommand offCmd(&light);

    RemoteControl remote;

    // 打开灯
    remote.setCommand(&onCmd);
    remote.pressButton();

    // 关闭灯
    remote.setCommand(&offCmd);
    remote.pressButton();

    return 0;
}
```

**示例二：**
每个按钮绑定一个 命令对象。点击按钮时，命令会调用 业务逻辑（UserService） 来完成操作。

GUI 界面上功能有：
>  1. 保存（Save）
>  2. 更新（Update）
>  3. 删除（Delete）
>  4. 撤销 (Undo)
>  5. 重做 (Redo)


```cpp
#include <QApplication>
#include <QPushButton>
#include <QMenu>
#include <QAction>
#include <QDebug>
#include <QStack>
#include <QMap>

// Receiver：业务逻辑层
class UserService {
    QMap<int, QString> userData;
public:
    void save(int id, const QString& name) {
        userData[id] = name;
        qDebug() << "[Save] User:" << id << "->" << name;
    }

    void update(int id, const QString& name) {
        userData[id] = name;
        qDebug() << "[Update] User:" << id << "->" << name;
    }

    void remove(int id) {
        qDebug() << "[Delete] User removed:" << id << "->" << userData.value(id, "");
        userData.remove(id);
    }

    QString getName(int id) const {
        return userData.value(id, "");
    }
};

// Command 接口
class Command {
public:
    virtual void execute() = 0;
    virtual void undo() = 0;
    virtual ~Command() = default;
};

// SaveCommand 新增/保存用户
class SaveCommand : public Command {
    UserService* service;
    int userId;
    QString newName;
    QString oldName;
    bool existed;
public:
    SaveCommand(UserService* s, int id, const QString& name)
        : service(s), userId(id), newName(name) {}

    void execute() override {
        oldName = service->getName(userId);
        existed = !oldName.isEmpty();
        service->save(userId, newName);
    }

    void undo() override {
        if (existed)
            service->save(userId, oldName);
        else
            service->remove(userId);
    }
};

// UpdateCommand 更新用户信息
class UpdateCommand : public Command {
    UserService* service;
    int userId;
    QString newName;
    QString oldName;
public:
    UpdateCommand(UserService* s, int id, const QString& name)
        : service(s), userId(id), newName(name) {}

    void execute() override {
        oldName = service->getName(userId);
        service->update(userId, newName);
    }

    void undo() override {
        service->update(userId, oldName);
    }
};

// DeleteCommand 删除用户
class DeleteCommand : public Command {
    UserService* service;
    int userId;
    QString oldName;
public:
    DeleteCommand(UserService* s, int id)
        : service(s), userId(id) {}

    void execute() override {
        oldName = service->getName(userId);
        service->remove(userId);
    }

    void undo() override {
        if (!oldName.isEmpty()) {
            service->save(userId, oldName);
        }
    }
};

// 命令管理器 使用栈保存执行的命令，支持撤销。
class CommandManager {
    QStack<Command*> history;
    QStack<Command*> redoStack;
public:
    void executeCommand(Command* cmd) {
        cmd->execute();
        history.push(cmd);
        // 新执行的命令会清空 redo 栈
        while (!redoStack.isEmpty()) redoStack.pop();
    }

	// 把撤销的命令放进 redoStack
    void undo() {
        if (!history.isEmpty()) {
            Command* cmd = history.pop();
            cmd->undo();
            redoStack.push(cmd);
        } else {
            qDebug() << "No command to undo.";
        }
    }

	// 从 redoStack 取出命令，再次执行，并放回 history
    void redo() {
        if (!redoStack.isEmpty()) {
            Command* cmd = redoStack.pop();
            cmd->execute();
            history.push(cmd);
        } else {
            qDebug() << "No command to redo.";
        }
    }
};

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);

    UserService service;
    CommandManager manager;

    // 按钮 - Save
    QPushButton btnSave("Save User");
    QObject::connect(&btnSave, &QPushButton::clicked, [&]() {
        auto* cmd = new SaveCommand(&service, 1, "张三");
        manager.executeCommand(cmd);
    });

    // 菜单项 - Update
    QMenu menu;
    QAction* actionUpdate = menu.addAction("Update User");
    QObject::connect(actionUpdate, &QAction::triggered, [&]() {
        auto* cmd = new UpdateCommand(&service, 1, "李四");
        manager.executeCommand(cmd);
    });

    // 快捷键（模拟） - Delete
    QPushButton btnShortcut("Shortcut Delete");
    QObject::connect(&btnShortcut, &QPushButton::clicked, [&]() {
        auto* cmd = new DeleteCommand(&service, 1);
        manager.executeCommand(cmd);
    });

    // 撤销按钮
    QPushButton btnUndo("Undo");
    QObject::connect(&btnUndo, &QPushButton::clicked, [&]() {
        manager.undo();
    });

    // 重做按钮
    QPushButton btnRedo("Redo");
    QObject::connect(&btnRedo, &QPushButton::clicked, [&]() {
        manager.redo();
    });

    btnSave.show();
    btnShortcut.show();
    btnUndo.show();
    btnRedo.show();

    return app.exec();
}
```
