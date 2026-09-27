# Remove Chrome Context Menu Items on macOS

[English](README.md) | [简体中文](README.zh-CN.md)

This guide explains how to use a macOS configuration profile to manage Chrome Enterprise policies, remove selected context-menu items, and understand items that cannot be removed individually or have broader side effects.

In this guide, “context menu” means the menu opened by right-clicking in Chrome; it is also called the right-click menu.

## Contents

- [Available policies](#available-policies)
- [Items with limitations or trade-offs](#items-with-limitations-or-trade-offs)
- [Install and verify](#install-and-verify)
- [Restore](#restore)
- [Configuration profile notes](#configuration-profile-notes)
- [Official references](#official-references)

<a id="available-policies"></a>
## Available policies

The included profile disables these items:

| Context-menu item | Policy and value | Effect |
|---|---|---|
| Ask Gemini | `GeminiSettings = 1` | Disables Chrome's Gemini integration. |
| Get Image Descriptions from Google | `AccessibilityImageLabelsEnabled = false` | Disables Google image descriptions and the related menu item. |
| Search with Google Lens | `SearchContentSharingSettings = 1` | Disables Chrome's page and file content-sharing entry points for Lens / Google AI Mode. This policy takes over from the legacy Lens policy starting with Chrome 147. |
| Cast… | `EnableMediaRouter = false` | Disables Google Cast in Chrome, including Chrome casting entry points. |

Policy profile: [chrome-context-menu-policies.mobileconfig](chrome-context-menu-policies.mobileconfig)

<a id="items-with-limitations-or-trade-offs"></a>
## Items with limitations or trade-offs

| Context-menu item | Details |
|---|---|
| Open in Reading Mode | For Chrome 152 (Chromium 7977), no supported enterprise policy or top-level `chrome://flags` switch was found to turn off Reading Mode or hide this item. Later Chrome releases may change this. |
| Send to Your Device | There is no separate policy to hide only this item. Turning off Chrome Sync also affects bookmarks, passwords, history, and other synced data. |
| Create QR code for this page | `DesktopSharingHubEnabled = false` can disable the desktop sharing hub and make the QR action unavailable, but it does not guarantee that the context-menu row disappears. |
| Translate… | `TranslateEnabled = false` can disable Chrome's built-in translation, but also affects other translation entry points. |

The QR-code and translation policies are not included in the supplied profile because they cannot reliably hide only the corresponding context-menu item.

<a id="install-and-verify"></a>
## Install and verify

1. Open `chrome-context-menu-policies.mobileconfig` in Finder and follow macOS prompts to install the **Chrome Context Menu Policy** profile.
2. Fully quit Chrome (`⌘Q`) and reopen it.
3. Open `chrome://policy` and select **Reload policies**.
4. Confirm that `GeminiSettings`, `AccessibilityImageLabelsEnabled`, `SearchContentSharingSettings`, and `EnableMediaRouter` show the expected values and **Status: OK**.
5. Open a webpage context menu and check the items.

The profile writes policies to the `com.google.Chrome` managed-preferences domain; it does not use `chrome://flags`. `SearchContentSharingSettings` is supported in Chrome 144 and later and takes over from the legacy Lens policy starting with Chrome 147. Policies and menu behavior can change between Chrome releases, so verify the values in `chrome://policy` on the target version.

<a id="restore"></a>
## Restore

### Restore Lens and Cast while keeping Gemini and image descriptions disabled

Open [chrome-context-menu-baseline.mobileconfig](chrome-context-menu-baseline.mobileconfig) to update the profile to the baseline. The baseline keeps `GeminiSettings = 1` and `AccessibilityImageLabelsEnabled = false`, while removing `SearchContentSharingSettings` and `EnableMediaRouter`. Restart Chrome, open `chrome://policy`, select **Reload policies**, and confirm the latter two policies are no longer supplied by this profile.

If macOS does not accept the baseline as an update, remove **Chrome Context Menu Policy** under **System Settings → General → Device Management / Profiles**, then install the baseline file.

### Remove all settings in this profile

Remove **Chrome Context Menu Policy** under **System Settings → General → Device Management / Profiles**. Restart Chrome and select **Reload policies** in `chrome://policy`. If no other management source provides these policies, they will no longer appear, and Chrome will use its defaults or settings from another management source.

<a id="configuration-profile-notes"></a>
## Configuration profile notes

The main profile and baseline share the same profile identifier and UUID so macOS can update between them. These are technical profile identity fields, not usernames, device serial numbers, or browser profile paths. When creating a separate profile, use a new unique identifier and UUID, and keep them consistent between its main and baseline files. Both files are unsigned XML configuration profiles.

<a id="official-references"></a>
## Official references

- [Gemini Settings](https://chromeenterprise.google/policies/gemini-settings/)
- [Accessibility Image Labels Enabled](https://chromeenterprise.google/policies/accessibility-image-labels-enabled/)
- [Search Content Sharing Settings](https://chromeenterprise.google/policies/search-content-sharing-settings/)
- [Enable Media Router](https://chromeenterprise.google/policies/enable-media-router/)
- [Desktop Sharing Hub Enabled](https://chromeenterprise.google/policies/desktop-sharing-hub-enabled/)
- [Translate Enabled](https://chromeenterprise.google/policies/translate-enabled/)
- [View Chrome policies](https://support.google.com/chrome/a/answer/9024365?hl=en)
