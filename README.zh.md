# Ludens

<p align="center">
  <a href="README.md">English</a> | <a href="README.es.md">Español</a> | <a href="BUILD.md">构建指南</a> | <a href="https://tryludens.vercel.app/">在线文档</a>
</p>

<p align="center">
  <a href="https://github.com/yoimerdr/ludens/releases/latest">
    <img alt="版本" src="https://img.shields.io/github/v/release/yoimerdr/ludens?display_name=tag&sort=semver&style=for-the-badge&label=Version">
  </a>
  <img alt="Android 21+" src="https://img.shields.io/badge/Android-21%2B-6EDB8D?style=for-the-badge&logo=android&logoColor=0B1F17">
  <img alt="iOS 13+" src="https://img.shields.io/badge/iOS-13%2B-CDCDCD?style=for-the-badge&logo=apple&logoColor=111827">
</p>

<p align="center">
  <a href="http://kotlinlang.org">
    <img alt="Kotlin" src="https://img.shields.io/badge/Kotlin-v2.3.0-7F52FF?style=for-the-badge&logo=kotlin&logoColor=white">
  </a>
  <a href="https://github.com/JetBrains/compose-multiplatform">
    <img alt="Compose Multiplatform" src="https://img.shields.io/badge/Compose_Multiplatform-v1.9.3-0EA5E9?style=for-the-badge&logo=jetpackcompose&logoColor=white">
  </a>
  <a href="https://github.com/KevinnZou/compose-webview-multiplatform">
    <img alt="Compose WebView" src="https://img.shields.io/badge/Compose_WebView-v2.0.3-334155?style=for-the-badge">
  </a>
  <a href="https://github.com/yoimerdr/compose-virtualjoystick-multiplatform">
    <img alt="虚拟摇杆" src="https://img.shields.io/badge/Virtual_Joystick-v1.0.0-0F172A?style=for-the-badge">
  </a>
</p>

Ludens 是一个基于 Kotlin 的 Compose Multiplatform 封装层，用于将 RPG Maker MV/MZ 游戏移植到移动设备。它将你的 HTML5 导出嵌入原生应用中，并提供可配置的移动端控件和设置。

<p align="center">
  <img src="docs/src/assets/samples/ludens-0.3.0-sample.gif" alt="Ludens 演示" width="480">
</p>

<p align="center">
  <a href="https://github.com/yoimerdr/ludens/releases/latest"><strong>下载示例 APK</strong></a>
</p>

## 特性

- 针对 RPG Maker MV/MZ 优化的 WebView 集成。
- 屏幕覆盖控制：
  - 虚拟摇杆。
  - 可配置按钮（A、B、X、Y）。
- 完整的设置界面：
  - 系统：主题（浅色/深色/跟随系统），语言（跟随系统/English/Español/中文）。
  - 工具：静音音频、显示 FPS、切换 WebGL。
  - 控件：启用/禁用、透明度、按键位置、键位映射。
  - 操作：可配置的快捷操作菜单（排序、启用/禁用）。
- 通过 [`ludens.properties`](ludens.properties) 轻松配置应用/构建。
- `composeResources/files` 中高效的资源管理。

> [!NOTE]
> 本项目不包含内置的原生存档系统。存档由 RPG Maker 在 WebView 内部管理（LocalStorage/IndexedDB）。

## 快速开始

> [!WARNING]
> iOS 构建尚未完全配置（当前为模板默认值）。本指南以 Android 为主。

> [!TIP]
> 请始终在模拟器或真机上验证。某些 RPG Maker 插件在移动端 WebView 上可能表现异常。

### 环境要求

