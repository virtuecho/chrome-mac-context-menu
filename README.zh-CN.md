# 在 macOS 上移除 Chrome 右键菜单项

[English](README.md) | [简体中文](README.zh-CN.md)

本指南介绍如何通过 macOS 配置描述文件设置 Chrome Enterprise 策略，移除部分右键菜单项，并说明哪些项目不能单独移除或会带来更广泛的功能影响。

本文中的 “context menu” 指在 Chrome 中单击右键打开的菜单，中文通常称为“右键菜单”。

## 目录

- [可用策略](#zh-available-policies)
- [有局限或功能取舍的项目](#zh-items-with-limitations-or-trade-offs)
- [安装与核验](#zh-install-and-verify)
- [恢复](#zh-restore)
- [配置描述文件说明](#zh-configuration-profile-notes)
- [官方参考](#zh-official-references)

<a id="zh-available-policies"></a>
## 可用策略

随附的配置描述文件会关闭以下菜单项：

| 右键菜单项 | 策略和值 | 效果 |
|---|---|---|
| Ask Gemini | `GeminiSettings = 1` | 禁用 Chrome 内的 Gemini 集成。 |
| Get Image Descriptions from Google | `AccessibilityImageLabelsEnabled = false` | 禁用 Google 图片描述功能及相关菜单项。 |
| Search with Google Lens | `SearchContentSharingSettings = 1` | 禁用 Chrome 中向 Lens / Google AI Mode 分享页面或文件内容的入口。Chrome 147 起，此策略接管旧 Lens 策略。 |
| Cast… | `EnableMediaRouter = false` | 禁用 Chrome Google Cast 功能，包括 Chrome 内的投放入口。 |

策略配置文件：[chrome-context-menu-policies.mobileconfig](chrome-context-menu-policies.mobileconfig)

<a id="zh-items-with-limitations-or-trade-offs"></a>
## 有局限或功能取舍的项目

| 右键菜单项 | 说明 |
|---|---|
| Open in Reading Mode | 针对 Chrome 152（Chromium 7977），未找到受支持的企业策略或 `chrome://flags` 顶层开关，可以关闭 Reading Mode 或隐藏此菜单项。后续 Chrome 版本可能会变化。 |
| Send to Your Device | 没有只隐藏此菜单项的独立策略。关闭 Chrome 同步还会影响书签、密码、历史记录及其他同步数据。 |
| Create QR code for this page | `DesktopSharingHubEnabled = false` 可以关闭桌面分享中心并使二维码操作不可用，但不能保证右键菜单项消失。 |
| Translate… | `TranslateEnabled = false` 可以关闭 Chrome 内置翻译，但也会影响其他翻译入口。 |

随附配置文件不包含二维码和翻译策略，因为它们不能可靠地只隐藏对应的右键菜单项。

<a id="zh-install-and-verify"></a>
## 安装与核验

1. 在 Finder 中打开 `chrome-context-menu-policies.mobileconfig`，按 macOS 提示安装 **Chrome Context Menu Policy** 描述文件。
2. 完全退出 Chrome（`⌘Q`）并重新打开。
3. 打开 `chrome://policy`，点击 **Reload policies**。
4. 确认 `GeminiSettings`、`AccessibilityImageLabelsEnabled`、`SearchContentSharingSettings` 和 `EnableMediaRouter` 显示预期值及 **Status: OK**。
5. 打开网页右键菜单，检查菜单项变化。

此描述文件会将策略写入 `com.google.Chrome` 管理偏好设置域，不使用 `chrome://flags`。Chrome 144 及更高版本支持 `SearchContentSharingSettings`；Chrome 147 起，此策略接管旧 Lens 策略。Chrome 版本变化可能影响策略和菜单行为，请在目标版本的 `chrome://policy` 中核验。

<a id="zh-restore"></a>
## 恢复

### 恢复 Lens 和 Cast，同时继续禁用 Gemini 与图片描述

打开 [chrome-context-menu-baseline.mobileconfig](chrome-context-menu-baseline.mobileconfig)，将描述文件更新为基线。基线保留 `GeminiSettings = 1` 和 `AccessibilityImageLabelsEnabled = false`，并移除 `SearchContentSharingSettings` 与 `EnableMediaRouter`。重启 Chrome，在 `chrome://policy` 中点击 **Reload policies**，确认后两项策略不再由此描述文件提供。

如果 macOS 不接受基线文件作为更新，请在 **系统设置 → 通用 → 设备管理 / 描述文件** 中移除 **Chrome Context Menu Policy**，再安装基线文件。

### 移除此描述文件中的全部设置

在 **系统设置 → 通用 → 设备管理 / 描述文件** 中移除 **Chrome Context Menu Policy**。重启 Chrome，并在 `chrome://policy` 中点击 **Reload policies**。如果没有其他管理来源提供这些策略，它们将不再显示，Chrome 会使用默认行为或其他管理来源设置的值。

<a id="zh-configuration-profile-notes"></a>
## 配置描述文件说明

主配置与基线使用相同的描述文件标识和 UUID，以便 macOS 在两者之间更新。这些是配置描述文件的技术身份字段，不是用户名、设备序列号或浏览器配置文件路径。创建独立描述文件时，请使用新的唯一标识和 UUID，并在主配置与基线文件中保持一致。两份文件均为未签名 XML 配置描述文件。

<a id="zh-official-references"></a>
## 官方参考

- [Gemini 设置](https://chromeenterprise.google/policies/gemini-settings/)
- [Google 图片描述辅助功能标签](https://chromeenterprise.google/policies/accessibility-image-labels-enabled/)
- [搜索内容共享设置](https://chromeenterprise.google/policies/search-content-sharing-settings/)
- [启用 Media Router](https://chromeenterprise.google/policies/enable-media-router/)
- [启用桌面分享中心](https://chromeenterprise.google/policies/desktop-sharing-hub-enabled/)
- [启用翻译](https://chromeenterprise.google/policies/translate-enabled/)
- [查看 Chrome 策略](https://support.google.com/chrome/a/answer/9024365?hl=zh-Hans)
