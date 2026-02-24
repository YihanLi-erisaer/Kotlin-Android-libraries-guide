# Android Advanced Functions（高级系统能力）选型对比表

| 库名 | 使用场景 | 状态 | 支持 Compose | 构建侵入性 | 构建影响 | 构建治理要求 | 跨平台(KMP) | APK 增量 | 反射开销 | 性能 | 接入难度 | Gradle 插件 | 构建支持 (Build Support) | 代码生成方式 (CodeGen Strategy) | 适配难度 | API 易读性 | 调试友好度 | 协议 | 维护频率 | 大厂背书 | 优点 | 缺点 | 选型建议 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---|:---|:---|
| **WorkManager** | 后台任务、延迟任务、可靠执行 | 🔥核心 | 🔁 Flow / LiveData 适配 | 低 | 低 | 无 | ❌ | +0.2MB | ⚠️ 中（调度反射） | 中 | 中 | 否 | Gradle / AGP | ❌ 无（运行时调度） | 中 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | Google | 系统级可靠任务调度，电量与系统策略友好 | API 复杂，不适合简单定时任务 | **涉及可靠后台任务（同步、上传、日志）必选** |
| **Paging 3** | 大数据列表分页加载 | 🔥核心 | ✅ 原生 API（paging-compose） | 低 | 极低 | 无 | ✅ | +0.3MB | ✅ 低 | 🔥 高 | 中 | 否 | Gradle | ❌ 无（纯运行时 + 协程） | 中 | 优 | 中 | Apache 2.0 | ⭐⭐ 活跃 | Google | 官方分页方案，Compose 体验最佳 | 学习成本高，状态较多 | **Compose 列表分页事实标准** |
| **BiometricPrompt** | 指纹 / 人脸认证 | 🔥核心 | 🔁 Flow / 回调适配 | 无 | 无 | 无 | ❌ | 极小 | ✅ 无 | 🔥 高 | 低 | 否 | Framework | ❌ 无（系统 API） | 低 | 中 | 中 | Apache 2.0 | ⭐ 活跃 | Google | 系统级安全能力，体验统一 | UI 与能力受系统限制 | **安全认证场景必选，不建议自研** |
| **Firebase（基础能力）** | 推送 / 配置 / A/B / 基础服务 | ⚪ 可选 | 🔁 Flow / LiveData 适配 | 中 | 中 | 建议 | ✅ | +1MB↑ | ⚠️ 高 | 中 | 中 | 是 | Gradle + Google Services | ⚠️ 编译期配置 + 运行时初始化 | 中 | 优 | 中 | 商业 / 混合 | ⭐⭐ 活跃 | Google | 功能齐全、生态成熟 | 体积大、隐私与合规成本高 | **中小项目 / 快速验证阶段使用** |


# 构建与调试治理
- WorkManager 调试：利用 Android Studio 4.1+ 提供的 Background Task Inspector，可以实时查看任务的状态（Enqueued/Running/Succeeded）、排队顺序以及任务失败原因，显著降低了调试难度。
- Manifest 治理：WorkManager 会自动注入 ProcessLifecycleOwnerInitializer，如果你的项目需要极致优化启动速度，建议考虑使用 Startup 库 进行手动初始化管理。
**国内环境下的“架构红线”**
    - 后台任务治理：明确 WorkManager 不是“保活工具”。在国产 ROM（尤其是华为、OPPO、vivo）的墓碑模式下，WorkManager 的触发会被严重推迟。
        - 准则：实时性要求高的任务必须使用 Foreground Service；可靠但不紧急的任务使用 WorkManager。
    - 推送能力：Firebase Cloud Messaging (FCM) 在国内基本不可用。
        - 准则：国内项目必须集成 厂商推送（移动推送 / 各厂 SDK）。

**推荐的“高级能力”组合方案**
    - 标准架构：使用 DataStore 存储用户信息 -> Paging 3 驱动列表展示 -> WorkManager 处理埋点离线上报。
    - 权限治理：全面废弃 Fragment 传统的 onRequestPermissionsResult，改用 ActivityResult API 或 Accompanist Permissions，实现配置变更下的权限请求连续性。
