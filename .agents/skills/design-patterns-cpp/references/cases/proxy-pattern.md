@[toc]
## 一、代理模式的定义
&emsp;&emsp;代理模式是一种结构型设计模式，它为某个对象提供一个代理或占位符，以控制对这个对象的访问。简单来说代理对象在客户端和目标对象之间起到中介作用，客户端并不会直接操作目标对象，而是通过代理对象间接访问。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ca1f4495e13340c6a1f1ccfdfd9c97d1.png)
**代理模式常用于以下情况：**
>  1. 远程代理：为远程对象（在不同地址空间、服务器上）提供本地代理，隐藏远程访问的复杂性。
>  2. 虚拟代理：在需要消耗大量资源时，先用代理延迟对象的创建或初始化。（如：大图片的延迟加载）
>  3. 保护代理：控制不同用户对对象的访问权限。（如：文件系统访问控制）
>  4. 智能代理：在访问对象时附加额外操作（如缓存、日志、计数、线程安全控制）。

**代理模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e31b616a9d97458eb0237236dc99d111.png)

## 二、实例分析
**问题：**
为什么要控制对于某个对象的访问呢？ 举个例子： 有这样一个消耗大量系统资源的巨型对象， 你只是偶尔需要使用它， 并非总是需要。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/95c03d4c0bc045f79fec2f62dbeada93.png)
你可以实现延迟初始化： 在实际有需要时再创建该对象。 对象的所有客户端都要执行延迟初始代码。 不幸的是， 这很可能会带来很多重复代码。

在理想情况下， 我们希望将代码直接放入对象的类中， 但这并非总是能实现： 比如类可能是第三方封闭库的一部分。

**解决方案：**
代理模式建议新建一个与原服务对象接口相同的代理类， 然后更新应用以将代理对象传递给所有原始对象客户端。 代理类接收到客户端请求后会创建实际的服务对象， 并将所有工作委派给它。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9d4ed4884d44407988f4b7ee6c449d03.png)
这有什么好处呢？ 如果需要在类的主要业务逻辑前后执行一些工作， 你无需修改类就能完成这项工作。 由于代理实现的接口与原类相同， 因此你可将其传递给任何一个使用实际服务对象的客户端。

**真实世界类比：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2d4b00ff4def42738af18e28c61b840d.png)
信用卡是银行账户的代理， 银行账户则是一大捆现金的代理。 它们都实现了同样的接口， 均可用于进行支付。 消费者会非常满意， 因为不必随身携带大量现金； 商店老板同样会十分高兴， 因为交易收入能以电子化的方式进入商店的银行账户中， 无需担心存款时出现现金丢失或被抢劫的情况。

代码实现：
>  - 抽象接口：Payment，定义统一的 pay(amount) 方法。
>  - 真实主题（RealSubject）：Cash，表示真正的支付方式（现金支付）
>  - 代理（Proxy）：CreditCard，作为银行账户的代理，内部持有一个 BankAccount（或 Cash）对象，通过代理实现支付。
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 抽象接口
class Payment {
public:
    virtual void pay(double amount) = 0;
    virtual ~Payment() = default;
};

// 真实主题：现金支付
class Cash : public Payment {
public:
    void pay(double amount) override {
        cout << "支付现金: " << amount << " 元" << endl;
    }
};

// 银行账户（被代理对象）
class BankAccount {
public:
    void withdraw(double amount) {
        cout << "从银行账户扣款: " << amount << " 元" << endl;
    }
};

// 代理：信用卡支付
class CreditCard : public Payment {
private:
    BankAccount* account; // 代理对象内部持有真实对象
public:
    CreditCard(BankAccount* acc) : account(acc) {}

    void pay(double amount) override {
        cout << "使用信用卡支付: " << amount << " 元" << endl;
        account->withdraw(amount); // 实际通过银行账户扣款
    }
};

