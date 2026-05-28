@[toc]
## 一、享元模式的介绍
&emsp;&emsp;享元模式（Flyweight Pattern） 是一种结构型设计模式。通过共享相同对象，减少内存消耗，提高性能。 它摒弃了在每个对象中保存所有数据的方式， 通过共享多个对象所共有的相同状态， 让你能在有限的内存容量中载入更多对象。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/ad129439564f4fbea578032ef9589523.png)


**适用场景：**
当系统中存在大量相似对象时，每个对象包含许多重复的状态（数据），这会浪费大量内存。
享元模式把对象的状态分为两类：
>  - 内部状态（Intrinsic State）：可共享的，不随环境变化的部分。
>  - 外部状态（Extrinsic State）：不能共享的，依赖于具体环境的部分。
>  
通过将内部状态共享，只在需要时传入外部状态，即可避免创建大量对象。

**享元模式的角色：**
 - Flyweight（享元接口）：定义可以共享的接口，声明接收外部状态的方法。
 - ConcreteFlyweight（具体享元类）：实现享元接口，存储内部状态。
 - FlyweightFactory（享元工厂类）：管理享元对象的创建和复用。
 - Client（客户端）：负责传入外部状态，使用享元对象。

**享元模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a7d880113c06485dab5b1fcf3a9ca726.png)
## 二、实例分析
**问题：**
假如你希望在长时间工作后放松一下， 所以开发了一款简单的游戏： 玩家们在地图上移动并相互射击。 你决定实现一个真实的粒子系统， 并将其作为游戏的特色。 大量的子弹、 导弹和爆炸弹片会在整个地图上穿行， 为玩家提供紧张刺激的游戏体验。

开发完成后， 你推送提交了最新版本的程序， 并在编译游戏后将其发送给了一个朋友进行测试。 尽管该游戏在你的电脑上完美运行， 但是你的朋友却无法长时间进行游戏： 游戏总是会在他的电脑上运行几分钟后崩溃。 在研究了几个小时的调试消息记录后， 你发现导致游戏崩溃的原因是内存容量不足。 朋友的设备性能远比不上你的电脑， 因此游戏运行在他的电脑上时很快就会出现问题。

真正的问题与粒子系统有关。 每个粒子 （一颗子弹、 一枚导弹或一块弹片） 都由包含完整数据的独立对象来表示。 当玩家在游戏中鏖战进入高潮后的某一时刻， 游戏将无法在剩余内存中载入新建粒子， 于是程序就崩溃了。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/52f2e4a781b0488198f277844a7c760d.png)

**解决方案：**
仔细观察粒子Particle类， 你可能会注意到颜色 （color） 和精灵图 （sprite）这两个成员变量所消耗的内存要比其他变量多得多。 更糟糕的是， 对于所有的粒子来说， 这两个成员变量所存储的数据几乎完全一样 （比如所有子弹的颜色和精灵图都一样）。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7c0a692526be4b5fa6e3b028ed716d0b.png)
每个粒子的另一些状态 （坐标、 移动矢量和速度） 则是不同的。 因为这些成员变量的数值会不断变化。 这些数据代表粒子在存续期间不断变化的情景， 但每个粒子的颜色和精灵图则会保持不变。

对象的常量数据通常被称为内在状态， 其位于对象中， 其他对象只能读取但不能修改其数值。 而对象的其他状态常常能被其他对象 “从外部” 改变， 因此被称为外在状态。

享元模式建议不在对象中存储外在状态， 而是将其传递给依赖于它的一个特殊方法。 程序只在对象中保存内在状态， 以方便在不同情景下重用。 这些对象的区别仅在于其内在状态 （与外在状态相比， 内在状态的变体要少很多）， 因此你所需的对象数量会大大削减。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a17c0469b27d4216a544fa6538d53aff.png)
让我们回到游戏中。 假如能从粒子类中抽出外在状态， 那么我们只需三个不同的对象 （子弹、 导弹和弹片） 就能表示游戏中的所有粒子。 你现在很可能已经猜到了， 我们将这样一个仅存储内在状态的对象称为享元。


**外在状态存储：**
那么外在状态会被移动到什么地方呢？ 总得有类来存储它们， 对不对？ 在大部分情况中， 它们会被移动到容器对象中， 也就是我们应用享元模式前的聚合对象中。

在我们的例子中， 容器对象就是主要的游戏Game对象， 其会将所有粒子存储在名为 粒子particles的成员变量中。 为了能将外在状态移动到这个类中， 你需要创建多个数组成员变量来存储每个粒子的坐标、 方向矢量和速度。 除此之外， 你还需要另一个数组来存储指向代表粒子的特定享元的引用。 这些数组必须保持同步， 这样你才能够使用同一索引来获取关于某个粒子的所有数据。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/0bcde78921a947a487acc366aff5f2b4.png)
更优雅的解决方案是创建独立的情景类来存储外在状态和对享元对象的引用。 在该方法中， 容器类只需包含一个数组。

