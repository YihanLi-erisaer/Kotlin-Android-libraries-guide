# Android UI 组件（UI Component）库对比选型表（对齐 APM 构建维度）

| 库名 | 使用场景 | 状态 | 跨平台（KMP） | 支持 Compose | 构建侵入性 | 构建影响 | 构建治理要求 | APK 增量 | 反射开销 | 性能 | 接入难度 | Gradle 插件 | 构建支持 (Build Support) | 代码生成方式 (CodeGen Strategy) | 适配难度 | API 易读性 | 调试友好度 | 协议 | 维护频率 | 大厂背书 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **Jetpack Compose** | 全量 UI 构建 | 🔥 核心 | ✅ 原生支持 | ✅ 原生 API | 中 | 中 | 建议 | +1MB↑ | ✅ 无 | 🔥 很高 | 中 | 是 | Gradle | ❌ 无（运行时声明式 UI） | 中 | 优 | 中 | Apache 2.0 | ⭐⭐ 活跃 | Google | 声明式 UI、状态驱动 | 学习曲线陡 | **所有新 Android UI 项目必选** |
| **Material3 (Compose)** | 官方设计体系 | 🔥 核心 | ✅ 原生支持 | ✅ 原生 API | 中 | 中 | 建议 | +0.3MB | ✅ 无 | 🔥 很高 | 中 | 否 | Gradle | ❌ 无 | 中 | 优 | 中 | Apache 2.0 | ⭐⭐ 活跃 | Google | 官方规范、主题一致 | 定制成本较高 | **Compose 项目默认设计体系** |
| **Accompanist** | Compose 能力补充 | ⚠️ 逐渐过时/维护中 | ❌ | ✅ 原生 API | 低 | 低 | 无 | +0.2MB | ✅ 无 | 🔥 很高 | 低 | 否 | Gradle | ❌ 无 | 易 | 优 | 中 | Apache 2.0 | ⭐ 活跃 | Google | Pager / Insets 等能力 | 部分模块迁移中 | **Compose 常用辅助库，按需引入** |
| **LazyColumn / LazyRow** | 列表 / 网格 | 🔥 核心 | ✅ 原生 API | 低 | 低 | 无 | 无 | ✅ 无 | 🔥 很高 | 低 | 否 | 系统内置 | ❌ 无 | 易 | 优 | 优 | Android SDK | ⭐⭐ 活跃 | Google | 高性能虚拟列表 | 高级特效需额外封装 | **Compose 列表默认方案** |
| **ConstraintLayout (Compose)** | 复杂布局 | ⚪ 可选 | ✅ 原生 API | 中 | 低 | 无 | 极小 | ⚠️ 低 | 高 | 中 | 否 | Gradle | ❌ 无 | 中 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | Google | 适合复杂约束 | 不符合 Compose 主流心智，仅在需要通过 JSON 动态下发布局或极深层嵌套时使用 | **Row / Column 无法表达时使用** |
| **Material Components (View)** | XML / View UI | ❌ 过时 | ❌ 不支持 | 高 | 高 | 必须 | +0.8MB | ⚠️ 中 | 中 | 中 | 是 | Gradle | ⚠️ XML + 运行时解析 | 中 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | Google | 组件齐全、成熟 | 与 Compose 冲突 | **仅维护旧 View 项目** |
| **RecyclerView** | 旧项目列表 | ❌ 过时 | ❌ 不支持 | 中 | 中 | 无 | +0.2MB | ⚠️ 中 | 中 | 中 | 否 | Gradle | ❌ 无 | 中 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | Google | 成熟稳定 | Adapter 心智负担大 | **Compose 项目禁止使用** |
| **Horologist** | WearOS UI | ⭐ 主流 | ✅ 原生 API | 中 | 低 | 无 | +0.3MB | ✅ 无 | 高 | 中 | 否 | Gradle | ❌ 无 | 中 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | Google | 针对圆屏优化 | 场景受限 | **WearOS + Compose 项目首选** |
| **Landscapist** | 图片 UI 扩展 | ⚪ 可选 | ✅ 原生 API | 中 | 低 | 无 | +0.3MB | ✅ 无 | 高 | 中 | 否 | Gradle | ❌ 无 | 中 | 优 | 中 | Apache 2.0 | ⭐ 活跃 | 社区 | 图片效果能力强 | 基于 Coil 封装 | **复杂图片效果时使用** |



这份 `UIComponent.md` 文档的基础维度非常扎实，但在 **2025-2026 年现代 Android 开发** 语境下，UI 选型已经超出了“单纯的 Android 视图”，进入了 **“跨平台 (KMP)”、“自适应布局 (Adaptive)”** 以及 **“编译器插件化”** 的新阶段。

以下是针对这份文档的深度改进建议：

### 1. 核心技术修正与维度补充

*   **Jetpack Compose (编译器更新)**：
    *   **构建影响**：从 Kotlin 2.0 开始，Compose Compiler 已经变成了 **Gradle 第一方插件**。
    *   **性能标注**：虽然是“🔥 很高”，但必须强调 **Baseline Profiles**。没有基准配置文件的 Compose 在冷启动时性能会打折扣。
