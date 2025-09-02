
# 一、框架描述

Apple Intents：是 Apple 公司的一个框架，从 iOS16 开始引入，其主要功能是将应用程序的核心功能和苹果设备的多种系统功能深度整合，使用户在苹果设备上无需打开应用即可执行相关操作，提升使用的便捷性和流畅度。接入 Intents 的应用，用户可以借助聚焦搜索、小组件和 “快捷指令” 应用等系统功能，更容易发现应用的内容和操作。

# 二、Apple Intents

## 2.1 核心价值
* 提升应用的可见性，让用户更易发现
* 加快用户与应用的交互效率，无需深入应用即可使用核心功能

## 2.2 应用场景
通过它，可以在系统的这些场景下被调用。
* Spotlight 搜索
* 控制中心
* 小组件
* Action 按钮（iPhone 15 Pro上新增按钮）
* Siri
* 快捷指令
  
## 2.3 App Intents
使用应用意图向系统展示你的应用功能。应用意图包含执行某项操作所需的代码，并表明你需要从系统获取的数据。系统会直接在 “快捷指令” 应用中以及像 Siri 这样的系统体验中展示你的操作。

### 2.3.1 AppIntent
AppIntent 协议的本质是标准化应用对外提供的功能接口。通过实现该协议，你可以将应用内的某个功能（如下单、分享、查询数据等）封装为 “意图”，系统或其他应用无需了解你的应用内部逻辑，只需通过这个 “意图” 即可触发对应功能。
```swift
import Foundation
import AppIntents
import SwiftUI

struct DisplayPhotoIntent: AppIntent {
    
    /// 操作名称：一个简短、本地化、易于人类理解的字符串
    static var title: LocalizedStringResource = "展示图片"
    
    /// 一种定义身份验证策略的属性，该策略指示此应用意图是否要求设备解锁或以其他方式进行身份验证。
    static var authenticationPolicy: IntentAuthenticationPolicy = .alwaysAllowed

    /// 向人们展示的应用意图描述
    static var description: IntentDescription? = "该意图支持从文件 App 中选取1张图片文件，进行展示"

    /// 它决定了 “快捷指令” 和 “聚焦搜索” 等系统功能是否可以发现此应用意图
    static var isDiscoverable: Bool = true
    
    // 让用户输入图片名称
    @Parameter(title: "请输入图片名称", description: "图片名称")
    var imageTitle: String
    
    // 文件参数，用于接收图片文件（可从文件 APP 中选取指定类型的文件）
    @Parameter(title: "请选择图片", description: "图片", supportedContentTypes: [.jpeg, .rtf, .image])
    var imageFile: IntentFile
    
    /// 不换行：定义此意图的摘要，该摘要与其参数的填充方式相关
    static var parameterSummary: some ParameterSummary {
        Summary("输入\(\.$imageTitle)，然后选择\(\.$imageFile)")
    }

    /// 换行：定义此意图的摘要，该摘要与其参数的填充方式相关
    static var parameterSummary1: some ParameterSummary {
        Summary("选择图片") {
            \.$imageTitle
            \.$imageFile
        }
    }
    
    /// 在解析提供的参数后执行该意图
    /// 当前例子中要求返回值必须同时实现这三个协议
    func perform() async throws -> some IntentResult & ProvidesDialog & ShowsSnippetView {
        
        // 执行下面的操作之前，做一次二次确认
        let confirmationDialog = IntentDialog("是否真的要执行此操作？")
        try await requestConfirmation(actionName: .view, dialog: confirmationDialog)
        
        // 创建用于显示图片和图片描述的视图
        let photoView = DisplayPhotoView(imageTitle: imageTitle, imageFile: imageFile)
        
        // donate（尚未试出来效果）
        let result: some IntentResult & ProvidesDialog & ShowsSnippetView  = .result(dialog: "展示效果", view: photoView)
        let identifier = try await donate(result: result)
        print(identifier)
        
        // 执行文件传递和处理逻辑
        return result
    }
}

// 显示 IntentFile 图片
struct DisplayPhotoView: View {
    let imageTitle: String
    let imageFile: IntentFile

    var body: some View {
        VStack {
            Text("图片名称：\(imageTitle)")
            if let uiImage = UIImage(data: imageFile.data) {
                Image(uiImage: uiImage)
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .frame(width: 200, height: 200)
            } else {
                Text("无法展示图片2")
            }
        }
    }
}
```


