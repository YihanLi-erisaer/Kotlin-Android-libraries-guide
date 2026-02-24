# Android 图标（Icon）库对比选型表

| 库名 | 使用场景 | 状态 | 跨平台（KMP）| 支持 Compose | 构建侵入性 | 构建影响 | 构建治理要求 | APK 增量 | 反射开销 | 性能 | 接入难度 | Gradle 插件 | 构建支持 (Build Support) | 代码生成方式 (CodeGen Strategy) | 适配难度 | API 易读性 | 调试友好度 | 协议 | 维护频率 | 大厂背书 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **Compose Material Icons（core）** | Compose UI 图标官方基础库 | 🔥核心 | ✅ 支持 | ✅ 原生 API | 无 | 无 | 无 | +0.2MB | ✅ 无 | 🔥 很高 | 低 | 否 | Gradle | ImageVector 代码 | 易 | 优 | 优 | Apache 2.0 | ⭐⭐ 活跃 | Google | Compose 原生支持，调用极简 | 图标风格固定，Extended 库会导致编译极慢 | **中大型项目严禁直接引入，建议提取所需代码使用** |
| **Compose Material Icons（extended）** | Compose UI 图标官方全量库 | 🔥核心 | ✅ 支持 | ✅ 原生 API | 无 | 无 | 无 | 不开启R8优化 + 30MB | ⚠️ 高 | 🔥 很高 | 低 | 否 | Gradle | 数千个 Kotlin 单例类 | 易 | 优 | 优 | Apache 2.0 | ⭐⭐ 活跃 | Google | Compose 原生支持，调用极简 | 图标风格固定，Extended 库会导致编译极慢 | **中大型项目严禁直接引入，建议提取所需代码使用** |
| **Material Icons（官方）** | 通用应用图标 | 🔥核心 | ✅ Compose Multiplatform版本 | 🔁 通过资源适配 | 无 | 无 | 无 | +0.3MB | ✅ 无 | 🔥 很高 | 低 | 否 | Gradle | ❌ 无（静态资源） | 易 | 优 | 优 | Apache 2.0 | ⭐⭐ 活跃 | Google | 官方标准，覆盖面广 | 个性化受限 | **绝大多数 Android 项目通用方案** |
| **Vector Drawable（系统）** | 自定义矢量图标 | 🔥核心 | ✅ 资源转换 | 🔁 通过资源适配 | 无 | 无 | 无 | 无 | ✅ 无 | 🔥 很高 | 中 （运行时解析 SVG 的 CPU 开销高于编译后的 VectorDrawable） | 否 | Framework | XML 预编译二进制 | 中 | 中 | 中 | 系统库 | ⭐⭐ 活跃 | Android 平台 | 体积小、性能最好 | 需要自行维护资源 | **品牌定制 / 高质量 UI 必选** |
| **SVG（运行时解析）** | 动态 / 网络图标 | ⚪ 可选 | ⚠️ 适配中 | ⚠️ 通过 Coil-SVG 或 Compose-SVG 实现 | 低 | 低 | 无 | +0.1MB | ⚠️ 中 | 中 | 中 | 否 | Gradle | ❌ 无（运行时解析） | 中 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | 社区 | 支持动态 SVG | 运行时解析有开销 | **动态图标或远端资源场景** |
| **Android-Iconics** | 图标字体 | ⚠️ 逐渐过时 | ❌ | ⚠️ AndroidView 包装 | 低 | 低 | 无 | +0.4MB | ⚠️ 中 | 中 | 中 | 否 | Gradle | ❌ 无（字体 + Runtime） | 中 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | 社区 | 多图标字体、主题化方便 | 偏 View 体系 | **传统 View / 老项目可选** |
| **Lucid (Compose版)** | 现代图标库生成工具 | ⭐主流 | ✅ 支持 | ✅ 原生 API | 低 | 低 | 无 | +0.2MB | ✅ 无 | 🔥 很高 | 中 | 是 | Gradle / AGP | ⚠️ 编译期代码生成（KSP） | 中 | 优 | 中 | Apache 2.0 | ⭐ 活跃 | JetBrains / Google | 支持多平台，KSP 加速构建，灵活的图标定义方式（SVG、DSL）| 需要学习新的图标定义方式，生态较小 | **需要跨平台或自定义图标生成的项目可选** |
| **Feather-Icons (Compose版)** | 轻量级开源图标库 | ⭐主流 | ✅ Compose Multiplatform版本 | 🔁 通过资源适配 | 无 | 无 | 无 | +0.1MB | ✅ 无 | 🔥 很高 | 低 | 否 | Gradle | ❌ 无（静态资源） | 易 | 优 | 优 | Apache 2.0 | ⭐ 活跃 | 社区 | 体积小、风格现代，支持多平台版本 | 图标数量有限，个性化受限 | **需要轻量现代图标的项目可选** |
| **Compose-resource-icons** | 资源图标 Compose 适配库 | ⚪ 可选 | ✅ 支持 | ✅ 原生 API | 无 | 无 | 无 | +0.1MB | ✅ 无 | 🔥 很高 | 低 | 否 | Gradle | ❌ 无（资源适配） | 易 | 优 | 优 | Apache 2.0 | ⭐ 活跃 | 社区 | 方便将现有资源图标适配到 Compose 中使用 | 需要维护适配代码，性能不如直接使用 ImageVector | **已有大量资源图标且迁移到 Compose 的项目可选** |

---

### 图标工程化治理红线 (Icon Governance)

#### A. 严禁无脑引入 Extended 库
*   **红线**：禁止在模块的 `build.gradle` 中直接添加 `implementation(libs.androidx.compose.material.icons.extended)`。
*   **理由**：这会引入上万个类。如果项目中只需要 10 个图标，引入此库会导致增量编译变慢数秒。
*   **治理对策**：使用脚本将所需的图标代码单独拷贝到项目的 `ui-common` 模块，或者使用专门的插件只按需编译。

#### B. 矢量图治理 (Vector Optimization)
*   **准则**：所有 XML 矢量图必须经过 **Avocado** 或 **Vector Drawable Optimizer** 压缩。
*   **红线**：禁止直接将带有冗余路径（由 AI/Figma 直接导出）的 SVG 转换为 XML。过多的无用路径会导致界面卡顿和膨胀。

#### C. KMP 跨平台图标方案
*   **准则**：在 `shared` 模块中，优先使用 **Compose Resources** 管理图标。
*   **路径**：将图标放在 `commonMain/composeResources/drawable` 下，以便 Android 和 iOS 同时通过 `painterResource(Res.drawable.icon_name)` 调用。

#### D. 图标字体 vs 矢量图
*   **结论**：在 Compose 项目中，**彻底废弃 Android-Iconics 或图标字体方案**。
*   **理由**：`ImageVector` 具有更好的编译期检查、更高的渲染性能以及更原生的 `Tint` 着色支持。

---

### 4. 总结：图标选型决策路径

1.  **你是纯 Android 且追求快？**
    *   👉 官方 **Material Icons Core**。
2.  **官方图标不够用？**
    *   👉 禁止用 Extended 库！去 [Lucide.dev](https://lucide.dev) 或 [Tabler](https://tabler-icons.io) 找对应的 Kotlin 代码复制。
3.  **设计师给的品牌图标？**
    *   👉 **Vector Asset Studio** 转换为 **Vector Drawable**。
4.  **需要从服务器动态下发图标？**
    *   👉 **Coil + SVG 解码器**。
5.  **你在做 KMP 跨平台？**
    *   👉 统一放入 **Compose Resources** 文件夹。