*   **Accompanist (状态更新)**：
    *   **修正**：状态应标注为 **“⚠️ 逐渐过时/维护中”**。Google 官方已将大部分常用功能（Pager, FlowLayout, Insets, SystemUIController）移入了 Compose 核心库。
    *   **建议**：仅在核心库不支持某些前沿特性（如权限处理、Adaptive 早期特性）时推荐。
*   **ConstraintLayout (Compose 版)**：
    *   **性能**：在 Compose 中，由于其嵌套测量逻辑，性能通常低于简单的 `Row/Column`。
    *   **选型建议**：补充“**仅在需要通过 JSON 动态下发布局或极深层嵌套时使用**”。
*   **补充：AndroidX Adaptive**：
    *   **背景**：针对折叠屏、平板和大屏设备的选型是目前的刚需。

---

### 优化后的 UI 组件（UI Component）库对比选型表

| 库名 | 核心定位 | 跨平台 (KMP) | 构建影响 | 实现原理 | 优点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---|:---|
| **Compose Foundation** | **UI 基石** | ✅ **原生支持** | 低 | 声明式渲染 | 极速响应、逻辑最纯净、零反射 | **所有 UI 构建的底层必选** |
| **Material 3 (M3)** | **官方标准设计** | ✅ **原生支持** | 中 | 官方样式封装 | 自动适配 Dynamic Color、组件极全 | **追求开发效率、标准 Android 风格首选** |
| **AndroidX Adaptive** | **自适应/大屏** | ✅ **原生支持** | 中 | 槽位模型 (Slots) | 完美支持折叠屏、分栏布局、大屏适配 | **平板/折叠屏/大屏设备优化必选** |
| **Compose Multiplatform** | **跨平台 UI** | ✅ **顶级支持** | 高 | **Skia / Canvas** | 一套代码运行在 Android/iOS/Web/桌面 | **KMP 项目、多端 UI 逻辑 100% 共享首选** |
| **WebView (KMP/Compose)** | **混合开发** | ✅ 支持 | 中 | 平台 View 包装 | 跨平台 H5 容器能力 | **内嵌活动页、H5 业务场景必选** |
| **Accompanist** | 实验性能力补充 | ❌ | 低 | 实验性 API | 包含一些官方库尚未稳定的前沿特性 | **仅当官方核心库无法满足特定组件时使用** |

---

### 2. UI 工程化治理红线 (UI Governance)

UI 是用户体验的第一战场，也是性能问题的多发区。建议增加以下治理准则：

#### A. 性能红线：基准配置文件 (Baseline Profiles)
*   **准则**：所有使用 Compose 的项目 **必须** 配置 `Baseline Profiles`。
*   **理由**：Compose 使用了解释执行+JIT。配置基准配置文件可以将部分代码在安装时预编译（AOT），使 App 启动速度提升 20%-30%，列表滑动掉帧减少 40%。

#### B. 稳定性红线：重组优化 (Recomposition)
*   **准则**：严禁在 Composable 函数中直接创建非稳定的状态对象。
*   **工具**：必须使用 **Layout Inspector** 监控重组次数。
*   **对策**：频繁变动的状态必须使用 `derivedStateOf` 或 `remember` 包装，防止因不必要的重组导致的界面卡顿。

#### C. 自适应红线：禁止硬编码尺寸
*   **原则**：禁止使用固定的 `width(360.dp)` 这种针对单一屏幕的设计。
*   **要求**：必须根据 **WindowSizeClass**（紧凑、中等、扩展）来定义响应式布局。

#### D. 状态提升与 UI 纯净度
*   **准则**：UI 组件应尽可能保持“无状态”（Stateless）。
*   **要求**：所有的状态（State）应提升至 ViewModel 侧，UI 只负责消费 `State` 和回调 `Event`，确保 UI 组件在 `Preview` 模式下的可测试性。

---

### 3. 总结：UI 选型决策路径

1.  **你是做一个全新的、标准风格的 App？**
    *   👉 **Jetpack Compose + Material 3**。这是官方目前的主力，组件最全。
2.  **你需要做一套高度品牌化、完全不符合 Material 设计的 UI？**
    *   👉 **Compose Foundation + 自定义 Design System**。不要在 M3 上强行修改，直接基于底层 Foundation 构建自己的按钮和样式。
3.  **你需要做 iOS/Android 跨平台 UI？**
    *   👉 **Compose Multiplatform (JetBrains)**。目前的完成度已经非常高，支持 iOS 原生渲染。
4.  **你的 App 需要在折叠屏（如三星/华为）和平板上有极佳表现？**
    *   👉 **AndroidX Adaptive**。使用 `ListDetailPaneScaffold` 来处理分栏逻辑。
5.  **你还在维护几年前的老项目？**
    *   👉 开启 **Compose & View 混合模式**。新的独立页面用 Compose，老页面维持 View，通过 `ComposeView` 逐步蚕食。

这份修改意见补齐了 **KMP 跨平台支持** 和 **自适应布局 (Adaptive)** 的趋势，并强化了 **Baseline Profiles** 这一 Compose 性能优化的核心治理点，非常具有前瞻性。