### 2.3.2 AudioPlaybackIntent
采用此协议可向系统表明您的应用意图是播放音频。这样，系统就能避免出现可能会中断该音频的对话或其他体验。

```swift
import AppIntents
import AVFoundation
import MediaPlayer

struct AudioIntent: AudioPlaybackIntent {
    
    static var title: LocalizedStringResource = "播放MP3"
    static var description = IntentDescription("通过快捷指令播放/暂停嵌入的MP3文件。")

    // 通过 App Intent 调用时，系统会为我们处理会话激活
    func perform() async throws -> some IntentResult {
        if AudioPlayerManager.shared.isPlaying {
            AudioPlayerManager.shared.pause()
        } else {
            AudioPlayerManager.shared.play()
        }
        return .result()
    }
}


final class AudioPlayerManager: NSObject {
    
    // 采用单例来管理播放/暂停的状态
    static let shared = AudioPlayerManager()
    
    private var player: AVAudioPlayer?
    private var playbackTime: TimeInterval = 0.0
    private var isInterrupted = false

    private override init() {
        super.init()
        setupAudioSession()
        setupInterruptionObserver()
    }
    
    deinit {
        NotificationCenter.default.removeObserver(self)
    }

    private func setupAudioSession() {
        let audioSession = AVAudioSession.sharedInstance()
        do {
            // 要支持不打开应用持续播放音频，就需要设置为 playback
            // 并且 info.plist 要支持音频后台播放
            try audioSession.setCategory(.playback, mode: .default, options: [])
        } catch {
            print("Failed to set audio session category: \(error.localizedDescription)")
        }
    }
    
    private func setupInterruptionObserver() {
        NotificationCenter.default.addObserver(self,
                                               selector: #selector(handleInterruption),
                                               name: AVAudioSession.interruptionNotification,
                                               object: nil)
    }
    
    /// 处理播放中断，有可能是播放结束，也有可能是被其他音频操作中断
    @objc func handleInterruption(notification: Notification) {
        guard let info = notification.userInfo,
              let typeValue = info[AVAudioSessionInterruptionTypeKey] as? UInt,
              let type = AVAudioSession.InterruptionType(rawValue: typeValue) else {
            return
        }

        switch type {
        case .began:
            print("Audio session interruption began.")
            isInterrupted = true
            // 保存当前播放时间
            playbackTime = player?.currentTime ?? 0.0
            player?.pause()
        case .ended:
            print("Audio session interruption ended.")
            isInterrupted = false
            guard let optionsValue = info[AVAudioSessionInterruptionOptionKey] as? UInt else { return }
            let options = AVAudioSession.InterruptionOptions(rawValue: optionsValue)
            if options.contains(.shouldResume) {
                // 如果中断结束后应恢复，则尝试重新激活会话并播放
                try? AVAudioSession.sharedInstance().setActive(true)
                if playbackTime > 0 {
                    player?.currentTime = playbackTime
                }
                player?.play()
            }
        @unknown default:
            break
        }
    }

    private func getPlayer() -> AVAudioPlayer? {
        if player == nil {
            guard let fileURL = Bundle.main.url(forResource: "pm", withExtension: "mp3") else {
                print("MP3 file not found.")
                return nil
            }
            do {
                player = try AVAudioPlayer(contentsOf: fileURL)
            } catch {
                print("AVAudioPlayer failed to initialize: \(error.localizedDescription)")
                return nil
            }
        }
        return player
    }

    func play() {
        print("Playing audio.")
        try? AVAudioSession.sharedInstance().setActive(true)
        guard let player = getPlayer() else { return }
        
        // 如果是中断后恢复，从中断时间点继续播放
        if playbackTime > 0 {
            player.currentTime = playbackTime
            playbackTime = 0.0
        }
        player.play()
        
    }

    func pause() {
        print("Pausing audio.")
        player?.pause()
        playbackTime = player?.currentTime ?? 0.0
        try? AVAudioSession.sharedInstance().setActive(false)
    }

    var isPlaying: Bool {
        return player?.isPlaying ?? false
    }
}
```


