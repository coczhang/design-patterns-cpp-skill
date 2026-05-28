@[toc]
## 一、模板方法模式介绍
&emsp;&emsp;模板方法模式定义在一个抽象类中，规定了一个操作的执行流程（算法骨架），而将其中的一些步骤延迟到子类中去实现。父类控制整体流程（不会改变）；子类决定某些步骤的具体实现（可扩展）。核心思想就是固定算法框架，变化的部分交给子类去实现。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/88d1cc5636b04980a6987d9564efa973.png)
**模板方法模式结构：**
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/1cd87a658fee4c1687206314a1d087fa.png)
## 二、实例分析
**问题：**
假如你正在开发一款分析公司文档的数据挖掘程序。 用户需要向程序输入各种格式 （PDF、 DOC 或 CSV）的文档， 程序则会试图从这些文件中抽取有意义的数据， 并以统一的格式将其返回给用户。

该程序的首个版本仅支持 DOC 文件。 在接下来的一个版本中， 程序能够支持 CSV 文件。 一个月后， 你 “教会” 了程序从 PDF 文件中抽取数据。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/70ab2a2d365f436c8d95954882e2c5ca.png)
一段时间后， 你发现这三个类中包含许多相似代码。 尽管这些类处理不同数据格式的代码完全不同， 但数据处理和分析的代码却几乎完全一样。 如果能在保持算法结构完整的情况下去除重复代码， 这难道不是一件很棒的事情吗？

还有另一个与使用这些类的客户端代码相关的问题： 客户端代码中包含许多条件语句， 以根据不同的处理对象类型选择合适的处理过程。 如果所有处理数据的类都拥有相同的接口或基类， 那么你就可以去除客户端代码中的条件语句， 转而使用多态机制来在处理对象上调用函数。

**解决方案：**
模板方法模式建议将算法分解为一系列步骤， 然后将这些步骤改写为方法， 最后在 “模板方法” 中依次调用这些方法。 步骤可以是 抽象的， 也可以有一些默认的实现。 为了能够使用算法， 客户端需要自行提供子类并实现所有的抽象步骤。 如有必要还需重写一些步骤 （但这一步中不包括模板方法自身）。

让我们考虑如何在数据挖掘应用中实现上述方案。 我们可为图中的三个解析算法创建一个基类， 该类将定义调用了一系列不同文档处理步骤的模板方法。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7e7807ef9b504179bebab2cec02706d3.png)
首先， 我们将所有步骤声明为 抽象类型， 强制要求子类自行实现这些方法。 在我们的例子中， 子类中已有所有必要的实现， 因此我们只需调整这些方法的签名， 使之与超类的方法匹配即可。

现在， 让我们看看如何去除重复代码。 对于不同的数据格式， 打开和关闭文件以及抽取和解析数据的代码都不同， 因此无需修改这些方法。 但分析原始数据和生成报告等其他步骤的实现方式非常相似， 因此可将其提取到基类中， 以让子类共享这些代码。

正如你所看到的那样， 我们有两种类型的步骤：
>  - 抽象步骤必须由各个子类来实现
>  - 可选步骤已有一些默认实现， 但仍可在需要时进行重写

还有另一种名为钩子的步骤。 钩子是内容为空的可选步骤。 即使不重写钩子， 模板方法也能工作。 钩子通常放置在算法重要步骤的前后， 为子类提供额外的算法扩展点。

不同的数据格式（DOC、CSV、PDF） 都需要执行类似的处理流程：
>  1. 打开文件
>  2. 提取原始数据
>  3. 解析数据
>  4. 分析数据
>  5. 生成报告
>  6. 关闭文件

不同点：每种文件格式提取/解析的方法不同
相同点：文件打开/关闭、分析、报告生成的逻辑相同

**UML 类图：**
```cpp
          ┌───────────────────────┐
          │      DataMiner        │  <-- 抽象类，定义算法骨架
          │-----------------------│
          │ + mine(path)          │  <-- 模板方法
          │ + openFile(path)      │
          │ + closeFile(file)     │
          │ + analyzeData(data)   │
          │ + sendReport(result)  │
          │ # extractData(file)   │  <-- 抽象方法
          │ # parseData(raw)      │  <-- 抽象方法
          └─────────▲────────────┘
                    │
 ┌──────────────────┼──────────────────┐
 │                  │                  │
 │                  │                  │
▼                  ▼                  ▼
DocDataMiner   CSVDataMiner       PDFDataMiner
```

