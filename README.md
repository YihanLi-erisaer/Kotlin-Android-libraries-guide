# Click here to view the full documentation for each library category:
[*KMP Shared Core Information*](docs/KMPSharedCore.md)
[*Build Engineering Information*](docs/BuildEngineering.md)
[Networking Libraries Information](docs/Networking.md)
[Image Loading Libraries Information](docs/ImageLoading.md)
[Json Serialization Libraries Information](docs/JsonSerialization.md)
[Local Database Libraries Information](docs/LocalDatabase.md)
[KV Storage Libraries Information](docs/KVStorage.md)
[Dependency Injection Libraries Information](docs/DI.md)
[State Control Libraries Information](docs/StateControl.md)
[Async & App Architecture Libraries Information](docs/AsyncProcessAppArchitecture.md)
[UI Components Libraries Information](docs/UIComponents.md)
[Advanced Functionality Libraries Information](docs/AdvFunc.md)
[Navigation Libraries Information](docs/Navigation.md)
[Permissions Libraries Information](docs/Permissions.md)
[Audio & Video Libraries Information](docs/AudioVideo.md)
[Animation Libraries Information](docs/Animation.md)




# 说明
**Compose 支持情况：**
- ✅ **原生 API：** 库提供官方或成熟的 Jetpack Compose 接口，可直接在 Compose 中使用（例如：Coil、Compose Navigation、`paging-compose`）。
- 🔁 **通过 Flow/LiveData 适配：** 库原生可能基于回调或传统 API，但可通过 `Flow` / `LiveData` 等适配层自然集成到 Compose（例如：Retrofit 配合 `Flow`、Room 返回 `Flow`/`LiveData`）。
- ⚠️ **需使用 `AndroidView` 包装：** 库基于传统 View 体系或暂无原生 Compose API，使用时需通过 `AndroidView` 或其他桥接方案包装（例如：某些自定义 View 或第三方 View 库）。
- ❌ **不支持Compose：** 仅支持传统 View 系统或已停止维护，不建议在新 Compose 项目中直接使用。

**库的状态：**
- 🔥 **核心**：必不可少，强烈推荐，Google官方支持或行业标准
- ⭐ **主流**：广泛使用，社区活跃，推荐使用
- ⚪ **可选**：特定场景使用，或非必需功能
- ❌ **过时**：已停止维护，不推荐新项目使用
- 📦 **基础**：系统内置或基础库

** 

**实现难度：**
- **低**：快速接入，配置少
- **中**：需要一定架构理解
- **高**：涉及系统调度或复杂状态管理

**API易读性：**
- **优**：声明式 / DSL 风格，理解成本低
- **中等**：配置项较多，但结构清晰
- **差**：命令式或概念复杂

**调试友好度：**
- **优**：可预览、调试工具完善
- **中等**：依赖日志或运行时观察
- **差**：定位问题成本高

**性能**
- **🔥高**：性能优越，适合高帧率或复杂场景
- **中**：性能一般，适合大多数常规场景
- **⚠️低**：性能较差，可能导致 UI 卡顿或资源

**反射开销**
- **✅无**：不使用反射，性能稳定
- **⚠️低**：少量反射，性能影响较小
- **⚠️中**：部分反射或 Hook，可能影响性能
- **⚠️高**：大量反射或自动初始化，可能显著影响性能

**开源协议：**
- **Apache 2.0**：自由度高，无商业排他限制
- **Realm License**：商业闭源协议，免费版有功能限制
- **BSD**：可商业使用

**维护频率：**
- **⭐⭐活跃**：日活跃迭代，快速响应问题（如火山引擎字节重点）
- **⭐活跃**：定期更新维护，响应及时（如Bugly、Umeng、阿里云）

**大厂背书：**
- **高**：由知名大厂主导或强力支持，在行业内广泛使用
- **中/中高**：社区维护或小厂支持，但在特定领域有较好口碑
- **低**：个人或小团队维护，适合特定场景但不适合大规模商业项目

**跨平台说明：**
- ✅ 表示可在 KMP / 多端项目中复用核心能力
- ❌ 表示仅限 Android 平台

---

## 推荐组合方案

