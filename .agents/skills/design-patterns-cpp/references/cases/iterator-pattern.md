@[toc]
## 一、概念
&emsp;&emsp;迭代器模式 是一种 行为型设计模式，用于在不暴露集合对象内部结构的前提下，顺序访问集合中的元素。

换句话说：
>  - 集合类只负责数据存储；
>  - 迭代器类负责遍历集合；
>  - 使用者不需要关心集合内部是数组、链表还是树。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b004df9b8df8495eb60b064c06ee7700.png)


**迭代器模式一般包含以下角色：**
>  1. Iterator（抽象迭代器）：定义访问元素的接口，比如 first()、next()、hasNext()、currentItem()。
>  2. ConcreteIterator（具体迭代器）：实现 Iterator 接口，负责跟踪遍历位置。
>  3. Aggregate（抽象聚合类）：定义创建迭代器的接口。
>  4. ConcreteAggregate（具体聚合类）：实现 Aggregate，返回一个具体迭代器对象。

**迭代器模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7208812648e34c94989120ac1565a978.png)
## 二、实例分析
**问题：**
集合是编程中最常使用的数据类型之一。 尽管如此， 集合只是一组对象的容器而已。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6485228600834759b5e1367d39143c1f.png)
大部分集合使用简单列表存储元素。 但有些集合还会使用栈、 树、 图和其他复杂的数据结构。

无论集合的构成方式如何， 它都必须提供某种访问元素的方式， 便于其他代码使用其中的元素。 集合应提供一种能够遍历元素的方式， 且保证它不会周而复始地访问同一个元素。

如果你的集合基于列表， 那么这项工作听上去仿佛很简单。 但如何遍历复杂数据结构 （例如树） 中的元素呢？ 例如， 今天你需要使用深度优先算法来遍历树结构， 明天可能会需要广度优先算法； 下周则可能会需要其他方式 （比如随机存取树中的元素）。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b872f54499db41ad8df8b4d6dd733826.png)
不断向集合中添加遍历算法会模糊其 “高效存储数据” 的主要职责。 此外， 有些算法可能是根据特定应用订制的， 将其加入泛型集合类中会显得非常奇怪。

另一方面， 使用多种集合的客户端代码可能并不关心存储数据的方式。 不过由于集合提供不同的元素访问方式， 你的代码将不得不与特定集合类进行耦合。

**解决方案：**
迭代器模式的主要思想是将集合的遍历行为抽取为单独的迭代器对象。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d7bfe7c6ff0d4d80b9714126bcc43c0e.png)
除实现自身算法外， 迭代器还封装了遍历操作的所有细节， 例如当前位置和末尾剩余元素的数量。 因此， 多个迭代器可以在相互独立的情况下同时访问集合。

迭代器通常会提供一个获取集合元素的基本方法。 客户端可不断调用该方法直至它不返回任何内容， 这意味着迭代器已经遍历了所有元素。

所有迭代器必须实现相同的接口。 这样一来， 只要有合适的迭代器， 客户端代码就能兼容任何类型的集合或遍历算法。 如果你需要采用特殊方式来遍历集合， 只需创建一个新的迭代器类即可， 无需对集合或客户端进行修改。
## 三、示例代码
**示例一：**
```cpp
#include <iostream>
#include <vector>
#include <memory>
using namespace std;

// 抽象迭代器
class Iterator {
public:
    virtual bool hasNext() = 0;
    virtual int next() = 0;
    virtual ~Iterator() = default;
};

// 抽象聚合类
class Aggregate {
public:
    virtual unique_ptr<Iterator> createIterator() = 0;
    virtual ~Aggregate() = default;
};

// 具体聚合类
class ConcreteAggregate : public Aggregate {
private:
    vector<int> items;
public:
    void add(int item) { items.push_back(item); }
    vector<int>& getItems() { return items; }

    unique_ptr<Iterator> createIterator() override;
};

// 具体迭代器
class ConcreteIterator : public Iterator {
private:
    ConcreteAggregate& aggregate;
    size_t index = 0;
public:
    ConcreteIterator(ConcreteAggregate& agg) : aggregate(agg) {}

    bool hasNext() override {
        return index < aggregate.getItems().size();
    }

    int next() override {
        return aggregate.getItems()[index++];
    }
};

// 工厂方法实现
unique_ptr<Iterator> ConcreteAggregate::createIterator() {
    return make_unique<ConcreteIterator>(*this);
}

// 测试
int main() {
    ConcreteAggregate agg;
    agg.add(10);
    agg.add(20);
    agg.add(30);

    auto it = agg.createIterator();
    while (it->hasNext()) {
        cout << it->next() << " ";
    }
    return 0;
}
```