**示例代码：**

```cpp
#include <iostream>
#include <string>
using namespace std;

// =============== 抽象基类 ===============
class DataMiner {
public:
    // 模板方法：固定算法骨架
    void mine(const string& path) {
        string file = openFile(path);
        string rawData = extractData(file);
        string data = parseData(rawData);
        string analysis = analyzeData(data);
        sendReport(analysis);
        closeFile(file);
    }

protected:
    // 公共部分
    string openFile(const string& path) {
        cout << "打开文件: " << path << endl;
        return "file_handle";
    }
    void closeFile(const string& file) {
        cout << "关闭文件: " << file << endl;
    }
    string analyzeData(const string& data) {
        cout << "分析数据..." << endl;
        return "分析结果";
    }
    void sendReport(const string& analysis) {
        cout << "发送报告: " << analysis << endl;
    }

    // 需要子类实现的部分
    virtual string extractData(const string& file) = 0;
    virtual string parseData(const string& rawData) = 0;
};

// =============== 子类：DOC ===============
class DocDataMiner : public DataMiner {
protected:
    string extractData(const string& file) override {
        cout << "提取 DOC 数据..." << endl;
        return "doc_raw";
    }
    string parseData(const string& rawData) override {
        cout << "解析 DOC 数据..." << endl;
        return "doc_data";
    }
};

// =============== 子类：CSV ===============
class CsvDataMiner : public DataMiner {
protected:
    string extractData(const string& file) override {
        cout << "提取 CSV 数据..." << endl;
        return "csv_raw";
    }
    string parseData(const string& rawData) override {
        cout << "解析 CSV 数据..." << endl;
        return "csv_data";
    }
};

// =============== 子类：PDF ===============
class PdfDataMiner : public DataMiner {
protected:
    string extractData(const string& file) override {
        cout << "提取 PDF 数据..." << endl;
        return "pdf_raw";
    }
    string parseData(const string& rawData) override {
        cout << "解析 PDF 数据..." << endl;
        return "pdf_data";
    }
};

// =============== 测试 ===============
int main() {
    DocDataMiner docMiner;
    CsvDataMiner csvMiner;
    PdfDataMiner pdfMiner;

    cout << "\n=== DOC 数据挖掘 ===" << endl;
    docMiner.mine("file.doc");

    cout << "\n=== CSV 数据挖掘 ===" << endl;
    csvMiner.mine("file.csv");

    cout << "\n=== PDF 数据挖掘 ===" << endl;
    pdfMiner.mine("file.pdf");

    return 0;
}
```

## 三、示例代码
**示例一：**
假设我们要实现一个“数据导入”的流程：
>  1. 读取数据
>  2. 解析数据
>  3. 保存数据

不同数据源（CSV、JSON、XML）处理方式不同。
```cpp
#include <iostream>
using namespace std;

// 抽象类：模板方法模式
class DataImporter {
public:
    // 模板方法：定义算法骨架
    void import() {
        readData();
        parseData();
        saveData();
    }

protected:
    virtual void readData() = 0;   // 由子类实现
    virtual void parseData() = 0;  // 由子类实现

    // 公共步骤：保存数据
    void saveData() {
        cout << "保存数据到数据库..." << endl;
    }
};

// 具体实现：CSV 导入
class CsvImporter : public DataImporter {
protected:
    void readData() override {
        cout << "读取 CSV 文件..." << endl;
    }
    void parseData() override {
        cout << "解析 CSV 数据..." << endl;
    }
};

// 具体实现：JSON 导入
class JsonImporter : public DataImporter {
protected:
    void readData() override {
        cout << "读取 JSON 文件..." << endl;
    }
    void parseData() override {
        cout << "解析 JSON 数据..." << endl;
    }
};

// 具体实现：XML 导入
class XmlImporter : public DataImporter {
protected:
    void readData() override {
        cout << "读取 XML 文件..." << endl;
    }
    void parseData() override {
        cout << "解析 XML 数据..." << endl;
    }
};

// ================== 测试 ==================
int main() {
    CsvImporter csv;
    JsonImporter json;
    XmlImporter xml;

    cout << "=== CSV 导入 ===" << endl;
    csv.import();

    cout << "\n=== JSON 导入 ===" << endl;
    json.import();

    cout << "\n=== XML 导入 ===" << endl;
    xml.import();

    return 0;
}
```

