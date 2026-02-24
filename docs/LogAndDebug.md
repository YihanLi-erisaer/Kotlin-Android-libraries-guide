# Android 日志与调试（Log & Debug）库对比选型表（对齐 APM 构建维度）

| 库名 | 使用场景 | 状态 | 跨平台（KMP）| 支持 Compose | 构建侵入性 | 构建影响 | 构建治理要求 | APK 增量 | 反射开销 | 性能 | 接入难度 | Gradle 插件 | 构建支持 (Build Support) | 代码生成方式 (CodeGen Strategy) | 适配难度 | API 易读性 | 调试友好度 | 协议 | 维护频率 | 大厂背书 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **Timber** | 业务日志记录 | 🔥 核心 | ❌ | 🔁 状态/Side-Effect 使用 | 低 | 极低 | 无 | +0.05MB | ✅ 无 | 🔥 很高 | 易 | 否 | Gradle | ❌ 无（纯运行时） | 易 | 优 | 中 | Apache 2.0 | ⭐ 活跃 | Square | 轻量优雅、事实标准 | 无 UI、无结构化 | **所有 Android 项目默认日志方案** |
| **Logcat（系统）** | 基础日志输出 | 📦 基础 | ❌ 不支持 | ❌ 不支持 | 低 | 极低 | 无 | 无 | ✅ 无 | 中 | 易 | 否 | 系统内置 | ❌ 无 | 易 | 差 | 差 | Android SDK | ⭐ 维护 | Google | 系统自带、零成本 | 无治理、不可控 | **仅底层或临时调试使用** |
| **LeakCanary** | 内存泄漏检测 | ⭐ 主流 | ❌ | ❌ 不支持 | 中 | 中 | 必须（仅 Debug） | +1.2MB（Debug） | ⚠️ 中（Hook / 分析） | ⚠️ 中 | 易 | 否 | Gradle | ❌ 无（运行时分析） | 易 | 优 | 优 | Apache 2.0 | ⭐ 活跃 | Square | 自动检测、零侵入 | 仅限 Debug | **Debug 构建必选，Release 禁用** |
| **Flipper** | 可视化调试平台 | ⭐ 主流 | ❌ | ❌ 不支持 | 中 | 高 | 必须（Debug 隔离） | +2MB（Debug） | ⚠️ 中（插件反射） | 中 | 中 | 是 | Gradle | ⚠️ 插件化注册 | 中 | 中 | 优 | MIT | ⭐ 活跃 | Meta | 网络/DB/日志可视化 | 集成与维护成本高 | **中大型项目 Debug 阶段推荐** |
| **App Inspection** | IDE 实时调试 | ⭐ 主流 | ❌ 不支持 | ❌ 不支持 | 低 | 无 | 无 | 无 | ✅ 无 | 🔥 很高 | 易 | 否 | Android Studio | ❌ 无（IDE 工具） | 易 | 优 | 中 | 官方工具 | ⭐ 维护 | Google | AS 内置、零侵入 | 非运行时能力 | **所有项目默认开启** |
| **Stetho** | Chrome 调试 | ❌ 过时，已被 Flipper 和 Android Studio App Inspection 完美替代 | ❌ 不支持 | ❌ 不支持 | 低 | 中 | 无 | +0.8MB | ⚠️ 中 | 中 | 易 | 否 | Gradle | ❌ 无 | 易 | 中 | 中 | Apache 2.0 | ❌ 停止 | Meta | DevTools 接入简单 | 已停更 | **新项目禁止使用** |
| **SLF4J + Logback** | 统一日志体系，除非需要将日志本地加密并分片压缩上报，否则不建议使用 | ⚪ 可选 | ❌ 不支持  | 🔁 状态/封装使用 | 高 | 高 | 必须 | +2MB | ⚠️ 中 | ⚠️ 中 | 难 | 否 | Gradle | ⚠️ 运行时配置解析 | 难 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | 社区 | 多策略、多输出 | 移动端偏重 | **仅日志统一 / KMP 场景使用** |
| **Chucker** | 网络请求调试 | ⭐ 主流 | ❌ 不支持 | ❌ 不支持 | 中 | 中 | 必须（仅 Debug） | +1.5MB（Debug） | ⚠️ 中（Hook / 分析） | ⚠️ 中 | 易 | 否 | Gradle | ❌ 无（运行时分析） | 易 | 优 | 优 | Apache 2.0 | ⭐ 活跃 | 社区 | 自动捕获 HTTP 请求响应 | 仅限 Debug，网络专用 | **Debug 构建网络调试首选** |
| **Kermit** | 结构化日志与遥测 | ⭐ 可选 | ✅ 原生支持 | ✅ 原生支持 | 高 | 中 | 建议 | +0.5MB | ✅ 无 | 🔥 很高 | 中 | 否 | Gradle | ❌ 无（纯运行时） | 中 | 优 | 优 | Apache 2.0 | ⭐ 活跃 | Google | 结构化日志，KMP 支持好 | 学习成本高，生态弱 | **需要结构化日志与 KMP 时使用** |

