# iOS 开发入门：从项目到真机运行

本文面向有编程经验、第一次接触 iOS 开发的读者，使用当前 Apple 工具链中常见的 Swift、SwiftUI 和 Xcode 工作流程。Xcode 菜单、最低系统版本和发布要求会随版本变化，请以 Apple 当前文档和开发者账户中的提示为准。

## 1. iOS 应用由什么组成？

- **Swift**：Apple 平台应用常用的编程语言。
- **SwiftUI**：用声明式方式描述界面和界面状态的框架。现有项目也可能使用 UIKit，二者可以在同一应用中协作。
- **Xcode**：项目管理、代码编辑、构建、调试、模拟器测试和应用分发工具。
- **Apple SDK**：提供系统 API、界面组件、设备能力和模拟器支持。

做 iOS 开发需要运行 Xcode 的受支持 macOS 环境。请从 Mac App Store 或 Apple Developer 网站获取 Xcode，并安装项目要求的 SDK 和模拟器运行时。

## 2. 创建并运行一个 SwiftUI 应用

在 Xcode 中创建新项目，选择 iOS 的 App 模板，界面选 SwiftUI，语言选 Swift。为项目设置名称、组织标识符和保存位置，然后等待 Xcode 完成初始化。

最小的 SwiftUI 界面示例：

```swift
import SwiftUI

@main
struct FirstApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

struct ContentView: View {
    @State private var count = 0

    var body: some View {
        VStack(spacing: 16) {
            Text("点击次数：\(count)")
            Button("增加") {
                count += 1
            }
        }
        .padding()
    }
}
```

`@main` 标记应用入口。`View` 描述界面；`@State` 保存这个视图拥有的可变状态，状态改变时 SwiftUI 会刷新相关界面。按钮点击通过闭包更新状态。

在 Xcode 工具栏选择一个模拟器设备，按 Run（通常是 `⌘R`）进行构建并运行。修改文字或按钮行为，再次运行，观察界面变化。模拟器适合快速检查布局和常见交互，但不能代替所有真机测试。

## 3. 项目与代码的基本结构

- 项目导航器显示源码、资源、测试和项目设置。
- `ContentView.swift` 通常放置 SwiftUI 界面；复杂项目应按功能拆分视图、模型和服务。
- Assets 目录管理应用图标、图片和颜色等资源。
- 项目的 Target 设置应用标识符、版本、签名能力和部署目标。
- Swift Package Manager 可用于管理 Swift 软件包依赖；添加依赖前应评估其来源、维护情况和许可。

Swift 使用类型、结构体、枚举、类、函数和协议组织代码。初学时可先掌握变量与常量、函数、条件和循环、集合、可选值，以及 `struct` 和 `enum`。不要将强制解包（`!`）当作处理可选值的默认方式。

## 4. 状态、界面和系统权限

SwiftUI 视图通常是值类型，界面应由状态推导，而不是手动保存并修改每个控件。视图自身的短期状态可用 `@State`；需要跨视图共享的数据应根据项目的部署目标和架构选择合适的观察模型。

访问相机、照片、麦克风、位置等受保护能力时：

1. 仅在确实需要时请求权限，并向用户清楚说明用途。
2. 在项目配置和应用说明中提供系统要求的用途描述。
3. 正确处理拒绝、受限和权限后来被撤销的情形。
4. 遵循 Apple 的隐私与数据使用要求，不收集功能不需要的数据。

系统 API、权限流程和配置键会因能力和 SDK 版本而异，请查阅对应的 Apple 文档。

## 5. 调试与测试

- 在代码左侧单击设置断点；运行到断点后可检查变量、调用栈和执行流程。
- 使用 Xcode 控制台查看诊断信息，避免记录密码、令牌或个人敏感数据。
- 使用不同尺寸和系统版本的模拟器检查布局、导航和基础交互。
- 为逻辑代码编写单元测试，为关键用户流程编写界面测试。
- 至少在目标真机上验证权限弹窗、相机、定位、推送、性能和实际设备布局等相关功能。
- 构建失败时先阅读 Issue Navigator 中的第一条相关错误，确认 SDK、部署目标和依赖设置，再逐项修复。

## 6. 在真机上运行

将设备连接到 Mac，解锁设备并按提示信任电脑。在 Xcode 中选择设备作为运行目标。项目通常使用 Apple ID 团队配置开发签名；Xcode 会根据账户权限和项目设置处理签名及设备安装。

组织团队、付费开发者计划、证书和受管理设备的配置可能有所不同。不要共享私钥或把签名凭据提交到版本控制。遇到签名错误时，检查 Apple Developer 账户权限、Bundle Identifier、Team 和 provisioning 配置，并参考 Xcode 的错误说明。

## 7. 分发与 App Store 发布

在发布前确认应用标识符、版本号、图标、隐私用途说明、权限行为和目标设备体验。用 Xcode 创建归档（Archive），通过 Organizer 按当前支持的流程上传构建版本，再在 App Store Connect 中补齐商店信息、隐私披露和审核材料。

内部或外部测试可通过 Apple 当前提供的测试分发方式进行，例如 TestFlight。正式发布需要满足提交时适用的审核规则、隐私要求和技术要求。不要依赖手工把 `.app` 文件改名或压缩成 `.ipa` 的旧方法；使用 Xcode 的受支持归档和分发流程。

## 8. 下一步学习

1. 熟悉 Swift 的类型、可选值、集合、函数和错误处理。
2. 学习 SwiftUI 布局、导航、表单和状态管理。
3. 为简单的网络请求增加加载、失败和空数据状态。
4. 了解并发、持久化、可访问性、本地化及应用生命周期。
5. 查阅 Apple Developer Documentation 和 Swift 官方文档，按项目最低系统版本选择 API。

参考资料：

- Swift 文档：<https://www.swift.org/documentation/>
- Apple Developer 文档：<https://developer.apple.com/documentation/>
- SwiftUI：<https://developer.apple.com/documentation/swiftui>