稍等！ 这样的话情景对象数量不是会和不采用该模式时的对象数量一样多吗？ 的确如此， 但这些对象要比之前小很多。 消耗内存最多的成员变量已经被移动到很少的几个享元对象中了。 现在， 一个享元大对象会被上千个情境小对象复用， 因此无需再重复存储数千个大对象的数据。

**享元与不可变性：**
由于享元对象可在不同的情景中使用， 你必须确保其状态不能被修改。 享元类的状态只能由构造函数的参数进行一次性初始化， 它不能对其他对象公开其设置器或公有成员变量。

**享元工厂：**
为了能更方便地访问各种享元， 你可以创建一个工厂方法来管理已有享元对象的缓存池。 工厂方法从客户端处接收目标享元对象的内在状态作为参数， 如果它能在缓存池中找到所需享元， 则将其返回给客户端； 如果没有找到， 它就会新建一个享元， 并将其添加到缓存池中。

你可以选择在程序的不同地方放入该函数。 最简单的选择就是将其放置在享元容器中。 除此之外， 你还可以新建一个工厂类， 或者创建一个静态的工厂方法并将其放入实际的享元类中。
## 三、示例代码
**示例一：**
在一个文本编辑器里，每个字符都是一个对象，如果每个字符都单独创建，就会消耗大量内存。实际上，每个字符的字形（内部状态） 是可以共享的，只需要保存它们在文档中的位置、大小、颜色等外部状态 即可。
```cpp
#include <iostream>
#include <map>
#include <string>
using namespace std;

// 享元接口
class Character {
public:
    virtual void display(int size, int x, int y) = 0; // 外部状态：字体大小、位置
    virtual ~Character() {}
};

// 具体享元类：具体字符
class ConcreteCharacter : public Character {
private:
    char symbol; // 内部状态：字符本身
public:
    ConcreteCharacter(char c) : symbol(c) {}

    void display(int size, int x, int y) override {
        cout << "字符: " << symbol
             << "  字体大小: " << size
             << "  位置: (" << x << "," << y << ")\n";
    }
};

// 享元工厂类：管理共享对象
class CharacterFactory {
private:
    map<char, Character*> pool; // 享元池
public:
    ~CharacterFactory() {
        for (auto& kv : pool) delete kv.second;
    }

    Character* getCharacter(char c) {
        if (pool.find(c) == pool.end()) {
            pool[c] = new ConcreteCharacter(c);
        }
        return pool[c];
    }
};

// 客户端
int main() {
    CharacterFactory factory;

    Character* c1 = factory.getCharacter('A');
    Character* c2 = factory.getCharacter('B');
    Character* c3 = factory.getCharacter('A'); // 复用已有对象

    // 显示字符（外部状态由客户端传入）
    c1->display(12, 10, 20);
    c2->display(14, 15, 25);
    c3->display(16, 50, 100);

    return 0;
}
```
**注意：虽然创建了三个字符，但实际上 'A' 只创建了一次，第二次复用，节省了内存。**

**示例二：**
使用用五子棋（棋盘游戏）举例实现享元模式

**场景分析：**
>  - 在五子棋中：棋子分为黑子和白子两种。
>  - 每颗棋子的颜色可以共享（内部状态 Intrinsic State）。
>  - 棋子的位置 (x, y) 每次都不同（外部状态 Extrinsic State）。
>  
因此，整个棋盘上无论下多少颗棋子，实际上只需要两个享元对象（黑子、白子）。

```cpp
#include <iostream>
#include <map>
#include <string>
using namespace std;

// 抽象享元类：棋子
class ChessPiece {
public:
    virtual void draw(int x, int y) = 0; // 外部状态：棋子位置
    virtual ~ChessPiece() {}
};

// 具体享元类：黑子
class BlackPiece : public ChessPiece {
public:
    void draw(int x, int y) override {
        cout << "黑子落在位置 (" << x << "," << y << ")\n";
    }
};

// 具体享元类：白子
class WhitePiece : public ChessPiece {
public:
    void draw(int x, int y) override {
        cout << "白子落在位置 (" << x << "," << y << ")\n";
    }
};

// 享元工厂：管理棋子对象
class PieceFactory {
private:
    map<string, ChessPiece*> pool; // 棋子池
public:
    ~PieceFactory() {
        for (auto& kv : pool) delete kv.second;
    }

    ChessPiece* getPiece(const string& color) {
        if (pool.find(color) == pool.end()) {
            if (color == "black") {
                pool[color] = new BlackPiece();
            } else if (color == "white") {
                pool[color] = new WhitePiece();
            }
        }
        return pool[color];
    }
};

// 客户端：模拟下棋
int main() {
    PieceFactory factory;

    ChessPiece* black = factory.getPiece("black");
    ChessPiece* white = factory.getPiece("white");

    // 下棋（外部状态：位置由客户端传入）
    black->draw(3, 3);
    white->draw(4, 4);
    black->draw(5, 5);
    white->draw(6, 6);
    black->draw(7, 7);

    return 0;
}
```
