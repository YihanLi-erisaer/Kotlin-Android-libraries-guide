# Android 网络通信（Networking）库对比选型表（对齐 APM 构建维度）

| 库名 | 使用场景 | 状态 | 支持 Compose | 构建侵入性 | 构建影响 | 构建治理要求 | APK 增量 | 反射开销 | 性能 | 接入难度 | Gradle 插件 | 构建支持 (Build Support) | 代码生成方式 (CodeGen Strategy) | 适配难度 | API 易读性 | 调试友好度 | 协议 | 维护频率 | 大厂背书 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **OkHttp** | HTTP 底层通信 | 🔥 核心 | 🔁 协程/Flow 支持 | 低 | 低 | 必须(强制版本对齐) | +0.6MB | ✅ 无 | 🔥 很高 | 中 | 否 | Gradle | ❌ 无（纯运行时） | 易 | 优 | 优 | Apache 2.0 | ⭐⭐⭐ (极高/事实标准) | Square / Google | 性能极高、拦截器强大，Android 的 事实标准底层 | 仅提供底层能力 | **所有 Android 项目网络层必选底座** |
| **Retrofit** | 业务层 REST API | 🔥 核心 | 🔁 Flow / suspend 适配 | 低 | 低 | 建议 | +0.3MB | ⚠️ 低（动态代理） | 高 | 低 | 否 | Gradle | ⚠️ 运行时动态代理 (Dynamic Proxy) | 中 | 优 | 优 | Apache 2.0 | ⭐⭐⭐ (极高/事实标准) | Square / Google | 行业标准、生态最完整 | 抽象层多，理解成本高 | **Android 业务网络请求默认首选** |
| **Ktor** | KMP / 多端网络 | ⭐ 主流 | 🔁 Flow / suspend 适配 | 中 | 高 | 必须 | +1.5MB | ⚠️ 中（插件化 DSL） | 高 | 高 | 否 | Gradle | ❌ 无（运行时 DSL） | 难 | 优 | 中 | Apache 2.0 | ⭐ 活跃 | JetBrains | Kotlin-first、跨平台统一 | Android 单端成本高 | **KMP / 多端项目首选，纯 Android 谨慎** |
| **HttpURLConnection** | 底层 / 无依赖 | 📦 基础 | ❌ 不支持 | 低 | 极低 | 无 | 无 | ✅ 无 | 中 | 高 | 否 | 系统内置 | ❌ 无 | 难 | 差 | 差 | Android SDK | ⭐ 维护 | Google | 系统自带、零依赖 | API 原始、效率低 | **仅 SDK / 极端受限环境使用** |
| **Volley** | 旧项目 / 教学 | ❌ 过时 | ❌ 不支持 | 低 | 低 | 无 | +0.1MB | ✅ 无 | 中 | 易 | 否 | Gradle | ❌ 无 | 易 | 差 | 差 | Apache 2.0 | ❌ 停止 | Google | 自动队列 | 架构老旧 | **新项目禁止使用** |
| **Cronet** | 极端性能 / 特殊协议 | ⚪ 可选 | 🔁 需封装适配 | 高 | 高 | 必须 | +8~10MB (内置版) / <1MB (Play Services 版) | ✅ 无 | 🔥 很高 | 高 | 否 | Gradle / NDK | ⚠️ Native Runtime | 难 | 中 | 低 | BSD | ⭐ 活跃 | Google | 性能极致、协议支持广泛，移动端完美支持 HTTP/3 (QUIC) 的库，在弱网环境下，它的性能远超 OkHttp| 集成复杂、体积大，API 异步且非标准，不直接支持 OkHttp 的拦截器 | **极端性能或特殊协议需求时考虑** |

---

### “网络治理红线” (Networking Governance)

网络库选型后，真正的工程挑战在于**治理**。建议在文档末尾增加以下准则：

#### A. 版本冲突治理 (Dependency Alignment)
*   **红线**：禁止在模块中私自引入不同版本的 OkHttp。
*   **治理对策**：必须在根目录的 `libs.toml` (Version Catalogs) 中统一版本，并利用 Gradle 的 `resolutionStrategy` 强制所有第三方 SDK（如地图、推送）共用同一个 OkHttp 实例，防止多套连接池导致的内存膨胀和连接浪费。

#### B. 序列化解耦治理 (Serialization Integration)
*   **准则**：网络库应与序列化库解耦。
*   **推荐方案**：
    *   **Retrofit** + **Moshi** / **Kotlinx.Serialization** (利用 Converter.Factory)。
    *   **Ktor** + **Kotlinx.Serialization** (原生支持)。
*   **红线**：严禁在业务代码中手动调用 `JSONObject` 或 `Gson().fromJson()` 处理网络响应。

#### C. 网络调试与拦截器治理 (Interceptor Governance)
*   **准则**：Debug 构建必须集成 **Chucker** 或 **Flipper**。
*   **红线**：严禁在 Release 构建中开启全量 Body 打印日志（Logging Interceptor），这会严重拖慢大数据量请求的性能，且存在信息泄露风险。

#### D. Cronet 的“平滑降级”方案
*   **准则**：对于集成 Cronet 的项目，建议使用 **OkHttp-Cronet Bridge**。
    *   **方案**：将 Cronet 包装成 OkHttp 的一个 `Call.Factory`。
    *   **优点**：既享受了 Cronet 的 **QUIC/HTTP3** 协议性能，又保留了 OkHttp 极其丰富的 **Interceptor 生态**（如日志、加签、缓存）。

### 选型建议决策树（追加建议）

为了让团队成员快速决策，可以增加这一段：

1.  **你是标准 RESTful 业务 App？**
    *   👉 **Retrofit + OkHttp** (这是 Android 领域的“黄金准则”，生态最稳)。
2.  **你需要做 iOS/Android 跨平台（KMP）？**
    *   👉 **Ktor** (它是目前 KMP 下唯一成熟的多端通用网络库)。
3.  **你是极高并发、或对弱网/首包耗时有极致追求（如视频流、社交、大厂应用）？**
    *   👉 **Cronet + OkHttp Bridge** (享受 QUIC 带来的 0-RTT 握手)。
4.  **你只是在写一个极小的、不希望引入额外依赖的插件或 SDK？**
    *   👉 **OkHttp** (单用 OkHttp，不封装 Retrofit) 或 **HttpURLConnection**。