示例二：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/9ac518adef12494eba901c185ff5cd29.png)
上图展示的是一个社交网络遍历的迭代器模式实现，核心思想是：
>  - SocialNetwork 定义了创建迭代器的接口（好友迭代器、同事迭代器）。
>  - WeChat 是具体的社交网络实现，能返回具体迭代器。
>  - ProfileIterator 是迭代器接口，统一规定 getNext() / hasMore()。
>  - WeChatIterator 是具体的迭代器实现，负责按不同方式遍历好友或同事。
>  - Application 只依赖 SocialNetwork 和 ProfileIterator 接口，不依赖具体实现。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <memory>
using namespace std;

// ==================== Profile ====================
class Profile {
    string id;
    string email;
public:
    Profile(string i, string e) : id(move(i)), email(move(e)) {}
    string getId() const { return id; }
    string getEmail() const { return email; }
};

// ==================== 迭代器接口 ====================
class ProfileIterator {
public:
    virtual bool hasMore() = 0;
    virtual shared_ptr<Profile> getNext() = 0;
    virtual ~ProfileIterator() = default;
};

// ==================== 社交网络接口 ====================
class SocialNetwork {
public:
    virtual unique_ptr<ProfileIterator> createFriendsIterator(const string& profileId) = 0;
    virtual unique_ptr<ProfileIterator> createCoworkersIterator(const string& profileId) = 0;
    virtual ~SocialNetwork() = default;
};

// ==================== WeChat 迭代器 ====================
class WeChat; // 前向声明

class WeChatIterator : public ProfileIterator {
    WeChat* weChat;
    string profileId;
    string type;  // friends / coworkers
    size_t currentPosition = 0;
    vector<shared_ptr<Profile>> cache;

public:
    WeChatIterator(WeChat* wc, string id, string t)
        : weChat(wc), profileId(move(id)), type(move(t)) {
        lazyInit();
    }

    void lazyInit();

    bool hasMore() override {
        return currentPosition < cache.size();
    }

    shared_ptr<Profile> getNext() override {
        if (!hasMore()) return nullptr;
        return cache[currentPosition++];
    }
};

// ==================== WeChat 网络实现 ====================
class WeChat : public SocialNetwork {
    vector<shared_ptr<Profile>> profiles;
public:
    void addProfile(shared_ptr<Profile> p) { profiles.push_back(p); }

    // 简单模拟：好友=所有偶数下标，同事=所有奇数下标
    vector<shared_ptr<Profile>> requestProfiles(const string& id, const string& type) {
        vector<shared_ptr<Profile>> result;
        for (size_t i = 0; i < profiles.size(); i++) {
            if ((type == "friends" && i % 2 == 0) ||
                (type == "coworkers" && i % 2 == 1)) {
                result.push_back(profiles[i]);
            }
        }
        return result;
    }

    unique_ptr<ProfileIterator> createFriendsIterator(const string& profileId) override {
        return make_unique<WeChatIterator>(this, profileId, "friends");
    }

    unique_ptr<ProfileIterator> createCoworkersIterator(const string& profileId) override {
        return make_unique<WeChatIterator>(this, profileId, "coworkers");
    }
};

// WeChatIterator 延迟加载实现
void WeChatIterator::lazyInit() {
    if (cache.empty()) {
        cache = weChat->requestProfiles(profileId, type);
    }
}

// ==================== 应用层（发垃圾消息） ====================
class SocialSpammer {
public:
    void send(ProfileIterator& it, const string& message) {
        while (it.hasMore()) {
            auto profile = it.getNext();
            cout << "Send '" << message << "' to " << profile->getEmail() << endl;
        }
    }
};

class Application {
    SocialSpammer spammer;
    shared_ptr<SocialNetwork> network;
public:
    Application(shared_ptr<SocialNetwork> net) : network(move(net)) {}

    void sendSpamToFriends(const string& profileId) {
        auto it = network->createFriendsIterator(profileId);
        spammer.send(*it, "Hello Friend!");
    }

    void sendSpamToCoworkers(const string& profileId) {
        auto it = network->createCoworkersIterator(profileId);
        spammer.send(*it, "Hello Coworker!");
    }
};

// ==================== 测试 ====================
int main() {
    auto wechat = make_shared<WeChat>();
    wechat->addProfile(make_shared<Profile>("001", "a@example.com"));
    wechat->addProfile(make_shared<Profile>("002", "b@example.com"));
    wechat->addProfile(make_shared<Profile>("003", "c@example.com"));
    wechat->addProfile(make_shared<Profile>("004", "d@example.com"));

    Application app(wechat);
    cout << "=== 发送给好友 ===" << endl;
    app.sendSpamToFriends("001");

    cout << "=== 发送给同事 ===" << endl;
    app.sendSpamToCoworkers("001");
}
```
