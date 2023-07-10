> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [xiaozhuanlan.com](https://xiaozhuanlan.com/topic/2586749130)

> WWDC23 内参 - @老司机技术 - 摘要：通过 SwiftUI 的 ImmersiveSpace，我们能够轻松的在 visionOS 上创造完全沉浸式的体验。

> 摘要：通过 SwiftUI 的 ImmersiveSpace，我们能够轻松的在 visionOS 上创造完全沉浸式的体验。在 Space 中，我们可以使用 `RealityView` 、`Model3D` 等展示内容，可以使用 `scenePhase`、`GeometryReader3D`、`ImmersionStyle`等更好的管理 Space，此外，还有一些自定义功能也值得我们探索。

本文基于 [Session 10111](https://developer.apple.com/videos/play/wwdc2023/10111/) 梳理。

> 作者：
> 
> Layer，就职于抖音即时通讯团队，APP：**喵喵消烦员** 开发者。
> 
> 审核：
> 
> 戴铭，极客时间《iOS 开发高手课》和纸书《跟戴铭学 iOS 编程》作者。
> 
> 黄骋志，老司机技术轮值主编，目前就职于字节跳动，参与西瓜视频质量与稳定性工作。对 OOM/Watchdog 较为了解并长期投入。

在 visionOS 上通过一些功能强大且易于使用的 API，我们能够轻松创造完全沉浸式的体验。所有这一切都可以通过我们已经熟悉的工具、框架和模式来实现。其核心是 SwiftUI 的 [`ImmersiveSpace`](https://developer.apple.com/documentation/swiftui/immersivespace)。本文我们将围绕 Space 展开，介绍包括 Space 的信息、如何在 Space 中展示内容和更好的管理 Space，此外，还会介绍一些自定义功能。

![](https://images.xiaozhuanlan.com/photo/2023/29a0b5b25c8d5afa697386860ec55263.png)

iOS 和 iPadOS 上丰富的 AR 体验

> 文章涉及的概念主要包括 SwiftUI 的 `App`、`Scene`、`View` 协议，visionOS 下的 Window、Volume、Space。详细的内容将在文中展开，以下是概念简述：
> 
> <table><thead><tr><th>概念</th><th>简述</th></tr></thead><tbody><tr><td><a href="https://developer.apple.com/documentation/swiftui/app" target="_blank"><code>App</code></a></td><td>表示应用程序的结构和行为的类型，通过声明符合 <code>App</code> 协议的结构来创建应用程序。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/scene" target="_blank"><code>Scene</code></a></td><td>应用程序用户界面的一部分，充当要向用户显示的 <code>View</code> 层次结构的容器。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/view" target="_blank"><code>View</code></a></td><td>应用程序用户界面的一部分，过声明符合 <code>View</code> 协议的类型来创建视图。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/window" target="_blank">Window</a></td><td>在独特的窗口中呈现其内容的 Scene。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/windowstyle/volumetric/" target="_blank">Volume</a></td><td>在有界区域内托管 3D 内容，是 Window Scene 的一种样式。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/immersivespace" target="_blank">Space</a></td><td>在无限的空间中呈现其内容的 Scene。</td></tr></tbody></table>

进入沉浸式体验
-------

在过去的几年中，Apple 引入和完善了许多工具及框架，用于为 iOS 和 iPadOS 等构建 AR 应用程序，例如 [ARKit](https://developer.apple.com/augmented-reality/arkit/)、[RealityKit](https://developer.apple.com/augmented-reality/realitykit/) 等。这些应用程序通过交互式的用户界面和虚拟对象来增强用户环境，模糊现实世界与想象之间的界限，创造了丰富的 AR 体验。

![](https://images.xiaozhuanlan.com/photo/2023/92ac515e6db29f0b5e96fe2ac075d981.png)

iOS 和 iPadOS 上丰富的 AR 体验

在 SwiftUI2.0 中，Apple 提供了全新的 [`App`](https://developer.apple.com/documentation/swiftui/app)、[`Scene`](https://developer.apple.com/documentation/swiftui/scene) 协议，使代码变得更清晰：

```
@main


struct WorldApp: App {


var body: some Scene {


WindowGroup {


Text("Hello world")


        }


    }


}

```

通在符合 `App` 协议的结构的声明之前加上 [`@main`](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#ID626) 属性，以指示该结构提供进入应用程序的入口点。`Scene` 是 `View` 层次结构的容器，通过在 `App` 实例的 `body`中组合一个或多个符合 `Scene` 协议的实例来呈现具体程序。SwiftUI2.0 提供了预置的 Scene，此外，用户也可以自己编写符合 `Scene` 协议的场景。预置的 Scene 包括 [`WindowGroup`](https://developer.apple.com/documentation/swiftui/windowgroup)、[`DocumentGroup`](https://developer.apple.com/documentation/swiftui/documentgroup)，macOS 使用的 [`Window`](https://developer.apple.com/documentation/swiftui/window)、[`Settings`](https://developer.apple.com/documentation/swiftui/settings)，watchOS 使用的 [`WKNotificationScene`](https://developer.apple.com/documentation/swiftui/wknotificationscene)。

在 WWDC23 [Take SwiftUI to the next dimension](https://developer.apple.com/videos/play/wwdc2023/10113) Session 中，Apple 详细介绍了 SwiftUI 中的第三维，可以在 visionOS 上呈现 Window 或 Volume。

*   Window：我们可以在 visionOS 应用中创建一个或多个的窗口。可以包含传统的视图或者控件，也可以通过添加 3D 内容来增加深度上的体验。如下左图的紫色、蓝色、红色 Window。Window 即常规的 `WindowGroup` 在 visionOS 上的表现。
*   Volume：Volume 为我们提供了一个固定比例的容器，在任何距离都保持相同的大小，支持从任何角度查看。Volume 是在应用程序中显示 3D 内容的好方法，同时不会占用整个空间。如下左图的绿色 Volume。创建 Volume 非常简单，只需在创建 Scene 时使用新的 [`.volumetric`](https://developer.apple.com/documentation/swiftui/windowstyle/volumetric/) 的 [`windowStyle(_:)`](https://developer.apple.com/documentation/swiftui/scene/windowstyle(_:)) 样式：

```
WindowGroup {


GlobeView()


}


.windowStyle(.volumetric)

```

<table><thead><tr><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/a9febe7b32168040c80f54bc0d13bb88.png"><p>用户 和 Window、Volume</p></th><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/e0d2c8d157ec3808aef1d0ad920dead8.png"><p>Windows 和 Volumes</p></th></tr></thead><tbody></tbody></table>

> 符合 `WindowStyle` 协议的样式，包括只服务于 macOS 的 [`hiddentitlebar`](https://developer.apple.com/documentation/swiftui/windowstyle/hiddentitlebar)、[`titleBar`](https://developer.apple.com/documentation/swiftui/windowstyle/titlebar)，服务于 macOS 和 visionOS 的 [`automatic`](https://developer.apple.com/documentation/swiftui/windowstyle/automatic)、以及只服务于 visionOS [`plain`](https://developer.apple.com/documentation/swiftui/windowstyle/plain)、[`volumetric`](https://developer.apple.com/documentation/swiftui/windowstyle/volumetric)。`automatic` 和 `plain` 即我们上文提到的 Window，但 `automatic` 作为普通 Window，与 `plain` 的差异在于是否在 visionOS 中展示玻璃背景效果。

但 Volume 并不是 “只能达到” 的体验，我们还可以更充分的利用 visionOS 提供的无限空间，创造身临其境的体验。比如说我们想把物体放在 Window 之外、用户周围—— **Space** 是另一种在 visionOS 上呈现用户界面的容器，可以为用户创建身临其境的体验。

<table><thead><tr><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/1e65595fed6b7d97e45448ac305e8227.png"><p>Windows、Volumes 以及 Spaces</p></th><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/68cc049068329e9778fa288cada6068a.png"><p>无限空间</p></th></tr></thead><tbody></tbody></table>

通过 Space，Apple 将 AR 提升到一个全新的水平 —— **沉浸式体验 (Immersive experience)**。应用程序会在用户周围显示窗口、三维内容等。开发者可以将虚拟对象或效果等锚定到物体表面，从而增强和丰富现实世界。同时，用户所处的现实环境也会作为用户体验的一部分，对用户仍然可见。

更进一步的是**完全沉浸式体验 (Fully immersive experience)**，应用程序可以完全控制用户所看到的内容，覆盖整个空间，这将解锁所有的可能！

<table><thead><tr><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/ae03216fe85a8ce46a5aa1d746f77d9b.gif"><p>沉浸式体验</p></th><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/9a28b4bc6ca118189ef548c760e1206a.gif"><p>完全沉浸式体验</p></th></tr></thead><tbody><tr><td>沉浸式体验 (Immersive experience)</td><td>完全沉浸式体验 ((Fully immersive experience)</td></tr></tbody></table>

我们将围绕沉浸式体验的核心—— SwiftUI 的 `ImmersiveSpace` 展开。

初识 Space
--------

我们以太空探索主题的 World 应用程序为例，增加一个探索太空的 Space。Space 是 SwiftUI 中的一种新的 Scene Type，称为 **`ImmersiveSpace`**。我们可以在应用程序中定义一个 `ImmersiveSpace`：

```
@main


struct WorldApp: App {


var body: some Scene {


ImmersiveSpace {


SolarSystem()


        }


    }


}

```

与其他 Scene 类型类似，我们需要将视图层次结构放置在 Scene 的 `body` 中。整个应用程序可以只包含 Space，也可以通过在已有的 Window 或 Volume 旁边添加一个或多个 Space 从而扩展现有的应用程序。但需要注意，在打开另一个 Space 之前，需要关闭当前 Space，即一个应用程序同时只可以打开一个 Space。

通过将 `SolarSystem()` 放置在 `ImmersiveSpace` 中，视图将在没有任何裁剪边界的情况下被渲染。仅通过以上几行，我们就将我们的 `SolarSystem` 带入了丰富的沉浸式体验中：

![](https://images.xiaozhuanlan.com/photo/2023/02e6ac0443aa8c671991fe7caa87a649.gif)

ImmersiveSpace

当多个应用程序并排运行时，它们都显示在同一个 Space 中，我们称之为**共享空间 (Shared Space)**。打开一个应用程序的 Space 会导致一些特殊行为，使这个 Scene 从其他 Scene 类型中脱颖而出。一旦应用程序显示 `ImmersiveSpace` Scene，应用程序就会进入**全空间 (Full Space)**。被选择的应用程序将成为用户唯一可见的应用程序，所有其他应用程序都将消失，来为 Space 腾出空间。一旦用户关闭该 Space，其他应用程序将重新出现。

![](https://images.xiaozhuanlan.com/photo/2023/2edf6fda2440547658d001e8b99c3ae2.png)

从 Shared Space 到 Full Space

此外，由于 `ImmersiveSpace` 是一个 Scene，因此有自己的坐标系。放置在 Space 中的所有内容都相对于 Space 的原点。而一个 Space 的原点在用户正下方，在用户第一次打开 Space 时靠近自己双脚中心的位置：

![](https://images.xiaozhuanlan.com/photo/2023/ec15d14943c8ccd3b2ed15575ae3995b.png)

Space 的原点

在 Space 中展示内容
-------------

### 使用 RealityView

`ImmersiveSpace` 作为一种 Scene 类型，也可以将视图层次结构放在其中：

```
ImmersiveSpace {


VStack {


Text("Hello, WWDC")


            .foregroundStyle(.primary)


Text("Welcome to a new dimension")


            .foregroundStyle(.secondary)


    }


}

```

`ImmersiveSpace` 可以展示任何 SwiftUI 视图，放置在 Space 中的任何内容都使用我们熟悉的 SwiftUI 布局系统。但由于 Space 的原点位于用户的正下方，我们可能不想只是将内容放在那里 ——让我们谈谈 `RealityView`。

如果我们想充分利用 SwiftUI、ARKit 和 RealityKit，Apple 鼓励我们将 `ImmersiveSpace` 与新的具有强大功能的 `RealityView` 结合使用。两者结合在一起以提供我们需要的功能，从而构建出色的沉浸式体验。例如，`RealityView` 内置了对资源异步加载的支持：

```
ImmersiveSpace {


RealityView { content in


let starfield = await loadStarfield()


        content.add(starfield)


    }


}

```

`RealityView` 使用 RealityKit 来显示其内容，因此需要注意，`RealityView` 与 SwiftUI 的坐标空间有些差异。在 SwiftUI 中，y 轴指向下方，z 轴指向屏幕。这适用于 Window、Volume 和 Space，而在 RealityKit 中，y 轴指向上方：

![](https://images.xiaozhuanlan.com/photo/2023/fc11177ef3a3356a339992f862a1a380.png)

SwiftUI 和 RealityKit 的坐标差异

除了异步加载，将 `RealityView` 放置在 `ImmersiveSpace` Scene 中还可以实现更多不一样的功能。例如我们可以将元素放置在 `RealityView` 上。或者在 Space 开启时，我们可以使用用户的手部和头部姿势数据，在 `RealityView` 中定位 Entity。关于 `RealityView` 的更多内容，可以从 WWDC 23 [Enhance your spatial computing app with RealityKit](https://developer.apple.com/videos/play/wwdc2023/10081/) Session 中了解。

### 使用 open(dismiss)ImmersiveSpace

我们为 World 应用程序新增一些元素。我们首先定义一个 `ImmersiveSpace`。与 `WindowGroup` 类似，我们可以为其分配 Identifier 或 Value Type。稍后我们将使用此 identifier 来打开 Space：

```
@main


struct WorldApp: App {


var body: some Scene {


ImmersiveSpace(id: "solar") {


SolarSystem()


        }


    }


}

```

我们还使用 `WindowGroup` 为我们的应用程序定义一个简单的 Window，在应用程序启动时显示该 Window，并带有一个控按钮以打开 Space 查看 SolarSystem：

```
struct LaunchWindow: Scene {


var body: some Scene {


WindowGroup {


VStack {


Text("The Solar System")


                    .font(.largeTitle)


Text("Every 365.25 days, the planet and its satellites [...]")


SpaceControl()


            }


        }


    }


}

```

![](https://images.xiaozhuanlan.com/photo/2023/a5191502a9ff33c4e7a5cf70870475be.png)

WindowGroup

单击该按钮时，我们将改按钮标题并打开 Space。此前，SwiftUI 为了控制 Window，提供了 `openWindow` 和 `dismissWindow` 的 Environment Action。对于 `ImmersiveSpace`，SwiftUI 添加了新的 [`openImmersiveSpace`](https://developer.apple.com/documentation/swiftui/environmentvalues/openimmersivespace) 和 [`dismissImmersiveSpace`](https://developer.apple.com/documentation/swiftui/dismissimmersivespaceaction) Action。

顾名思义，`openImmersiveSpace` 是呈现 Space 的 Action，`dismissImmersiveSpace` 是消除 Space 的 Action。我们可以在按钮响应时使用这些 Action：

```
struct SpaceControl: View {


    @Environment(\.openImmersiveSpace) private var openImmersiveSpace


    @Environment(\.dismissImmersiveSpace) private var dismissImmersiveSpace


}

```

打开 Space 的时候，我们需要传入之前定义的 Identifier。由于一次只能打开一个 Space，因此 `dismissImmersiveSpace` 操作不需要任何参数。

系统会以一定的持续时间动画来展现 Space 进出。这些 Action 是异步的，这样我们就可以对动画的完成做出响应。打开 Space 可能会失败，`openImmersiveSpace` 会通过它的结果告诉我们调用的结果，以确保有正确的错误处理：

```
struct SpaceControl: View {


    @State private var isSpaceHidden: Bool = true


var body: some View {


Button(isSpaceHidden ? "View Outer Space" : "Exit the solar system") {


Task {


if isSpaceHidden {


let result = await openImmersiveSpace(id: "solar")


switch result {


                    }


                } else {


                    await dismissImmersiveSpace()


                    isSpaceHidden = true


                }


            }


        }


    }


}

```

回到应用程序入口，我们现在可以在这里添加 `LaunchWindow`。这里需要注意两个 Scene 的顺序。`LaunchWindow` 是我们 Scene 列表中的第一个，因此 SwiftUI 将在应用程序启动时显示 `LaunchWindow`。Space 在应用程序启动时不可见，只会在用户单击按钮后显示：

```
@main


struct WorldApp: App {


var body: some Scene {


LaunchWindow()


ImmersiveSpace(id: "solar") {


SolarSystem()


        }


    }


}

```

当我们在模拟器上运行应用程序时，我们会看到 `LaunchWindow`。然后只要点击 “View Outer Space” 按钮，`SolarSystem` 就会出现在我们的眼前：

![](https://images.xiaozhuanlan.com/photo/2023/67c82daa478739e881f45e63f56fca64.gif)

LaunchWindow 和 SolarSystem

我们可以回顾下文章最初提到的概念，如果此时有多个应用程序并排运行时，在点击按钮时，我们的 Space 将在其中脱颖而出。其他应用程序将暂时消失。

### 使用 Model3D

现在我们已经定义了一个多场景应用程序，其中包含一个标准的 Window 和一个 Space。我们已经看到 World 应用程序中使用了地球模型。在构建沉浸式体验的应用程序时，我们肯定会希望在 Space 中显示一些具有大量细节的 3D 资源。需要注意的是，这些资源可能需要一些时间才能完全加载和呈现。

如果我们想在 SwiftUI 应用程序中嵌入 USD 文件或 Reality 文件的 3D 模型，为获得最佳用户体验，可以利用新 [`Model3D`](https://developer.apple.com/documentation/realitykit/model3d/) API，这是一个新的 View 视图，用于异步加载 3D 资源：

```
Model3D(named: "Earth") { phase in


switch phase {


case .empty:


Text( "Waiting" )


case .failure(let error):


Text("Error \(error.localizedDescription)")


case .success(let model):


            model.resizable()


    }


}

```

在此代码中，资源的加载被划分了不同的阶段，在模型仍在加载时或出现问题时显示提示。

此外，我们可以使用标准的 ViewModifier 来调整模型的大小。 例如，使用 [`resizable(_:)`](https://developer.apple.com/documentation/realitykit/resolvedmodel3d/resizable(_:)) 方法缩放模型，适应当前视图的大小。使用 [`aspectRatio(_:contentMode:)`](https://developer.apple.com/documentation/SwiftUI/View/aspectRatio(_:contentMode:)-771ow) 方法调整大小，保持模型的原始纵横比：

```
Model3D(named: "Robot-Drummer") { model in


     model


         .resizable()


         .aspectRatio(contentMode: .fit)


 } placeholder: {


ProgressView()


 }

```

如果我们希望从指定的 URL 加载模型：

```
Model3D(url: URL(string: "https://example.com/robot.usdz")!)


     .frame(width: 300, height: 600)

```

在模型加载之前，`Model3D` 会显示一个填充可用空间的标准占位符，即我们在上文图片中看到的、地球在加载时展示的 “空白地球”。加载成功完成后，视图将更新以显示模型。此外，我们也可以指定自定义占位符：

```
let url = URL(string: "https://example.com/robot.usdz")!


Model3D(url: url) { model in


     model.resizable()


 } placeholder: {


ProgressView()


 }


 .frame(width: 50, height: 50)

```

管理 Space
--------

将沉浸式的体验带入我们的应用程序还离不开开发者与系统的管理，包括处理 [`scenePhase`](https://developer.apple.com/documentation/swiftui/scenephase)、使用 `GeometryReader3D` 进行 Scene 间协调、使用 `ImmersionStyle` 呈现不同样式。

> 摘要：通过 SwiftUI 的 ImmersiveSpace，我们能够轻松的在 visionOS 上创造完全沉浸式的体验。在 Space 中，我们可以使用 `RealityView` 、`Model3D` 等展示内容，可以使用 `scenePhase`、`GeometryReader3D`、`ImmersionStyle`等更好的管理 Space，此外，还有一些自定义功能也值得我们探索。

本文基于 [Session 10111](https://developer.apple.com/videos/play/wwdc2023/10111/) 梳理。

> 作者：
> 
> Layer，就职于抖音即时通讯团队，APP：**喵喵消烦员** 开发者。
> 
> 审核：
> 
> 戴铭，极客时间《iOS 开发高手课》和纸书《跟戴铭学 iOS 编程》作者。
> 
> 黄骋志，老司机技术轮值主编，目前就职于字节跳动，参与西瓜视频质量与稳定性工作。对 OOM/Watchdog 较为了解并长期投入。

在 visionOS 上通过一些功能强大且易于使用的 API，我们能够轻松创造完全沉浸式的体验。所有这一切都可以通过我们已经熟悉的工具、框架和模式来实现。其核心是 SwiftUI 的 [`ImmersiveSpace`](https://developer.apple.com/documentation/swiftui/immersivespace)。本文我们将围绕 Space 展开，介绍包括 Space 的信息、如何在 Space 中展示内容和更好的管理 Space，此外，还会介绍一些自定义功能。

![](https://images.xiaozhuanlan.com/photo/2023/29a0b5b25c8d5afa697386860ec55263.png)

iOS 和 iPadOS 上丰富的 AR 体验

> 文章涉及的概念主要包括 SwiftUI 的 `App`、`Scene`、`View` 协议，visionOS 下的 Window、Volume、Space。详细的内容将在文中展开，以下是概念简述：
> 
> <table><thead><tr><th>概念</th><th>简述</th></tr></thead><tbody><tr><td><a href="https://developer.apple.com/documentation/swiftui/app" target="_blank"><code>App</code></a></td><td>表示应用程序的结构和行为的类型，通过声明符合 <code>App</code> 协议的结构来创建应用程序。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/scene" target="_blank"><code>Scene</code></a></td><td>应用程序用户界面的一部分，充当要向用户显示的 <code>View</code> 层次结构的容器。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/view" target="_blank"><code>View</code></a></td><td>应用程序用户界面的一部分，过声明符合 <code>View</code> 协议的类型来创建视图。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/window" target="_blank">Window</a></td><td>在独特的窗口中呈现其内容的 Scene。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/windowstyle/volumetric/" target="_blank">Volume</a></td><td>在有界区域内托管 3D 内容，是 Window Scene 的一种样式。</td></tr><tr><td><a href="https://developer.apple.com/documentation/swiftui/immersivespace" target="_blank">Space</a></td><td>在无限的空间中呈现其内容的 Scene。</td></tr></tbody></table>

进入沉浸式体验
-------

在过去的几年中，Apple 引入和完善了许多工具及框架，用于为 iOS 和 iPadOS 等构建 AR 应用程序，例如 [ARKit](https://developer.apple.com/augmented-reality/arkit/)、[RealityKit](https://developer.apple.com/augmented-reality/realitykit/) 等。这些应用程序通过交互式的用户界面和虚拟对象来增强用户环境，模糊现实世界与想象之间的界限，创造了丰富的 AR 体验。

![](https://images.xiaozhuanlan.com/photo/2023/92ac515e6db29f0b5e96fe2ac075d981.png)

iOS 和 iPadOS 上丰富的 AR 体验

在 SwiftUI2.0 中，Apple 提供了全新的 [`App`](https://developer.apple.com/documentation/swiftui/app)、[`Scene`](https://developer.apple.com/documentation/swiftui/scene) 协议，使代码变得更清晰：

```
@main


struct WorldApp: App {


var body: some Scene {


WindowGroup {


Text("Hello world")


        }


    }


}

```

通在符合 `App` 协议的结构的声明之前加上 [`@main`](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html#ID626) 属性，以指示该结构提供进入应用程序的入口点。`Scene` 是 `View` 层次结构的容器，通过在 `App` 实例的 `body`中组合一个或多个符合 `Scene` 协议的实例来呈现具体程序。SwiftUI2.0 提供了预置的 Scene，此外，用户也可以自己编写符合 `Scene` 协议的场景。预置的 Scene 包括 [`WindowGroup`](https://developer.apple.com/documentation/swiftui/windowgroup)、[`DocumentGroup`](https://developer.apple.com/documentation/swiftui/documentgroup)，macOS 使用的 [`Window`](https://developer.apple.com/documentation/swiftui/window)、[`Settings`](https://developer.apple.com/documentation/swiftui/settings)，watchOS 使用的 [`WKNotificationScene`](https://developer.apple.com/documentation/swiftui/wknotificationscene)。

在 WWDC23 [Take SwiftUI to the next dimension](https://developer.apple.com/videos/play/wwdc2023/10113) Session 中，Apple 详细介绍了 SwiftUI 中的第三维，可以在 visionOS 上呈现 Window 或 Volume。

*   Window：我们可以在 visionOS 应用中创建一个或多个的窗口。可以包含传统的视图或者控件，也可以通过添加 3D 内容来增加深度上的体验。如下左图的紫色、蓝色、红色 Window。Window 即常规的 `WindowGroup` 在 visionOS 上的表现。
*   Volume：Volume 为我们提供了一个固定比例的容器，在任何距离都保持相同的大小，支持从任何角度查看。Volume 是在应用程序中显示 3D 内容的好方法，同时不会占用整个空间。如下左图的绿色 Volume。创建 Volume 非常简单，只需在创建 Scene 时使用新的 [`.volumetric`](https://developer.apple.com/documentation/swiftui/windowstyle/volumetric/) 的 [`windowStyle(_:)`](https://developer.apple.com/documentation/swiftui/scene/windowstyle(_:)) 样式：

```
WindowGroup {


GlobeView()


}


.windowStyle(.volumetric)

```

<table><thead><tr><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/a9febe7b32168040c80f54bc0d13bb88.png"><p>用户 和 Window、Volume</p></th><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/e0d2c8d157ec3808aef1d0ad920dead8.png"><p>Windows 和 Volumes</p></th></tr></thead><tbody></tbody></table>

> 符合 `WindowStyle` 协议的样式，包括只服务于 macOS 的 [`hiddentitlebar`](https://developer.apple.com/documentation/swiftui/windowstyle/hiddentitlebar)、[`titleBar`](https://developer.apple.com/documentation/swiftui/windowstyle/titlebar)，服务于 macOS 和 visionOS 的 [`automatic`](https://developer.apple.com/documentation/swiftui/windowstyle/automatic)、以及只服务于 visionOS [`plain`](https://developer.apple.com/documentation/swiftui/windowstyle/plain)、[`volumetric`](https://developer.apple.com/documentation/swiftui/windowstyle/volumetric)。`automatic` 和 `plain` 即我们上文提到的 Window，但 `automatic` 作为普通 Window，与 `plain` 的差异在于是否在 visionOS 中展示玻璃背景效果。

但 Volume 并不是 “只能达到” 的体验，我们还可以更充分的利用 visionOS 提供的无限空间，创造身临其境的体验。比如说我们想把物体放在 Window 之外、用户周围—— **Space** 是另一种在 visionOS 上呈现用户界面的容器，可以为用户创建身临其境的体验。

<table><thead><tr><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/1e65595fed6b7d97e45448ac305e8227.png"><p>Windows、Volumes 以及 Spaces</p></th><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/68cc049068329e9778fa288cada6068a.png"><p>无限空间</p></th></tr></thead><tbody></tbody></table>

通过 Space，Apple 将 AR 提升到一个全新的水平 —— **沉浸式体验 (Immersive experience)**。应用程序会在用户周围显示窗口、三维内容等。开发者可以将虚拟对象或效果等锚定到物体表面，从而增强和丰富现实世界。同时，用户所处的现实环境也会作为用户体验的一部分，对用户仍然可见。

更进一步的是**完全沉浸式体验 (Fully immersive experience)**，应用程序可以完全控制用户所看到的内容，覆盖整个空间，这将解锁所有的可能！

<table><thead><tr><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/ae03216fe85a8ce46a5aa1d746f77d9b.gif"><p>沉浸式体验</p></th><th><img class="" src="https://images.xiaozhuanlan.com/photo/2023/9a28b4bc6ca118189ef548c760e1206a.gif"><p>完全沉浸式体验</p></th></tr></thead><tbody><tr><td>沉浸式体验 (Immersive experience)</td><td>完全沉浸式体验 ((Fully immersive experience)</td></tr></tbody></table>

我们将围绕沉浸式体验的核心—— SwiftUI 的 `ImmersiveSpace` 展开。

初识 Space
--------

我们以太空探索主题的 World 应用程序为例，增加一个探索太空的 Space。Space 是 SwiftUI 中的一种新的 Scene Type，称为 **`ImmersiveSpace`**。我们可以在应用程序中定义一个 `ImmersiveSpace`：

```
@main


struct WorldApp: App {


var body: some Scene {


ImmersiveSpace {


SolarSystem()


        }


    }


}

```

与其他 Scene 类型类似，我们需要将视图层次结构放置在 Scene 的 `body` 中。整个应用程序可以只包含 Space，也可以通过在已有的 Window 或 Volume 旁边添加一个或多个 Space 从而扩展现有的应用程序。但需要注意，在打开另一个 Space 之前，需要关闭当前 Space，即一个应用程序同时只可以打开一个 Space。

通过将 `SolarSystem()` 放置在 `ImmersiveSpace` 中，视图将在没有任何裁剪边界的情况下被渲染。仅通过以上几行，我们就将我们的 `SolarSystem` 带入了丰富的沉浸式体验中：

![](https://images.xiaozhuanlan.com/photo/2023/02e6ac0443aa8c671991fe7caa87a649.gif)

ImmersiveSpace

当多个应用程序并排运行时，它们都显示在同一个 Space 中，我们称之为**共享空间 (Shared Space)**。打开一个应用程序的 Space 会导致一些特殊行为，使这个 Scene 从其他 Scene 类型中脱颖而出。一旦应用程序显示 `ImmersiveSpace` Scene，应用程序就会进入**全空间 (Full Space)**。被选择的应用程序将成为用户唯一可见的应用程序，所有其他应用程序都将消失，来为 Space 腾出空间。一旦用户关闭该 Space，其他应用程序将重新出现。

![](https://images.xiaozhuanlan.com/photo/2023/2edf6fda2440547658d001e8b99c3ae2.png)

从 Shared Space 到 Full Space

此外，由于 `ImmersiveSpace` 是一个 Scene，因此有自己的坐标系。放置在 Space 中的所有内容都相对于 Space 的原点。而一个 Space 的原点在用户正下方，在用户第一次打开 Space 时靠近自己双脚中心的位置：

![](https://images.xiaozhuanlan.com/photo/2023/ec15d14943c8ccd3b2ed15575ae3995b.png)

Space 的原点

在 Space 中展示内容
-------------

### 使用 RealityView

`ImmersiveSpace` 作为一种 Scene 类型，也可以将视图层次结构放在其中：

```
ImmersiveSpace {


VStack {


Text("Hello, WWDC")


            .foregroundStyle(.primary)


Text("Welcome to a new dimension")


            .foregroundStyle(.secondary)


    }


}

```

`ImmersiveSpace` 可以展示任何 SwiftUI 视图，放置在 Space 中的任何内容都使用我们熟悉的 SwiftUI 布局系统。但由于 Space 的原点位于用户的正下方，我们可能不想只是将内容放在那里 ——让我们谈谈 `RealityView`。

如果我们想充分利用 SwiftUI、ARKit 和 RealityKit，Apple 鼓励我们将 `ImmersiveSpace` 与新的具有强大功能的 `RealityView` 结合使用。两者结合在一起以提供我们需要的功能，从而构建出色的沉浸式体验。例如，`RealityView` 内置了对资源异步加载的支持：

```
ImmersiveSpace {


RealityView { content in


let starfield = await loadStarfield()


        content.add(starfield)


    }


}

```

`RealityView` 使用 RealityKit 来显示其内容，因此需要注意，`RealityView` 与 SwiftUI 的坐标空间有些差异。在 SwiftUI 中，y 轴指向下方，z 轴指向屏幕。这适用于 Window、Volume 和 Space，而在 RealityKit 中，y 轴指向上方：

![](https://images.xiaozhuanlan.com/photo/2023/fc11177ef3a3356a339992f862a1a380.png)

SwiftUI 和 RealityKit 的坐标差异

除了异步加载，将 `RealityView` 放置在 `ImmersiveSpace` Scene 中还可以实现更多不一样的功能。例如我们可以将元素放置在 `RealityView` 上。或者在 Space 开启时，我们可以使用用户的手部和头部姿势数据，在 `RealityView` 中定位 Entity。关于 `RealityView` 的更多内容，可以从 WWDC 23 [Enhance your spatial computing app with RealityKit](https://developer.apple.com/videos/play/wwdc2023/10081/) Session 中了解。

### 使用 open(dismiss)ImmersiveSpace

我们为 World 应用程序新增一些元素。我们首先定义一个 `ImmersiveSpace`。与 `WindowGroup` 类似，我们可以为其分配 Identifier 或 Value Type。稍后我们将使用此 identifier 来打开 Space：

```
@main


struct WorldApp: App {


var body: some Scene {


ImmersiveSpace(id: "solar") {


SolarSystem()


        }


    }


}

```

我们还使用 `WindowGroup` 为我们的应用程序定义一个简单的 Window，在应用程序启动时显示该 Window，并带有一个控按钮以打开 Space 查看 SolarSystem：

```
struct LaunchWindow: Scene {


var body: some Scene {


WindowGroup {


VStack {


Text("The Solar System")


                    .font(.largeTitle)


Text("Every 365.25 days, the planet and its satellites [...]")


SpaceControl()


            }


        }


    }


}

```

![](https://images.xiaozhuanlan.com/photo/2023/a5191502a9ff33c4e7a5cf70870475be.png)

WindowGroup

单击该按钮时，我们将改按钮标题并打开 Space。此前，SwiftUI 为了控制 Window，提供了 `openWindow` 和 `dismissWindow` 的 Environment Action。对于 `ImmersiveSpace`，SwiftUI 添加了新的 [`openImmersiveSpace`](https://developer.apple.com/documentation/swiftui/environmentvalues/openimmersivespace) 和 [`dismissImmersiveSpace`](https://developer.apple.com/documentation/swiftui/dismissimmersivespaceaction) Action。

顾名思义，`openImmersiveSpace` 是呈现 Space 的 Action，`dismissImmersiveSpace` 是消除 Space 的 Action。我们可以在按钮响应时使用这些 Action：

```
struct SpaceControl: View {


    @Environment(\.openImmersiveSpace) private var openImmersiveSpace


    @Environment(\.dismissImmersiveSpace) private var dismissImmersiveSpace


}

```

打开 Space 的时候，我们需要传入之前定义的 Identifier。由于一次只能打开一个 Space，因此 `dismissImmersiveSpace` 操作不需要任何参数。

系统会以一定的持续时间动画来展现 Space 进出。这些 Action 是异步的，这样我们就可以对动画的完成做出响应。打开 Space 可能会失败，`openImmersiveSpace` 会通过它的结果告诉我们调用的结果，以确保有正确的错误处理：

```
struct SpaceControl: View {


    @State private var isSpaceHidden: Bool = true


var body: some View {


Button(isSpaceHidden ? "View Outer Space" : "Exit the solar system") {


Task {


if isSpaceHidden {


let result = await openImmersiveSpace(id: "solar")


switch result {


                    }


                } else {


                    await dismissImmersiveSpace()


                    isSpaceHidden = true


                }


            }


        }


    }


}

```

回到应用程序入口，我们现在可以在这里添加 `LaunchWindow`。这里需要注意两个 Scene 的顺序。`LaunchWindow` 是我们 Scene 列表中的第一个，因此 SwiftUI 将在应用程序启动时显示 `LaunchWindow`。Space 在应用程序启动时不可见，只会在用户单击按钮后显示：

```
@main


struct WorldApp: App {


var body: some Scene {


LaunchWindow()


ImmersiveSpace(id: "solar") {


SolarSystem()


        }


    }


}

```

当我们在模拟器上运行应用程序时，我们会看到 `LaunchWindow`。然后只要点击 “View Outer Space” 按钮，`SolarSystem` 就会出现在我们的眼前：

![](https://images.xiaozhuanlan.com/photo/2023/67c82daa478739e881f45e63f56fca64.gif)

LaunchWindow 和 SolarSystem

我们可以回顾下文章最初提到的概念，如果此时有多个应用程序并排运行时，在点击按钮时，我们的 Space 将在其中脱颖而出。其他应用程序将暂时消失。

### 使用 Model3D

现在我们已经定义了一个多场景应用程序，其中包含一个标准的 Window 和一个 Space。我们已经看到 World 应用程序中使用了地球模型。在构建沉浸式体验的应用程序时，我们肯定会希望在 Space 中显示一些具有大量细节的 3D 资源。需要注意的是，这些资源可能需要一些时间才能完全加载和呈现。

如果我们想在 SwiftUI 应用程序中嵌入 USD 文件或 Reality 文件的 3D 模型，为获得最佳用户体验，可以利用新 [`Model3D`](https://developer.apple.com/documentation/realitykit/model3d/) API，这是一个新的 View 视图，用于异步加载 3D 资源：

```
Model3D(named: "Earth") { phase in


switch phase {


case .empty:


Text( "Waiting" )


case .failure(let error):


Text("Error \(error.localizedDescription)")


case .success(let model):


            model.resizable()


    }


}

```

在此代码中，资源的加载被划分了不同的阶段，在模型仍在加载时或出现问题时显示提示。

此外，我们可以使用标准的 ViewModifier 来调整模型的大小。 例如，使用 [`resizable(_:)`](https://developer.apple.com/documentation/realitykit/resolvedmodel3d/resizable(_:)) 方法缩放模型，适应当前视图的大小。使用 [`aspectRatio(_:contentMode:)`](https://developer.apple.com/documentation/SwiftUI/View/aspectRatio(_:contentMode:)-771ow) 方法调整大小，保持模型的原始纵横比：

```
Model3D(named: "Robot-Drummer") { model in


     model


         .resizable()


         .aspectRatio(contentMode: .fit)


 } placeholder: {


ProgressView()


 }

```

如果我们希望从指定的 URL 加载模型：

```
Model3D(url: URL(string: "https://example.com/robot.usdz")!)


     .frame(width: 300, height: 600)

```

在模型加载之前，`Model3D` 会显示一个填充可用空间的标准占位符，即我们在上文图片中看到的、地球在加载时展示的 “空白地球”。加载成功完成后，视图将更新以显示模型。此外，我们也可以指定自定义占位符：

```
let url = URL(string: "https://example.com/robot.usdz")!


Model3D(url: url) { model in


     model.resizable()


 } placeholder: {


ProgressView()


 }


 .frame(width: 50, height: 50)

```

管理 Space
--------

将沉浸式的体验带入我们的应用程序还离不开开发者与系统的管理，包括处理 [`scenePhase`](https://developer.apple.com/documentation/swiftui/scenephase)、使用 `GeometryReader3D` 进行 Scene 间协调、使用 `ImmersionStyle` 呈现不同样式。