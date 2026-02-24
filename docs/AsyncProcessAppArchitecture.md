# 异步处理 / 应用架构（Async Process & App Architecture）选型对比表（对齐 APM 构建维度）

| 库名 | 使用场景 | 状态 | 支持 Compose | 构建侵入性 | 构建影响 | 构建治理要求 | 跨平台（KMP） | APK 增量 | 反射开销 | 性能 | 接入难度 | Gradle 插件 | 构建支持 (Build Support) | 代码生成方式 (CodeGen Strategy) | 适配难度 | API 易读性 | 调试友好度 | 协议 | 维护频率 | 大厂背书 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:----:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **Kotlin Coroutines** | 现代异步 / 并发基础 | 🔥核心 | 🔁 Flow / State 适配 | 低 | 低 | 无 | ✅ | +0.2MB | ✅ | 无 | 🔥 很高 | 中 | 否 | Gradle | 编译器驱动的状态机转换 | 中 | 优 | 中 | Apache 2.0 | ⭐⭐ 活跃 | JetBrains / Google | 结构化并发、低开销、官方推荐 | 学习曲线陡峭 | **所有现代 Android / Compose 项目必选基础设施** |
| **Flow (StateFlow / SharedFlow)** | 状态流 / 数据流 | 🔥核心 | 🔁 原生状态模型 | 低 | 极低 | 无 | ✅| +0.1MB | ✅ | 无 | 🔥 很高 | 中 | 否 | Gradle | ❌ 无（纯运行时） | 中 | 优 | 中 | Apache 2.0 | ⭐ 活跃 | JetBrains | 与 Compose 状态模型天然契合 | 操作符语义需熟悉 | **UI 状态管理与数据流首选** |
| **RxJava** | 复杂事件流 / 遗留项目 | ⭐主流 | 🔁 通过适配使用 | 低 | 中 | 无 | ❌ | +1.2MB | ⚠️ 中 | 中 | 高 | 否 | Gradle | ❌ 无（纯运行时） | 难 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | ReactiveX | 表达能力极强，生态成熟 | 体积大、易误用 | **仅限遗留项目或 Rx 深度经验团队** |
| **LiveData** | 生命周期感知数据 | ⚪ 可选 | 🔁 适配使用 | 低 | 极低 | 无 | ❌ | +0.1MB | ⚠️ 低 | 中 | 低 | 否 | Gradle | ❌ 无（纯运行时） | 易 | 中 | 差 | Apache 2.0 | ⭐ 活跃 | Google | 简单安全，学习成本低 | 不适合复杂状态流 | **旧项目维护使用，新项目建议用 StateFlow** |

---

### 架构治理红线与技术准则

为了对齐你之前的文档风格，建议为“应用架构”增加以下**治理准则**：

#### A. 响应式架构红线 (Reactive Redlines)
*   **准则 1：唯一可信源 (SSOT)**。所有 UI 状态必须通过 `StateFlow` 或 `Compose State` 暴露，严禁在 UI 层直接修改底层数据。
*   **准则 2：结构化并发**。严禁使用 `GlobalScope`。所有协程必须在具备生命周期的 `CoroutineScope`（如 `viewModelScope`）中启动，确保 Activity/ViewModel 销毁时任务自动取消。

#### B. 异步方案选型策略
*   **新项目：Coroutines + Flow**。这是 Google 和 JetBrains 共同背书的未来。它在包体积（协程仅 +200KB vs RxJava +1.2MB）和内存占用上具有绝对优势。
*   **状态处理**：使用 `collectAsStateWithLifecycle()`。这是 Compose 中消费 Flow 的安全方式，它能感知 Android 的生命周期（如进入后台自动停止采集），比简单的 `collectAsState()` 更节省资源。

#### C. 跨平台适配治理
*   **准则**：在 `shared` 层（KMP）定义的架构模型必须避免使用 `LiveData`。如果需要向 iOS 暴露响应式流，应在 `shared` 层使用 `NativeFlow` 封装或利用 **SKIE** 插件进行自动桥接。