- Android Studio Otter 2 Feature Drop (2025.2.2) 或更高版本。
  - [配置指南](https://developer.android.com/courses/pathways/android-basics-compose-unit-1-pathway-2)
- JDK 17 或更高版本。
  - RPG Maker MV/MZ 项目已导出为 Web 版本。
  - 可选：启用 [`YDP_Ludens.js`](resources/plugins/js/YDP_Ludens.js) 以获得额外兼容性/功能。
  - 在 [rpgm-plugins](https://github.com/yoimerdr/rpgm-plugins) 获取最新版本和推荐插件。
  - 建议：将其作为插件管理器中的第一个插件。
  - 兼容性：对旧版 WebView 字体加载问题有帮助。
  - 重要：请验证你所有插件的移动端兼容性。

### 导出游戏

在 RPG Maker 中使用 `Android / iOS` 导出（推荐）。如果不可用，使用 `Web Browsers`。

根据 MV/MZ 版本，你可能得到一个包含 `www` 的游戏文件夹，或只有 `www` 文件夹。两种情况下都需要 `index.html`。

### 导入资源

导入游戏资源有两种有效方式：

**选项 A（推荐）：根目录**
将完整的 `www` 文件夹放在项目根目录：
`project/www/`

> [!NOTE]
> 构建系统会在构建期间自动将这些文件同步到所需的内部位置。此自动同步仅在 `project/www/` 文件夹包含 `index.html` 以外的文件时发生。

**选项 B：内部目录**
将完整的 `www` 文件夹直接移动到 Compose 资源内部路径：
`composeApp/src/commonMain/composeResources/files/www/`

> [!WARNING]
> **大小写敏感性**：Windows 文件系统不区分大小写（`/Path/Example` = `/path/example`），但 Android 和 iOS 使用区分大小写的文件系统。请确保游戏代码中所有文件引用（图片、音频、数据文件）使用精确匹配的名称。如果文件名为 `MyImage.png`，你必须引用为 `MyImage.png`，而不是 `myimage.png`。这是移动端静默失败的常见原因。
>
> **提示**：你可以使用 rpgm-plugins 仓库中的 **[YDP_CrossAssets](https://github.com/yoimerdr/rpgm-plugins)** 插件来自动规范化文件路径，避免此问题。

导入或同步后的内部结构：

```text
files/
  └── www/
       ├── audio/
       ├── img/
       ├── js/
       ├── ...
       └── index.html
```

### Android 配置

编辑 [`ludens.properties`](ludens.properties)：

```properties
# 应用唯一标识符
ludens.android.id=com.yourorg.example

# 用户可见的版本号
ludens.android.version=1.0.0

# 整数版本代码
ludens.android.versionCode=1

# 系统中显示的应用名称
ludens.android.name=游戏名称

# 图标下方显示的短名称
ludens.android.launcherName=游戏
```

[`gradle.properties`](gradle.properties) 仍用于 Gradle/Kotlin 构建选项。Ludens 的身份和设置在 [`ludens.properties`](ludens.properties) 中配置。

### 构建

完整的发布签名流程请参考 [BUILD.md](BUILD.md)。

- Debug：`./gradlew assembleDebug`
  - 输出：`composeApp/build/outputs/apk/debug/`
- Release：`./gradlew assembleRelease`
  - 请先创建 [`keystore.properties`](keystore.properties)（参考 [`keystore.properties.template`](keystore.properties.template)）。
  - 输出：`composeApp/build/outputs/apk/release/`

### iOS

即将推出。当前配置为 Compose Multiplatform 默认模板。

## 贡献

欢迎贡献代码。重大改动请先提 Issue。

1. Fork 本仓库。
2. 创建你的分支 (`git checkout -b feature/AmazingFeature`)。
3. 提交 (`git commit -m 'Add some AmazingFeature'`)。
4. 推送 (`git push origin feature/AmazingFeature`)。
5. 开启 Pull Request。

## 许可证

Apache 2.0。详见 [`LICENSE`](LICENSE)。

## 链接

- [在线文档](https://tryludens.vercel.app/)
- [GitHub 仓库](https://github.com/yoimerdr/ludens)
- [Issue 追踪](https://github.com/yoimerdr/ludens/issues)
- [Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform.html)
- [Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/)

## 致谢

- [Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/)
- [Compose WebView Multiplatform](https://github.com/KevinnZou/compose-webview-multiplatform)
- [Compose Virtual Joystick](https://github.com/yoimerdr/compose-virtualjoystick-multiplatform)

---

如果这个项目对你有帮助，欢迎给个 ⭐️
