
# Android 权限管理（Permission）库对比选型表（对齐 APM 构建维度）

| 库名 | 使用场景 | 状态 | 跨平台（KMP）| 支持 Compose | 构建侵入性 | 构建影响 | 构建治理要求 | APK 增量 | 反射开销 | 性能 | 接入难度 | Gradle 插件 | 构建支持 (Build Support) | 代码生成方式 (CodeGen Strategy) | 适配难度 | API 易读性 | 调试友好度 | 协议 | 维护频率 | 大厂背书 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **AndroidX Activity Result API** | 官方权限请求 | 🔥 核心 | ❌ | 🔁 间接适配 | 低 | 低 | 无 | 无 | ✅ 无 | 🔥 很高 | 低 | 否 | Gradle | ❌ 无（纯运行时） | 中 | 中 | 中 | Apache 2.0 | ⭐⭐ 活跃 | Google | 官方方案，紧跟系统权限模型 | 样板代码偏多 | **所有项目的底层依赖，默认必选** |
| **Accompanist Permissions** | Compose 权限管理 | 🔥 核心 | ❌ | ✅ 原生 API | 低 | 低 | 无 | +0.1MB | ✅ 无 | 🔥 很高 | 低 | 否 | Gradle | ❌ 无（纯运行时） | 易 | 优 | 中 | Apache 2.0 | ⭐ 活跃 | Google | Compose 原生、状态可组合 | 属于辅助库，需关注版本 | **Compose 项目权限管理首选** |
| **PermissionX** | 权限流程快速封装 | ⭐ 主流 | ❌ | 🔁 状态适配 | 中 | 低 | 建议 | +0.1MB | ⚠️ 中 | 高 | 中 | 否 | Gradle | ❌ 无（运行时封装） | 中 | 优 | 中 | Apache 2.0 | ⭐ 活跃 | 社区 | Rationale 流程完整 | 非 Compose 原生 | **传统 View / 混合项目推荐** |
| **EasyPermissions** | 简化权限回调 | ⚪ 可选 | ❌ | ❌ | 中 | 低 | 无 | +0.1MB | ⚠️ 中 | 中 | 中 | 否 | Gradle | ⚠️ 注解 + 运行时反射 | 中 | 中 | 低 | Apache 2.0 | ⭐ 维护 | Google 示例 | 上手快 | Compose 体验差 | **老项目维护可用，新项目不推荐** |
| **PermissionsDispatcher** | 注解生成权限代码 | ❌ 过时 | ❌ | ❌ 不支持 | 高 | 中 | 必须 | +0.2MB | ⚠️ 高 | 中 | 高 | 是 | Gradle | ⚠️ 编译期注解生成 | 难 | 中 | 低 | Apache 2.0 | ⚪ 维护 | 社区 | 减少模板代码 | 构建慢、复杂 | **新项目禁止使用** |
| **RxPermissions** | RxJava 权限流 | ❌ 过时 | ❌ | ❌ 不支持 | 中 | 中 | 必须 | +0.1MB | ⚠️ 高 | 中 | 高 | 否 | Gradle | ❌ 无（Rx 运行时封装） | 难 | 中 | 中 | Apache 2.0 | ⚪ 维护 | 社区 | Rx 链式处理方便 | 与协程 / Compose 冲突 | **仅限遗留 Rx 项目** |
| **MOKO Permissions** | KMP 跨平台权限 | ⭐ 主流 | ✅ 原生支持 | 🔁 状态适配 | 中 | 高 | 必须 | +0.2MB | ✅ 无 | 高 | 高 | 否 | Gradle / Swift Package Manager | ❌ 无（多平台运行时） | 难 | 中 | 低 | Apache 2.0 | ⭐ 活跃 | IceRock / Touchlab | KMP 跨平台统一权限处理 | Android 体验一般 | **KMP 多端项目首选，纯 Android 谨慎** |

---

<!-- ### 优化后的权限申请（Permission）库对比选型表

| 库名 | 核心定位 | 跨平台 (KMP) | 支持 Compose | 状态恢复支持 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **Activity Result API** | **官方底座** | ❌ | 🔁 需配合协程 | 🔥 **极强** | 零依赖、官方最稳定、完美处理进程重启 | 样板代码多，Rationale (解释逻辑) 需手写 | **所有项目的基础核心，架构师首选** |
| **Accompanist** | **Compose 增强** | ❌ | ✅ **原生 API** | 中 | 状态驱动，API 与 Compose 哲学高度统一 | 官方已停止特性更新，维护状态一般 | **追求代码简洁、纯 Compose 项目首选** |
| **PermissionX** | **业务流程大师** | ❌ | 🔁 状态适配 | 中 | **Rationale 处理最优雅**，支持链式调用，一键跳转设置页 | 引入了 Fragment 作为代理，侵入性稍高 | **国内复杂合规需求、传统 View 项目首选** |
| **MOKO Permissions** | **KMP 标准** | ✅ **原生支持** | 🔁 状态适配 | 低 | **Android/iOS 逻辑高度统一**，基于协程 | 配置相对复杂，对 iOS 端的注入有要求 | **KMP (跨平台) 项目唯一成熟选型** |
| **PermissionsDispatcher** | 编译期生成 | ❌ | ❌ | ❌ | 减少样板代码，强制处理权限拒绝场景 | **增加编译耗时 (APT)**，与 Compose 兼容性极差 | **❌ 现代项目已不推荐使用** |-->

---

### 权限申请工程化治理红线 (Permission Governance)

在现代 Android 治理（特别是国内应用市场审核）中，权限是“高压线”。

#### A. 启动合规红线 (Compliance Redline)
*   **红线**：禁止在 App 启动时**静默申请**任何敏感权限（如定位、存储）。
*   **准则**：权限申请必须由用户的主动行为（点击按钮）触发。
*   **治理对策**：使用 `PermissionX` 或自定义 `RationaleProvider`，在真正 `request` 之前弹出一个 UI 解释为什么要这个权限。

#### B. 进程重启治理 (Process Death)
*   **准则**：必须考虑权限申请过程中的进程回收。
*   **红线**：严禁在权限回调中使用任何**匿名内部类**或**非持久化引用**。
*   **要求**：如果使用官方 Activity Result API，必须在 `onCreate` 阶段进行 `register`。

#### C. “不再提醒”后的引导治理
*   **准则**：当用户勾选“不再提醒”并拒绝后，App 不应陷入“死循环”。
*   **方案**：选型库必须支持直接跳转到系统的 **“应用设置详情页”**，并能监听从设置页返回后的权限变化状态。

#### D. KMP 项目的平台差异
*   **准则**：iOS 的权限需要在 `Info.plist` 中声明字符串，而 Android 在 `AndroidManifest.xml`。
*   **治理要求**：使用 **MOKO Permissions** 时，必须确保 `commonMain` 中的权限 key 与各平台 native 配置严格对齐，否则会导致 iOS 端直接闪退。

---

### 总结：权限选型决策路径

1.  **你是纯 Android 项目，且追求极致稳定与启动性能？**
    *   👉 **AndroidX Activity Result API** (通过自定义 `suspend` 函数封装可降低样板代码)。
2.  **你是纯 Compose 项目，且逻辑较简单？**
    *   👉 **Accompanist Permissions** (虽然维护慢，但目前依然是 Compose 体验天花板)。
3.  **你的 App 需要上架国内各大商店，且权限交互非常复杂？**
    *   👉 **PermissionX** (郭霖出品，对国内 Rationale 和弹窗逻辑处理得最透彻)。
4.  **你在做 Kotlin Multiplatform (KMP)？**
    *   👉 **MOKO Permissions**。