### 现代 Compose 应用（推荐）
- 网络：Retrofit + OkHttp + Kotlin Coroutines（稳定、生态丰富）
- 图片：Coil（Kotlin/Compose 优先）
- 序列化：Moshi 或 Kotlinx.serialization（KMP选 kotlin x）
- 本地存储：Room + DataStore（偏好/配置用 DataStore）
- DI：Hilt（编译时注入、与 Jetpack 集成）
- 异步/流：Kotlin Coroutines + Flow
- UI：Jetpack Compose（配合 Accompanist 插件集）
- 状态管理：若复杂用 Mavericks/Orbit，简单用 纯 ViewModel
- 分页：Paging（与 Room/Network 无缝）
- 日志/调试：Timber + LeakCanary (Debug)

### 企业级/跨平台（KMP / 类型安全优先）
- 网络：Ktor（KMP 原生）或 Retrofit（若需 JVM 原生生态）
- 序列化：Kotlinx.serialization（跨平台）
- 本地数据库：SQLDelight（类型安全、KMP 支持）
- DI：Dagger/Hilt（企业级、性能可控）
- 异步：Coroutines + Flow

### 传统/兼容老项目（XML + RecyclerView）
- 网络：Retrofit + OkHttp（或保留 RxJava 的话加 RxJava）
- 图片：Glide（兼容性最佳）
- 序列化：Gson（Java 互操作友好）
- 本地存储：Room 或 Realm；偏好/小量键值用 SharedPreferences/MMKV
- DI：Dagger2（或 Hilt 逐步迁移）
- UI：XML 布局 + RecyclerView

### 轻量/快速原型
- 网络：OkHttp 或 系统 HttpURLConnection（无外部依赖）
- 图片：Coil 或 Picasso（体积小）
- KV 存储：MMKV 或 SharedPreferences
- DI：Koin（快速上手）
- 测试：JUnit + MockK

说明：以上组合按“推荐（现代）→ 企业级 → 兼容旧项目 → 轻量原型”排序。若项目已有技术栈（如 RxJava、Glide），优先考虑渐进式迁移而非全量替换。