// 客户端
int main() {
    // 使用现金支付
    unique_ptr<Payment> cashPayment = make_unique<Cash>();
    cashPayment->pay(100);

    // 使用信用卡支付（代理）
    BankAccount bankAcc;
    unique_ptr<Payment> creditPayment = make_unique<CreditCard>(&bankAcc);
    creditPayment->pay(200);

    return 0;
}
```
## 三、示例代码
**示例一：**
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 抽象主题
class Subject {
public:
    virtual void request() = 0;
    virtual ~Subject() = default;
};

// 真实主题
class RealSubject : public Subject {
public:
    void request() override {
        cout << "RealSubject: Handling request." << endl;
    }
};

// 代理类
class Proxy : public Subject {
private:
    unique_ptr<RealSubject> realSubject;
public:
    void request() override {
        if (!realSubject) {
            cout << "Proxy: Creating RealSubject." << endl;
            realSubject = make_unique<RealSubject>();
        }
        cout << "Proxy: Delegating request to RealSubject." << endl;
        realSubject->request();
    }
};

// 客户端
int main() {
    Proxy proxy;
    proxy.request(); // 第一次访问，创建真实对象
    proxy.request(); // 第二次访问，直接使用已有对象
    return 0;
}
```

**示例二：**
数据库模块（QSqlQuery）+ 缓存代理的实际应用示例

**设计思路：**
>  - 接口类 IDatabase：定义通用的查询接口
>  - 真实类 RealDatabase：直接使用 QSqlDatabase + QSqlQuery 访问数据库。
>  - 代理类 DatabaseProxy：在真实查询前先检查缓存，缓存命中就直接返回结果，避免重复访问数据库。
```cpp
#include <QCoreApplication>
#include <QtSql/QSqlDatabase>
#include <QtSql/QSqlQuery>
#include <QtSql/QSqlError>
#include <QDebug>
#include <QVariant>
#include <QMap>
#include <QStringList>

// 抽象接口
class IDatabase {
public:
    virtual QStringList query(const QString& sql) = 0;
    virtual ~IDatabase() = default;
};

// 真实数据库类
class RealDatabase : public IDatabase {
private:
    QSqlDatabase db;
public:
    RealDatabase() {
        // 连接 SQLite 数据库
        db = QSqlDatabase::addDatabase("QSQLITE");
        db.setDatabaseName(":memory:"); // 内存数据库，演示用
        if (!db.open()) {
            qWarning() << "Database error:" << db.lastError().text();
        }

        // 初始化数据
        QSqlQuery q;
        q.exec("CREATE TABLE users (id INT, name TEXT)");
        q.exec("INSERT INTO users VALUES (1, 'Alice')");
        q.exec("INSERT INTO users VALUES (2, 'Bob')");
    }

    QStringList query(const QString& sql) override {
        QSqlQuery q;
        QStringList result;

        if (!q.exec(sql)) {
            qWarning() << "SQL error:" << q.lastError().text();
            return result;
        }

        while (q.next()) {
            result << q.value(0).toString(); // 只取第一列
        }

        qDebug() << "[RealDatabase] Executing:" << sql;
        return result;
    }
};

// 代理类，带缓存
class DatabaseProxy : public IDatabase {
private:
    RealDatabase* realDb;
    QMap<QString, QStringList> cache; // SQL -> 查询结果
public:
    DatabaseProxy() : realDb(nullptr) {}

    QStringList query(const QString& sql) override {
        // 缓存检查
        if (cache.contains(sql)) {
            qDebug() << "[Proxy] Cache hit for:" << sql;
            return cache[sql];
        }

        // 创建真实对象
        if (!realDb) {
            realDb = new RealDatabase();
        }

        qDebug() << "[Proxy] Cache miss, querying DB:" << sql;
        QStringList result = realDb->query(sql);

        // 存入缓存
        cache[sql] = result;
        return result;
    }

    ~DatabaseProxy() {
        delete realDb;
    }
};

// 测试
int main(int argc, char *argv[]) {
    QCoreApplication a(argc, argv);

    IDatabase* db = new DatabaseProxy();

    qDebug() << db->query("SELECT name FROM users WHERE id=1");
    qDebug() << db->query("SELECT name FROM users WHERE id=2");
    qDebug() << db->query("SELECT name FROM users WHERE id=1"); // 命中缓存

    delete db;
    return 0;
}
```