### 2.3.3 AudioRecordingIntent
采用此协议来创建一个用于音频录制功能的应用意图，并告知系统你的应用正在录制音频。通过该意图，系统会显示一个音频录制指示器。
由于我们没有开发者证书，故 demo 跑不起来。大致描述下思路：
1. 工程配置与准备
  - 配置麦克风访问权限需要
  - 创建 1 个Widget Extension Target，并勾选 Include Live Activity
  - 主 target 的 info.plist 文件中添加 NSSupportsLiveActivities 键并设置为 YES
  - 开启通知权限（这个是 Live Activity 的必要条件）
2. App Intents 实现
3. 音频录制与会话管理
4. 实时活动（Live Activity）的 UI
5. 快捷指令集成

### 2.3.4 CameraCaptureIntent
是一种应用意图，开发者可借助它从锁屏、操作按钮或控制中心等系统界面直接在其应用中启动拍摄体验。

### 2.3.5 DeleteIntent
用于表达应用删除一个或多个实体的能力。通过采用该协议，开发者可将应用的删除功能开放给 Siri 和 “快捷指令” 应用等系统体验。这使得用户能够通过语音命令或快捷指令删除应用内容，而无需在应用内导航至删除选项。

### 2.3.6 OpenIntent
用于表达应用打开特定项目或导航到特定视图的能力。它在从系统体验（如 “快捷指令” 应用、聚焦搜索或控制中心小组件）深度链接到应用时特别有用。

### 2.3.7 OpenURLIntent
是 iOS 18 中预先定义的应用意图，用于打开通用链接。它并非供你在自己的应用意图中直接实现，而是用于从另一个意图的 perform () 方法中作为 IntentResult 返回。

### 2.3.8 PlayVideoIntent
是 iOS 18 中一个预定义的应用意图，专为媒体类应用设计。它允许用户借助 Siri 或 “快捷指令” 应用等系统体验，基于搜索词或其他条件在应用内播放视频。

### 2.3.9 ProgressReportingIntent
它允许开发者向系统提供长时间运行操作的实时进度更新。通过采用该协议，你的应用可以向 “快捷指令” 应用和 Siri 等系统体验告知意图执行的状态。

### 2.3.10 PushToTalkTransmissionIntent
是一种应用程序意图协议，开发者可借助它手动开始或结束与苹果的 “一键通” 框架之间的音频传输。这是针对实现对讲机风格功能的应用程序的一种特定意图，能让用户从系统体验中控制音频传输。

### 2.3.11 URLRepresentableIntent
为应用意图添加了 URL 表示形式，使 Siri 和快捷指令等系统体验能够将该意图当作通用链接来处理。其核心功能是让用户能够深度链接到应用内的特定内容，使指向该内容的链接可共享，并调用应用现有的 URL 处理逻辑。

### 2.3.12 SetValueIntent
用于定义一个应用意图，该意图用于设置特定值，尤其适用于系统控制，如控制中心的开关或交互式小组件上的开关。其功能是允许用户直接从系统级界面更改应用中的设置或状态。

### 2.3.13 ShowInAppSearchResultsIntent
是一种预定义的应用意图协议，它允许应用直接将用户带到包含特定搜索词搜索结果的视图。通过采用此协议，开发者能够将其应用的搜索功能与 Siri 和 “快捷指令” 应用等系统级体验相集成。

### 2.3.14 ControlConfigurationIntent
是一种应用意图，用于为自定义控制中心模块定义配置选项。通过采用该协议，开发者可以定义一组参数，用户可以通过与这些参数交互来个性化控制项，例如选择该控制项应影响的特定模式或项目。

### 2.3.15 LiveActivityIntent
它允许开发者通过系统级操作直接管理实时活动的生命周期。通过采用该协议，应用程序可以借助快捷指令应用、Siri 或交互式小组件等功能，来开启、暂停或以其他方式修改实时活动。

### 2.3.16 WidgetConfigurationIntent
用于定义 WidgetKit 小组件的用户可配置属性。它允许用户直接从小组件的配置界面自定义小组件的内容，从而提供更具个性化和相关性的体验。

