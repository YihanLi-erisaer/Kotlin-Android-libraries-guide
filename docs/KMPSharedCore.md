# KMP 基础设施（Shared Core）选型对比表

| 维度 | 推荐库 | 状态 | 技术原理 | 构建/性能影响 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **架构/生命周期** | **Decompose** | 🔥 核心 | 独立组件树 (Component) | 中 (需编写模板代码) | 完美的生命周期管理，支持状态保存/恢复，逻辑高度解耦 | 学习曲线陡峭，样板代码较多 | **复杂项目、追求极致架构严谨性的首选** |
| **导航 (Nav)** | **Voyager** | ⭐ 主流 | 基于 Screen 模型 | 低 | API 极简，类似 Compose 原生体验，集成速度极快 | 生命周期管理深度不如 Decompose | **中小型项目、追求开发效率的首选** |
| **依赖注入 (DI)** | **Koin** | 🔥 核心 | 运行时 DSL 注入 | 低 (运行时开销) | 入门简单，社区生态最强，与 Compose 结合紧密 | 错误发生在运行时，不支持编译期检查 | **绝大多数 KMP 项目的通用首选** |
| **依赖注入 (DI)** | **kotlin-inject** | 🚀 进阶 | **编译期生成 (KSP)** | 中 (增加编译耗时) | **编译期类型安全**，零反射损耗，性能与 Dagger 相当 | 样板代码多，对新手不友好 | **大型、性能敏感型项目首选** |
| **序列化** | **Kotlinx Serialization** | 📦 基础 | 编译器插件生成 | 低 | **官方标准**，反射极小，全平台支持，体积小 | 必须使用 @Serializable 注解 | **KMP 项目唯一的、无争议的序列化方案** |
| **KV 存储** | **Multiplatform Settings** | 🔥 核心 | 平台原生 API 包装 | 极低 | 直接适配 NSUserDefaults / SharedPreferences | 功能单一，不支持复杂对象直接存储 | **存储轻量级配置、用户 Token 的默认选择** |
| **数据库 (DB)** | **SQLDelight** | 🔥 核心 | **SQL 驱动代码生成** | 中 (KSP/插件) | 真正的类型安全，SQL 代码即接口，性能极佳 | 需手写原生 SQL，有一定的学习成本 | **需要处理复杂结构化数据的项目必选** |
| **日志 (Logging)** | **Kermit** | 🔥 核心 | 静态扩展包装 | 极低 | 性能极高，支持分级，完美适配多平台输出 | 功能相对单一 | **KMP 项目的标准日志打印方案** |

---

为了确保 KMP 项目的长期健壮性和跨平台兼容性，我们制定了以下 **“架构红线”**。所有进入 `shared` 层的代码及库选型必须严格遵守这些准则。

---

## 🏛️ KMP 架构红线与开发准则

### 1. 编译期安全高于运行时反射 (Compile-time Safety Over Reflection)
在 Kotlin Native (iOS/Desktop) 环境中，反射（Reflection）不仅性能损耗巨大，且由于混淆和链接机制，极易导致运行时崩溃（NoSuchMethodException）。

*   **治理准则**：禁止在 `shared` 模块引入依赖重度反射的库。优先选择通过 **KSP (Kotlin Symbol Processing)** 或 **编译器插件** 在编译期生成代码的方案。
*   **推荐方案**：
    *   **依赖注入**：优先选择 `kotlin-inject` (编译期生成)；若使用 `Koin`，需严格测试运行时模块加载。
    *   **序列化**：必须使用 `kotlinx-serialization` (编译器插件)。
    *   **数据库**：必须使用 `SQLDelight` (SQL驱动生成代码)。
*   **目标**：确保 90% 的错误在编译阶段发现，而非在 iOS 运行时闪退。

### 2. 跨平台状态流转治理 (State Management & Flow)
跨平台代码的核心产物是 **UI 状态 (UI State)**。为了保证状态在 Android 和 iOS 之间的一致性，必须规范流转路径。

*   **原则**：状态必须是 **不可变对象 (Immutable Objects)**。禁止使用可变类（var）作为状态载体。
*   **工具选型**：统一使用 `StateFlow` 或 `SharedFlow` 作为 UI 驱动引擎。
*   **iOS 适配 (关键)**：由于 Swift 无法直接、优雅地消费 Kotlin Coroutines 中的 Flow（会丢失泛型或类型信息），**必须在基础设施中引入 SKIE 插件**。
    *   **SKIE 作用**：自动将 Kotlin Flow 桥接为 Swift 的 `AsyncSequence`，让 iOS 开发者能以原生方式（`for await`）编写代码。

### 3. 严格的平台依赖隔离 (Strict Platform Isolation)
`shared` 层是纯粹的 Kotlin 代码。引入平台私有包是导致多平台构建失败（尤其是 `LinkReleaseFramework` 阶段报错）的首要原因。

*   **禁止性要求**：`shared/commonMain` 下的代码**绝对禁止**引入 `java.*`、`javax.*` 或 `android.*` 等包名下的任何类（如 `java.util.Date`, `java.util.Calendar`）。
*   **替代方案表**：

| 原始需求 | 禁止使用 (JVM/Android) | **推荐替代 (Multiplatform)** |
| :--- | :--- | :--- |
| 时间日期 | `java.util.Date` / `Calendar` | **`kotlinx-datetime`** |
| 集合操作 | 部分 `java.util.*` 特有 API | **Kotlin 标准库 (Collections)** |
| 异步/多线程 | `Thread` / `Executor` | **Kotlin Coroutines** |
| IO 流 | `java.io.*` | **`Okio` (KMP 版)** |

### 4. 推荐“黄金技术组合” (The Gold Stack)
为了降低团队选型纠结，减少填坑成本，我们定义了 KMP 基础设施的**标准推荐栈**。

*   **核心组合**：**Decompose + Koin + Ktor + SQLDelight + Kotlinx Serialization**
*   **选型理由**：
    1.  **验证充分**：该组合在国内外大型 KMP 项目（如 CashApp, JetBrains 官方示例）中验证最广。
    2.  **生态成熟**：针对这套组合的第三方插件（如 `Decompose-Router`）和社区 Issue 方案最丰富。
    3.  **文档齐全**：团队成员遇到问题时，能以最短时间找到成熟的解决方案。

---

> **结语**：
> 架构红线不是为了限制灵活性，而是为了确保 `shared` 层作为“跨平台地基”的绝对稳定性。违反红线的代码提交将无法通过架构评审。