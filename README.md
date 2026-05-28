# design-patterns-cpp Skill

## 如何应用

### 当前项目使用

本仓库已经把 skill 放在当前项目的 `.agents` 目录中：

```text
.agents/skills/design-patterns-cpp/
```

在当前仓库中打开 Codex 后，可以直接显式调用：

```text
使用 $design-patterns-cpp，帮我分析这个 Qt 模块应该采用什么设计模式或架构。
```

也可以用更具体的问题触发它：

```text
使用 $design-patterns-cpp，帮我设计一个多厂商 SDK 接入架构，要求包含 Adapter、Factory、Qt 生命周期和过度设计检查。
```

### 所有项目可用

如果希望所有 Codex 项目都能使用这个 skill，把整个 skill 目录复制到用户级 Codex skills 目录：

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills" | Out-Null
Copy-Item -LiteralPath ".agents\skills\design-patterns-cpp" -Destination "$env:USERPROFILE\.codex\skills\design-patterns-cpp" -Recurse -Force
```

复制完成后，新开的 Codex 会话即可在任意项目中使用：

```text
使用 $design-patterns-cpp，帮我审查这个 C++/Qt 模块是否存在过度设计。
```

如果之后更新了仓库内的 skill，再执行同一条 `Copy-Item` 命令即可覆盖用户级版本。

## Skill 作用

`design-patterns-cpp` 面向 C++17 / Qt 5.14+ / Qt 6.x 项目，帮助 Codex 在真实工程中选择、解释、实现、审查和重构设计模式。

它覆盖 GoF 23 种设计模式，并额外包含 C++/Qt 项目常用的 Simple Factory。核心目标不是机械套模式，而是先分析变化点，再判断是否需要设计模式，并明确指出过度设计风险。

典型能力包括：

- 根据需求推荐合适的设计模式。
- 判断现有代码是否适合重构为某种模式。
- 解释模式适用场景、类结构、扩展点和风险。
- 生成 C++17 / Qt 风格接口和示例代码。
- 针对 QObject 生命周期、signals/slots、RAII、智能指针给出实现建议。
- 识别过度设计，优先推荐简单可维护方案。
- 支持多厂商 SDK、多协议通信、图像处理、撤销/重做、状态管理、复杂 UI 协作等 Qt 工业软件场景。
- 使用决策评分表判断模式是否真的值得引入。
- 复用高频 C++/Qt 代码模板，降低生成代码时的风格漂移。
- 在复杂架构说明中按需输出 Mermaid 类图。
- 在真实代码重构前检查测试、QObject 生命周期、线程归属、CMake 影响和兼容性风险。
- 提供 QtTest / gtest 示例，帮助验证模式重构没有改变行为。
- 通过代码审查 smell 清单识别“该用模式”和“过度设计”两类问题。
- 按需读取本地真实案例语料，结合你的设计模式文章生成更贴近实际工程的说明和代码。
- 支持 Qt 应用架构设计，包括分层架构、Model/View、MVP/MVVM、插件架构、线程/Worker 架构和 CMake 模块边界。

## 当前优化点

当前版本在初版基础上增加了以下能力：

- 新增 `pattern-decision-rubric.md`：用 0-16 分评分表衡量实现数量、扩展概率、运行时切换、创建复杂度、耦合痛点、测试收益、生命周期/线程风险和团队维护成本。
- 新增 `qt-code-templates.md`：沉淀 Adapter + Factory、Strategy + Factory、Command + Memento、State、Mediator 等 C++/Qt 高频模板。
- 增强 `cpp-implementation-guide.md`：补充 QObject 不可复制、`QPointer`、`deleteLater()`、跨线程 signals/slots、parent-owned QObject 返回规则。
- 增强 `anti-overengineering-guide.md`：补充 Singleton 滥用、Facade 上帝类、Mediator 膨胀、Strategy 只有一个实现、Adapter 塞业务逻辑等反模式。
- 增强 `output-format-guide.md`：模式推荐输出必须包含决策分数；架构设计可按需输出 Mermaid class diagram。
- 更新 `SKILL.md`：把决策评分和 Qt 代码模板接入主工作流与 reference 路由。

## 最新增强点

新增能力包括：

- 新增 `pattern-combination-guide.md`：说明 Adapter + Factory、Strategy + Factory、Command + Memento、Facade + Adapter、Mediator + signals/slots、State + Strategy 等组合的适用边界。
- 新增 `refactoring-safety-checklist.md`：真实项目重构前检查行为保持、调用方、公开 API、signals/slots、QObject ownership、线程归属、CMake 和日志/测试。
- 新增 `test-templates.md`：提供 fake adapter、Strategy 选择测试、`QSignalSpy` 信号测试、Command undo/redo、State transition、gtest skeleton 等模板。
- 新增 `code-review-smells.md`：用于审查巨型分支、SDK 泄漏、Facade 过胖、Mediator 膨胀、QObject 所有权不清、跨线程调用等问题。
- 新增 `prompt-regression-tests.md`：提供多厂商 SDK、通信模块、撤销恢复、连接状态、拒绝过度设计等回归测试 prompt。
- 增强 `SKILL.md`：把模式组合、重构安全、测试模板、代码审查 smell 和 prompt 回归测试接入 reference 路由。
- 增强 `output-format-guide.md`：重构输出新增安全清单结果和测试计划；代码审查输出新增 Qt ownership/threading smells。

## 本地案例语料增强

本轮更新将 `设计模式/` 中的 22 篇 Markdown 案例文章接入 skill，形成“规则 + 模板 + 真实案例语料”的版本。

案例文件被复制到：

```text
.agents/skills/design-patterns-cpp/references/cases/
```

并统一改为英文 kebab-case 文件名，例如：

```text
singleton-pattern.md
factory-method-pattern.md
abstract-factory-pattern.md
adapter-pattern.md
strategy-pattern.md
state-pattern.md
visitor-pattern.md
```

新增索引文件：

```text
.agents/skills/design-patterns-cpp/references/case-index.md
```

`case-index.md` 负责把设计模式映射到对应案例文件。Codex 不会默认加载全部 22 篇文章，而是在用户要求真实案例、CSDN 文章案例、某个模式的详细示例时，先读取索引，再按需读取具体案例文件。

当前本地案例覆盖 22 种 GoF 模式，缺少 `Interpreter` 解释器模式的独立案例文章；该缺口已在 `case-index.md` 中标注。Simple Factory 仍由 skill 的工厂相关指南覆盖，但没有单独案例文章。

当 Codex 使用本地案例时，输出中应标注：

```text
Local case used: references/cases/<file-name>.md
```

## Qt 架构设计增强

新增 Qt 架构设计能力包括：

- `qt-architecture-patterns.md`：Qt 架构总览与路由，先判断架构风格，再选择 GoF 模式。
- `qt-layered-architecture.md`：UI、Presentation、Application Service、Domain、Infrastructure 分层边界。
- `qt-model-view-guide.md`：`QAbstractItemModel`、`QSortFilterProxyModel`、delegate、view 的 Model/View 架构。
- `qt-mvp-mvvm-guide.md`：Qt Widgets 中的 MVP 与 QML/Qt Quick 中的 MVVM 取舍。
- `qt-plugin-architecture.md`：`QPluginLoader`、插件接口、`Q_PLUGIN_METADATA`、元数据和版本管理。
- `qt-threading-architecture.md`：Worker Object、`QThreadPool`、`QtConcurrent`、任务队列和跨线程 signals/slots。
- `qt-module-boundaries.md`：CMake target、include 方向、模块拆分和依赖边界。

适用场景示例：

```text
使用 $design-patterns-cpp，帮我设计一个 Qt 工业客户端的分层架构。
要求包含 UI、业务服务、多厂商 SDK Adapter、数据库 Repository、后台 Worker，并说明 CMake target 如何拆分。
```

```text
使用 $design-patterns-cpp，帮我判断这个设备列表和告警列表应该用 QTableWidget 还是 QAbstractTableModel。
请给出 Model/View 架构、proxy model、delegate 和测试建议。
```

```text
使用 $design-patterns-cpp，帮我设计一个可动态加载算法插件的 Qt 插件架构。
要求包含插件接口、QPluginLoader、插件元数据、版本兼容和错误处理。
```

```text
使用 $design-patterns-cpp，帮我设计一个不阻塞 UI 的图像识别 Worker 架构。
要求说明 QThread worker-object、跨线程信号、对象销毁和取消流程。
```

## 使用方式

在 Codex 中显式调用：

```text
使用 $design-patterns-cpp，帮我分析这个多厂商 NVR 接入模块应该使用什么设计模式。
要求支持海康、大华、TP-LINK，每个厂商都有登录、预览、回放、下载，后续还会继续增加厂商。
请输出类设计、C++ 接口示例，并判断是否存在过度设计。
```

```text
使用 $design-patterns-cpp，帮我把通信模块中的 if-else 重构为更适合扩展的结构。
目前支持 TCP、UDP、HTTP、WebSocket，后续可能增加串口。使用 C++17，给出迁移步骤。
```

```text
使用 $design-patterns-cpp，解释 Strategy 和 State 的区别，并结合 Qt 连接状态管理给出示例。
```

## Reference 文件

- `pattern-selection-guide.md`：根据变化点选择模式。
- `case-index.md`：本地 22 篇设计模式案例文章索引。
- `references/cases/`：英文命名的本地设计模式案例语料目录。
- `pattern-decision-rubric.md`：用评分表判断是否应该现在引入设计模式。
- `pattern-combination-guide.md`：判断模式组合是否必要，并明确组合中各模式边界。
- `anti-overengineering-guide.md`：判断是否不该使用模式。
- `qt-pattern-examples.md`：Qt 工业软件常见场景映射。
- `qt-code-templates.md`：高频 C++/Qt 模式代码模板。
- `qt-architecture-patterns.md`：Qt 应用架构总览与 reference 路由。
- `qt-layered-architecture.md`：Qt 分层架构和 Service/Facade 边界。
- `qt-model-view-guide.md`：Qt Model/View 架构。
- `qt-mvp-mvvm-guide.md`：Qt Widgets MVP 与 QML MVVM。
- `qt-plugin-architecture.md`：Qt 插件架构。
- `qt-threading-architecture.md`：Qt 线程、Worker 和异步任务架构。
- `qt-module-boundaries.md`：CMake target、include 方向和模块边界。
- `cpp-implementation-guide.md`：C++17、RAII、智能指针、QObject 生命周期规则。
- `refactoring-safety-checklist.md`：真实项目重构前的安全检查清单。
- `test-templates.md`：QtTest / gtest 测试模板。
- `code-review-smells.md`：设计模式相关代码审查 smells。
- `creational-patterns.md`：创建型模式。
- `structural-patterns.md`：结构型模式。
- `behavioral-patterns.md`：行为型模式。
- `pattern-comparison-table.md`：相似模式对比。
- `refactoring-recipes.md`：常见重构 recipes。
- `output-format-guide.md`：推荐、重构、解释、审查、架构设计输出格式。
- `prompt-regression-tests.md`：用于验证 skill 输出质量的回归测试 prompts。