## 2.4 Intent Discovery
通过向系统贡献意图，让人们更容易发现你的意图。当有人在你的应用中执行某个操作时，贡献一个与该操作对应的意图。系统会利用你提供的信息来预测用户未来可能采取的操作。例如，如果有人每天早上都通过你的应用查询天气，系统可能会在每天的同一时间主动提供相应的应用意图。
只有当有人直接使用你的应用界面时，才提交意图。你无需提交与 Siri 相关的意图或与 “快捷指令” 应用的交互，因为系统会自动提交这些内容。当有人取消或撤销之前执行的操作，或者当该操作不再相关时，你也可以删除已提交的内容。

## 2.5 App ShortCuts
将应用的意图和实体与快捷指令应用、Siri、聚焦搜索以及支持该功能的 iPhone 和 Apple Watch 机型上的操作按钮相集成。
创建一个预配置的应用快捷指令，让人们无需任何配置就能发现并运行你的应用意图。通过创建应用快捷指令，从用户安装你的应用那一刻起，你的应用功能就能立即在 “快捷指令”、“聚焦搜索” 和 “Siri” 中使用 —— 无需在 “快捷指令” 应用中进行任何设置，也无需点击 “添加到 Siri” 按钮。在支持操作按钮的 iPhone 机型上，人们可以将你的预配置应用快捷指令与操作按钮相关联，以便快速使用你的应用功能。

* 创建快捷指令
  
```swift
import Foundation
import AppIntents
import SwiftUI

@main
struct MyAppIntentsApp: App {
    init() {
        AppShortcuts.updateAppShortcutParameters()
    }
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

// 实现 AppShortcutsProvider 协议，设置快捷指令列表即可
// intent：上文中我们配置的播放暂停音乐的意图
// phrases：这种表达，可以通过 siri 来调用这条指令
// shortTitle：这个文案是我们这条意图在快捷指令 APP 中展示的标题，也可以通过全局搜索进行检索到
// systemImageName: 显示在快捷指令 APP 中的快捷指令图标
struct AppShortcuts: AppShortcutsProvider {
    static var appShortcuts: [AppShortcut] {
        AppShortcut(intent: AudioIntent(),
            phrases: ["在\(.applicationName)应用中播放音乐"],
            shortTitle: "播放音乐",
            systemImageName: "music.note")
    }
}

```

* 自定义快捷指令磁贴颜色
  
AppShortcutsProvider 中的静态变量 shortcutTileColor 设置颜色没有效果。正确的解决方法是在 Info.plist文件中添加如下的键值对，其中ShortcutsForeground 、ShortcutsBackground1 和 ShortcutsBackground2需要在 Assets 中配置颜色。

```xml
<key>CFBundleIcons</key>
<dict>
   <key>CFBundlePrimaryIcon</key>
   <dict>
       <key>NSAppIconActionTintColorName</key>
       <string>ShortcutsForeground</string>
       <key>NSAppIconComplementingColorNames</key>
       <array>
           <string>ShortcutsBackground1</string>
           <string>ShortcutsBackground2</string>
       </array>
   </dict>
</dict>
```


## 2.6 Parameter resolution
定义你的应用意图所需的参数，并指定如何在运行时解析这些参数。
参数代表应用意图的输入参数，并向系统提供额外的元数据。当你定义一个应用意图时，要为任何用作输入的属性添加 @Parameter 属性包装器

## 2.7 App entities
通过将核心类型或概念声明为应用实体，使系统能够发现它们。以下是一个 demo 示例：