# References list
**Networking:**
[Retrofit](https://github.com/square/retrofit)
[OkHttp](https://github.com/square/okhttp)
[Volley](https://github.com/google/volley)
[HttpURLConnection](https://developer.android.com/reference/java/net/HttpURLConnection)
[Ktor](https://github.com/ktorio/ktor)

**Animation:**
[Rive](https://rive.app/)
[MotionLayout](https://developer.android.com/jetpack/compose/layouts/motionlayout)
[Rebound](https://github.com/facebook/rebound)
[PAG](https://pag.qq.com/)
[Lottie](https://github.com/airbnb/lottie-android)
[Compose Animation](https://developer.android.com/jetpack/compose/animation)

**Image Loading:**
[Glide](https://github.com/bumptech/glide)
[Picasso](https://github.com/square/picasso)
[Coil](https://github.com/coil-kt/coil)
[Fresco](https://github.com/facebook/fresco)

**Json Serialization:**
[Gson](https://github.com/google/gson)
[Moshi](https://github.com/square/moshi)
[Kotlinx Serialization](https://github.com/Kotlin/kotlinx.serialization)
[Jackson](https://github.com/FasterXML/jackson)
[Protocol Buffers](https://developers.google.com/protocol-buffers)

**Local Database:**
[Room](https://developer.android.com/jetpack/androidx/releases/room)
[Realm](https://github.com/realm/realm-java)
[greenDAO](https://github.com/greenrobot/greenDAO)
[SQLCipher](https://www.zetetic.net/sqlcipher/)
[SQLDelight](https://github.com/cashapp/sqldelight)

**KV Storage:**
[DataStore](https://developer.android.com/topic/libraries/architecture/datastore)
[SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences)
[MMKV](https://github.com/Tencent/MMKV)
[DataStore Proto](https://developer.android.com/topic/libraries/architecture/datastore#proto)

**Dependencies Injection**
[Hilt](https://dagger.dev/hilt/)
[Dagger](https://dagger.dev/)
[Koin](https://insert-koin.io/)
[Guice](https://github.com/google/guice)
[kotlin-inject](https://github.com/evant/kotlin-inject)
[Molecule](https://github.com/cashapp/molecule)

**State Control**
[Lifecycle](https://developer.android.com/topic/libraries/architecture/lifecycle)
[Mavericks](https://github.com/airbnb/mavericks)
[Orbit MVI](https://github.com/orbit-mvi/orbit)
[ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel)
[Redux Kotlin](https://github.com/pardom/redux-kotlin)

**Async Process Application Architecture**
[Kotlin Coroutines](https://github.com/Kotlin/kotlinx.coroutines)
[RxJava](https://github.com/ReactiveX/RxJava)
[LiveData](https://developer.android.com/topic/libraries/architecture/lifecycle)
[Flow](https://github.com/Kotlin/kotlinx.coroutines)

**UI Components**
[Material Components](https://github.com/material-components/material-components-android)
[Jetpack Compose](https://developer.android.com/jetpack/compose)
[Compose Layouts](https://developer.android.com/jetpack/compose/layouts)
[ConstraintLayout](https://github.com/androidx/constraintlayout)
[RecyclerView](https://developer.android.com/jetpack/androidx/releases/recyclerview)
[Accompanist](https://github.com/google/accompanist)
[Horologist](https://github.com/google/horologist)
[Landscapist](https://github.com/skydoves/landscapist)

**Advance Functionality**
[WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager)
[Firebase](https://firebase.google.com/)
[Paging Compose](https://developer.android.com/jetpack/compose/lists#paging)
[BiometricPrompt](https://developer.android.com/training/sign-in/biometric-auth)

**APM**
[腾讯 Bugly](https://bugly.qq.com)
[友盟 Umeng](https://www.umeng.com)
[火山引擎 APM](https://www.volcengine.com/product/apm)
[阿里云日志服务 SLS](https://www.aliyun.com/product/sls)
[Sentry](https://sentry.io/)
[Firebase Crashlytics](https://firebase.google.com/products/crashlytics)
[New Relic](https://newrelic.com/)
[Datadog](https://www.datadoghq.com/)

**LogAndDebug**
[Timber](https://github.com/JakeWharton/timber)
[Stetho](https://github.com/facebook/stetho)
[LeakCanary](https://github.com/square/leakcanary)
[Logback](https://github.com/qos-ch/logback)
[Flipper](https://github.com/facebook/flipper)


**Navigation**
[Navigation](https://developer.android.com/guide/navigation)
[Compose Navigation](https://developer.android.com/jetpack/compose/navigation)
[Compose Destinations](https://github.com/raamcosta/compose-destinations)
[Fragments](https://developer.android.com/guide/fragments)
[Voyager](https://github.com/raamcosta/voyager)
[Decompose](https://github.com/arkivanov/Decompose)


**Permissions**
[Runtime Permissions](https://developer.android.com/training/permissions/requesting)
[Accompanist Permissions](https://github.com/google/accompanist/tree/main/permissions)
[PermissionX](https://github.com/guolindev/PermissionX)
[EasyPermissions](https://github.com/googlesamples/easypermissions)
[PermissionsDispatcher](https://github.com/permissions-dispatcher/permissionsdispatcher)
[RxPermissions](https://github.com/tbruyelle/RxPermissions)

**AudioVideo**
[ExoPlayer](https://github.com/google/ExoPlayer)
[Media3](https://developer.android.com/jetpack/androidx/releases/media3)
[FFmpegKit](https://github.com/FFmpegKit/FFmpegKit)
[LibVLC](https://code.videolan.org/videolan/libvlc)
[IjkPlayer](https://github.com/bilibili/ijkplayer)
[GSYVideoPlayer](https://github.com/CarGuo/GSYVideoPlayer)
[AliPlayer](https://www.alibabacloud.com/product/apsaravideo-for-player)

**Test**
[JUnit 5](https://junit.org/junit5)
[Espresso](https://developer.android.com/training/testing/espresso)
[Robolectric](https://github.com/robolectric/robolectric)
[Mockito](https://github.com/mockito/mockito)
[MockK](https://mockk.io/)
[Compose Testing](https://developer.android.com/jetpack/compose/testing)
[JMH](https://openjdk.org/projects/code-tools/jmh/)
[Roborazzi](https://github.com/takahirom/roborazzi)
[Macrobenchmark](https://developer.android.com/jetpack/androidx/releases/benchmark)
[Turbine](https://github.com/cashapp/turbine)

**Icon**
[Material Icons](https://fonts.google.com/icons)
[Material Icons Extended](https://developer.android.com/jetpack/compose/designsystems/material-icons-extended)
[Android-Iconics](https://github.com/mikepenz/Android-Iconics)
[Vector Asset Studio](https://developer.android.com/studio/write/vector-asset-studio)
[Lucid](https://github.com/lucide-icons/lucide)
[Feather Icons](https://github.com/DevSrSouza/compose-icon)
[Compose-resource-icons](https://github.com/composablehorizons/compose-icons)