---

<!-- ### 优化后的日志与调试（Log & Debug）库对比表（局部）

| 库名 | 使用场景 | 跨平台 (KMP) | 优点 | 选型建议 |
|:---:|:---:|:---:|:---|:---|
| **Timber** | 基础业务日志 | ❌ | API 简洁，支持多 Tree 扩展（如过滤输出） | **所有 Android 纯端项目默认首选** |
| **Chucker** | **网络联调** | ❌ | **手机端即时查看网络包，无需连接电脑** | **所有开发/测试构建必装，强烈推荐** |
| **Kermit** | **KMP 共享层日志** | ✅ | 跨平台统一 API，支持多端输出，性能极高 | **KMP 项目 Shared 模块必备** |
| **Flipper** | 桌面级调试平台 | ❌ | 功能极其全（DB、Layout、Network、Preference） | **中大型项目、需要桌面端深度调试时首选** |
| **LeakCanary** | 内存泄漏治理 | ❌ | 自动捕获、自动分析并推送堆栈信息 | **所有项目 Debug 阶段必选，Release 严禁注入** |-->

### 日志与调试工程化治理红线 (Log & Debug Governance)

日志是调试的“眼睛”，但也可能成为泄密和性能的“杀手”。建议增加以下治理准则：

#### A. 日志剥离红线 (Log Stripping)
*   **准则**：Release 环境严禁输出业务 Log。
*   **治理对策**：
    *   **Timber 模式**：仅在 `Application.onCreate` 的 Debug 分支植入 `Timber.DebugTree()`。
    *   **R8/Proguard**：配置规则强制移除 `android.util.Log` 的所有调用。
    ```proguard
    -assumenosideeffects class android.util.Log {
        public static *** d(...);
        public static *** v(...);
    }
    ```

#### B. 隐私脱敏治理 (Privacy Masking)
*   **红线**：严禁在日志中明文打印用户 PII（个人可标识信息），如：手机号、身份证、密码、Auth Token。
*   **要求**：在 Timber 的 Tree 装饰器中增加拦截逻辑，利用正则自动将敏感字段打码（如 `138****0001`）。

#### C. 构建安全隔离 (Build Safety)
*   **原则**：调试工具必须“物理隔离”。
*   **要求**：**Chucker**、**LeakCanary** 和 **Flipper** 必须使用 `debugImplementation` 引入。
*   **红线**：严禁将 `com.github.chuckerteam.chucker:library` 误写为 `implementation`，这会导致生产包可以直接查看到网络 Token 和用户信息。

#### D. 网络调试规范
*   **准则**：由于 **App Inspection** 已内置于 Android Studio，对于日常开发：
    1.  简单网络查看用 **Chucker**（手机端）。
    2.  深度网络重放/Mock 用 **Flipper**。
    3.  数据库查看用 **App Inspection** (Database Inspector)。

---

### 总结：选型决策路径

1.  **你是要做 KMP（跨平台）？**
    *   👉 **Kermit** (Shared 层) + **Timber** (Android 表现层)。
2.  **你发现联调效率低，经常需要找后台看日志？**
    *   👉 立即集成 **Chucker**，让 QA 和开发者在手机上就能看接口报错。
3.  **你的应用有神秘的 OOM（内存溢出）？**
    *   👉 **LeakCanary** 是唯一真神。
4.  **你需要可视化查看本地数据库或 SharedPreferences？**
    *   👉 首选 **AS App Inspection**（无需依赖）；如果需要修改数据，选 **Flipper**。