* BookEntity，必须实现 AppEntity 和 Identifiable 协议
```swift
import AppIntents
import SwiftUI

struct BookEntity: AppEntity, Identifiable {
    // 符合 `Identifiable` 以实现唯一标识。系统使用此 ID 来获取特定实体。
    var id: UUID
    // 书名
    var title: String
    // 作者
    var author: String
    
    /// 此实体的默认查询，这是 `AppEntity` 协议所必需的。 
    /// 这告诉系统如何查找和推荐图书。
    static var defaultQuery = BookQuery()
    
    /// 提供实体的用户友好表示。 
    // 例如，这是用户在“快捷方式”应用中看到的内容。
    var displayRepresentation: DisplayRepresentation {
        DisplayRepresentation(
            title: .init(stringLiteral: title),
            subtitle: .init(stringLiteral: author)
        )
    }
    
    /// 提供实体类型的可视化表示
    static var typeDisplayRepresentation: TypeDisplayRepresentation = "书"
    
    // MARK: - Query Implementation
    
    /// 自定义的“EntityQuery”定义系统如何找到我们的“BookEntity”实例
    struct BookQuery: EntityQuery {
        /// 所有可用书籍的数组。可以用数据库获取来代替。
        private var allBooks: [BookEntity] = [
            BookEntity(id: UUID(), title: "火星救援", author: "Andy Weir"),
            BookEntity(id: UUID(), title: "挽救计划", author: "Andy Weir"),
            BookEntity(id: UUID(), title: "沙丘", author: "Frank Herbert")
        ]
        /// 获取给定标识符集合的实体.
        /// 当系统已经知道所需实体的 ID 时使用
        func entities(for identifiers: [UUID]) async throws -> [BookEntity] {
            return allBooks.filter { identifiers.contains($0.id) }
        }
        
        /// 提供建议实体列表以显示给用户
        /// 快捷方式应用程序使用它来填充动态选项
        func suggestedEntities() async throws -> [BookEntity] {
            return allBooks
        }
    }
}
```

* BookCategory

```swift
import AppIntents
import SwiftUI

/// 一个遵循AppEnum的枚举，用于提供一组固定的选项。
/// 这非常适合选项已知且不会改变的情况，例如应用的各个部分。
enum BookCategory: String, AppEnum {
    case fiction = "虚构类"
    case nonFiction = "写实类"
    case scienceFiction = "科幻类"
    
    /// 提供快捷方式应用中枚举类型的显示名称
    static var typeDisplayRepresentation: TypeDisplayRepresentation = "书籍分类"
    
    /// 为每个枚举情况提供用户友好的表示形式
    static var caseDisplayRepresentations: [BookCategory: DisplayRepresentation] = [
        .fiction: DisplayRepresentation(title: "虚构类", image: .init(named: "虚构")),
        .nonFiction: DisplayRepresentation(title: "写实类", image: .init(named: "写实")),
        .scienceFiction: DisplayRepresentation(title: "科幻类", image: .init(named: "科幻"))
    ]
}
```

* GetBookIntent

```swift
import AppIntents
import SwiftUI

/// 一个用于检索并显示所选书籍信息的应用意图。
struct GetBookInfoIntent: AppIntent {
    
    /// 意图的标题，显示在 Siri 和快捷指令中。
    static var title: LocalizedStringResource = "查询书籍信息"
    
    /// 描述为用户提供了更多描述信息
    static var description = IntentDescription("从图书馆查找书籍信息")
    
    /// 一个使用我们的 `BookEntity` 的参数，允许用户动态选择一本书。 
    /// 我们之前定义的 `BookQuery` 将提供选项列表。
    @Parameter(title: "选择书名")
    var book: BookEntity
    
    /// 一个使用我们的BookCategory枚举的参数，提供静态选项列表。
    @Parameter(title: "选择分类")
    var category: BookCategory
    
    /// 意图执行时运行的核心逻辑。
    func perform() async throws -> some IntentResult & ProvidesDialog & ShowsSnippetView {
        return .result(dialog: "检索结果", view: ResultView(book: book, category: category))
    }
}

// 显示结果
struct ResultView: View {
    let book: BookEntity
    let category: BookCategory

    var body: some View {
        VStack {
            Text("书名：\(book.title)")
            Text("作者：\(book.author)")
            Text("类型: \(category.rawValue)")
        }
    }
}

```

## 2.7 Entity queries
帮助系统找到您的应用定义的实体并使用它们来解析参数。
当系统需要检索应用程序实体的一个或多个特定实例时，它会要求你提供相关的查询类型。当意图的参数包含实体时，系统会在参数解析过程中使用查询。系统还会利用查询将不同格式的信息解析为你的应用程序的某个实体。例如，它会用查询将自然口语解析为你的应用程序的某个实体。
该系统有时能够确定它所需的实体，并为你提供相应标识符的列表。请提供一个 EntityQuery 类型来为这些标识符提供实体。提供其他查询类型以执行更高级的搜索，例如匹配实体特定属性的搜索。

