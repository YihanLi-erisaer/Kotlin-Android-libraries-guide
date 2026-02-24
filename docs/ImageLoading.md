# Android 图片加载（Image Loading）库对比选型表（对齐 APM 构建维度）

| 库名 | 使用场景 | 状态 | 跨平台（KMP）| 支持 Compose | 构建侵入性 | 构建影响 | 构建治理要求 | APK 增量 | 反射开销 | 性能 | 接入难度 | Gradle 插件 | 构建支持 (Build Support) | 代码生成方式 (CodeGen Strategy) | 适配难度 | API 易读性 | 调试友好度 | 协议 | 维护频率 | 大厂背书 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **Coil 3** | Compose / Kotlin 项目 | 🔥核心 | ✅ 原生支持 | ✅ 原生 API | 低 | 低 | 无 | +0.3MB | ✅ 无 | 🔥 很高 | 低 | 否 | Gradle | ❌ 无（纯运行时 + 协程） | 易 | 优 | 中 | Apache 2.0 | ⭐⭐ 活跃 | JetBrains / 社区 | Kotlin-first，Compose 体验最佳 | 极端场景性能略逊 | **Compose / Kotlin 项目默认首选** |
| **Glide 4.x/5** | 传统 View / GIF / 复杂缓存 | ⭐主流 | ❌ (JVM Only) | 🔁 AndroidView 适配/✅ 官方的 GlideCompose 库，支持原生 Compose 调用 | 中 | 中 | 建议 | +0.5MB | ⚠️ 中 | 🔥 很高 | 中 | 是 | Gradle | ⚠️ 编译期代码生成（Annotation Processor)/Glide 5.0+ 已经开始支持 KSP，不再强依赖 KAPT | 中 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | Google | 功能最全，成熟稳定 | 体积大、构建慢 | **非 Compose 或需要 GIF 的项目** |
| **Fresco** | 大图 / 内存敏感 | ⚪ 可选 | ❌ | ❌ 不支持 | 中 | 高 | 必须 | +0.8MB | ⚠️ 中 | 高 | 高 | 否 | Gradle / NDK | ❌ 无（Native Runtime） | 难 | 差 | 差 | Apache 2.0 | ⭐ 活跃 | Meta | 内存管理最强 | API 复杂、维护成本高 | **极端内存或超大图场景** |
| **Picasso** | 老项目维护 | ❌ 过时 | ❌ | ❌ 不支持 | 低 | 极低 | 无 | +0.2MB | ✅ 无 | 中 | 易 | 否 | Gradle | ❌ 无（纯运行时） | 易 | 优 | 中 | Apache 2.0 | ❌ 停止 | Square | API 简洁 | 性能与能力落后 | **仅用于老项目，不推荐新项目** |
| **Landscapist** | Compose 多后端 | ⭐ 可选 | ✅ 原生支持 | ✅ 原生 API | 低 | 低 | 无 | +0.4MB | ✅ 无 | 高 | 低 | 否 | Gradle | ❌ 无（基于其他库） | 易 | 优 | 中 | MIT | ⭐ 活跃 | 社区 | 多后端支持，Compose 友好 | 依赖其他库，性能受限 | **Compose 项目需多后端支持时** |
---

<!-- ### 优化后的图片加载（Image Loading）库对比选型表

| 库名 | 核心定位 | 跨平台 (KMP) | 支持 Compose | 构建影响 | 代码生成策略 | 性能 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **Coil 3** | **现代/跨平台首选** | ✅ **原生支持** | ✅ **原生 API** | 极低 | ❌ 无 (纯协程) | 🔥 极高 | Kotlin 优先，KMP 唯一解，无代码生成，包体积极小 | 在极低端设备的大图列表滚动表现略逊于 Glide | **Compose/KMP 项目绝对首选** |
| **Glide 4.x/5** | **功能最全/高性能** | ❌ (JVM Only) | ✅ **原生 API** | 中 | ⚠️ **KSP / APT** | 🔥 极高 | 极其深度的内存与缓存管理，GIF/视频首帧支持极佳 | 增加编译耗时，API 依然带有强烈的 Java 感 | **传统 View 项目或需要极致性能优化的商业项目** |
| **Fresco** | **大厂大图治理** | ❌ | ⚠️ 适配困难 | 高 | ❌ 无 (Native) | 高 | 内存管理（Ashmem/匿名共享内存）极强，防 OOM 效果好 | 极其臃肿，API 设计反人类，无法完美兼容 Compose | **拥有海量大图且需极致防 OOM 的超大型项目** |
| **Landscapist** | **UI 适配增强** | ✅ 支持 | ✅ 深度集成 | 低 | ❌ 无 | 中 | 为 Coil/Glide 提供极强的 Compose 骨架屏和转换动画 | 属于第三方二次包装，版本更新依赖底层库 | **需要精美图片占位和过渡动画的 Compose 项目** | -->

---

### 图片加载工程化治理红线 (Image Governance)

图片加载是 Android 应用 OOM 和内存抖动的第一大来源，必须建立治理准则：

#### A. 网络层单例治理 (Network Layer Reuse)
*   **红线**：严禁库内自行创建 `OkHttpClient`。
*   **治理要求**：必须通过 `ImageLoader` 配置，将图片请求路由到 App 统一的 **OkHttp 实例** 中。这样可以复用连接池、共享磁盘缓存并统一监控流量（APM）。

#### B. 内存与位图治理 (Bitmap Governance)
*   **准则**：默认开启 **`ALLOW_HARDWARE` (硬件位图)**。
*   **理由**：硬件位图将内存占用从 Java Heap 转移到了显存，能有效降低 OOM 概率。
*   **红线**：禁止在列表中加载原图。必须配置 `crossfade` 动画和合适的 `size` 限制，防止内存溢出。

#### C. 格式与压缩治理 (Format Governance)
*   **准则**：全面推行 **AVIF** 或 **WebP**。
*   **治理对策**：在图片请求拦截器中自动根据服务端能力添加 `Accept: image/avif, image/webp` 请求头。

#### D. 占位图规范 (Placeholder Strategy)
*   **红线**：禁止使用高分辨率位图作为 Placeholder。
*   **推荐**：使用 **ColorDrawable**、**VectorDrawable** 或 **BlurHash**（一种用简短字符串代表的模糊图）以减少内存压力。

---

### 总结：图片加载选型决策路径

1.  **你是纯 Android 项目且全面转向 Compose？**
    *   👉 **Coil 3**。
2.  **你需要做 iOS/Android 跨平台 (KMP)？**
    *   👉 **Coil 3** (目前唯一的成熟选择)。
3.  **你的项目有大量 GIF、短视频预览，或者还是以 View 为主？**
    *   👉 **Glide** (使用 KSP 版本以优化构建速度)。
4.  **你的应用是一个像 FB/微信 一样的超级 App，面临严峻的 OOM 压力？**
    *   👉 考虑 **Fresco** 的底层原理，或在 Glide/Coil 上层定制更严苛的内存回收策略。