**示例二：**
主窗口里有三个按钮：CSV 导入、JSON 导入、XML 导入；点击按钮后，会调用不同的导入流程，但流程的骨架由模板方法固定：
>  1. 读取数据
>  2. 解析数据
>  3. 保存数据

在界面上通过 QTextEdit 显示执行结果。

```cpp
#include <QApplication>
#include <QMainWindow>
#include <QPushButton>
#include <QTextEdit>
#include <QVBoxLayout>
#include <QWidget>

// ================= 抽象类：模板方法 =================
class DataImporter : public QObject {
    Q_OBJECT
public:
    explicit DataImporter(QTextEdit* output, QObject* parent = nullptr)
        : QObject(parent), m_output(output) {}

    // 模板方法：固定流程
    void import() {
        readData();
        parseData();
        saveData();
    }

protected:
    QTextEdit* m_output;

    virtual void readData() = 0;   // 子类实现
    virtual void parseData() = 0;  // 子类实现

    void saveData() {
        m_output->append("保存数据到数据库...\n");
    }
};

// ================= CSV 导入 =================
class CsvImporter : public DataImporter {
public:
    using DataImporter::DataImporter;
protected:
    void readData() override {
        m_output->append("读取 CSV 文件...");
    }
    void parseData() override {
        m_output->append("解析 CSV 数据...");
    }
};

// ================= JSON 导入 =================
class JsonImporter : public DataImporter {
public:
    using DataImporter::DataImporter;
protected:
    void readData() override {
        m_output->append("读取 JSON 文件...");
    }
    void parseData() override {
        m_output->append("解析 JSON 数据...");
    }
};

// ================= XML 导入 =================
class XmlImporter : public DataImporter {
public:
    using DataImporter::DataImporter;
protected:
    void readData() override {
        m_output->append("读取 XML 文件...");
    }
    void parseData() override {
        m_output->append("解析 XML 数据...");
    }
};

// ================= 主窗口 =================
class MainWindow : public QMainWindow {
    Q_OBJECT
public:
    MainWindow(QWidget* parent = nullptr) : QMainWindow(parent) {
        QWidget* central = new QWidget(this);
        QVBoxLayout* layout = new QVBoxLayout(central);

        m_output = new QTextEdit(this);
        m_output->setReadOnly(true);

        QPushButton* btnCsv = new QPushButton("导入 CSV", this);
        QPushButton* btnJson = new QPushButton("导入 JSON", this);
        QPushButton* btnXml = new QPushButton("导入 XML", this);

        layout->addWidget(m_output);
        layout->addWidget(btnCsv);
        layout->addWidget(btnJson);
        layout->addWidget(btnXml);

        setCentralWidget(central);
        resize(400, 300);

        // 连接信号槽
        connect(btnCsv, &QPushButton::clicked, this, &MainWindow::onCsvClicked);
        connect(btnJson, &QPushButton::clicked, this, &MainWindow::onJsonClicked);
        connect(btnXml, &QPushButton::clicked, this, &MainWindow::onXmlClicked);
    }

private slots:
    void onCsvClicked() {
        CsvImporter importer(m_output);
        importer.import();
    }
    void onJsonClicked() {
        JsonImporter importer(m_output);
        importer.import();
    }
    void onXmlClicked() {
        XmlImporter importer(m_output);
        importer.import();
    }

private:
    QTextEdit* m_output;
};

// ================= main =================
int main(int argc, char *argv[]) {
    QApplication app(argc, argv);
    MainWindow w;
    w.show();
    return app.exec();
}
```

示例三：