## 2.8 Resolvers
解析你的应用意图的参数，并扩展标准解析类型以包含你的应用的类型。
像 Siri 和 “快捷指令” 应用这类系统体验所产生的输入，并非总能与你的代码需求相匹配。例如，来自 Siri 的自然口语指令是字符串，但你的应用意图可能需要的是整数或浮点值。解析器能让系统自动将一种类型转换为另一种类型。
该系统提供解析器，用于在整数、浮点数、布尔值、字符串和 URL 类型之间进行转换。根据需要，系统可以将多个解析器链接起来，以在没有单个解析器可用的类型之间进行转换。例如，它可以先将整数转换为字符串，然后再将该字符串转换为布尔值。如果你的应用程序定义了自定义类型，请创建自己的解析器，将这些类型转换为更易识别的值。

## 2.9 Common types
指定你的应用程序支持的常见类型，包括货币、文件和联系人。在为应用意图创建参数或为应用实体创建属性时，请使用这些类型来管理特定类型的数据。
* IntentPerson
* IntentFile
* IntentCurrencyAmount
* IntentPaymentMethod
* IntentItem
* IntentItemCollection
* IntentItemSection
* IntentCollectionSize

## 2.10 AppIntentError
意图处理代码在解释或执行应用意图时，可返回的用于指示问题的错误。

## 2.11 Protocols
* AppIntentSceneDelegate
* AppShortcutsContent
* CustomURLRepresentationParameterConvertible
* ShowsSnippetIntent
* TargetContentProvidingIntent
* UISceneAppIntent
* UndoableIntent

## 2.12 Structures
* ConfirmationConditions
* EntityPropertyModifiers
* EntityURLRepresentation
* EnumURLRepresentation
* FileEntityIdentifier
* IntentChoiceOption
* IntentModes
* IntentURLRepresentation

## 2.13 与其他框架配合使用

### 2.13.1 WidgetKit
使用 WidgetKit，您可以使您的应用程序内容在应用程序外部环境中可用，并通过构建一个可一目了然、最新体验的生态系统来扩展其覆盖范围。小组件可被放置在以下位置：
* 今日视图
* 主屏幕和锁定屏幕
* 主屏幕上堆叠小组件
* 实时活动会在锁屏或灵动岛显示来自你的应用的最新内容
* 控制中心和锁屏界面中的按钮、Action 按钮

### 2.13.2 ActivityKit
利用 ActivityKit 框架，你可以创建实时活动，以丰富的互动方式在多个平台上（如 iPad、iPhone、Apple Watch、Mac 和 CarPlay）展示应用中的实时更新，尤其适合需要频繁更新的应用，如体育赛事追踪。实时活动不仅显示在显眼位置（如锁屏、灵动岛、主屏幕等），还允许用户在不打开应用的情况下，通过内置按钮执行基本操作或快速进入相关应用场景。在开发过程中，你可以使用 ActivityKit 来管理实时活动的生命周期，并借助 WidgetKit 和 SwiftUI 构建用户界面，实现代码共享。与小组件不同，实时活动通过 ActivityKit 和推送通知接收更新，而非时间线机制。

https://developer.apple.com/documentation/ActivityKit

### 2.13.3 Core Spotlight
为你的应用添加搜索功能，并为你的内容建立索引，这样人们就能从 Spotlight 和 Safari 中找到它。

https://developer.apple.com/documentation/CoreSpotlight

### 2.13.4 Siri && App Intelligence
Apple Intelligence 是一个全新的个人智能系统，它将强大的生成式模型深度集成到 iPhone、iPad 和 Mac 的核心之中。Siri 将借助 Apple Intelligence 的能力，为用户提供更自然、与上下文更相关且更具个性化的辅助服务。

https://developer.apple.com/documentation/appintents/integrating-actions-with-siri-and-apple-intelligence


# 参考文档
https://developer.apple.com/documentation/appintents/appintent

* App Intents Domains：
  
https://developer.apple.com/documentation/appintents/appintent(schema:)
https://developer.apple.com/documentation/appintents/appentity(schema:)
https://developer.apple.com/documentation/appintents/appenum(schema:)
