> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [tangchuan.notion.site](https://tangchuan.notion.site/Figma-or-e87159bd281c4eecaffed2f768b7bc76)

> A new tool that blends your everyday work apps into one. It's the all-in-one workspace for you and yo......

这篇文章将会从以下几个方面介绍这次 Figma 的更新，一是功能介绍，及其在工作中的运用；二是吐槽一下本次更新，变粗糙的设计，更复杂的使用；三是发散脑洞讨论一下，设计到底是什么，现在的 Figma 越来越像是在写代码，而声明式的前端框架也越来越像是在做设计，所以未来的软件开发的终极形态是什么？最后随便聊聊 Figma 本次升级中的商业考量因素。

在半个月前，Figma 举办了 Config 2023 大会，除了各个领域的 UI/UX 设计师的分享，还发布了大量新功能——全局变量、Dev 模式、Autolayout Warp 等等。这些强大的功能都为设计和开发带来了更多的可能性。在浅浅的试用了这段时间后，下面我们来一一过一边这些功能，和这些功能在我们现在工作流的中的运用，以及如何运用它们优化工作流。

过去为了维护产品中设计的统一，我们设计开发用了一段时间来构建我们的 design tokens，并完成了开发侧的协同，这是我们重新构建整个设计系统的第一步。

原先我们团队中 design token 的制作是通过维护 json 代码来实现，这种方式可以在代码层面很好的应用 design tokens，但是这些 tokens 并不体现在设计稿上，设计稿上仅仅能查看的是原来 style 有的样式——color , typography, shadow。对于 spacing, sizing 这些属性，仅仅是设计师和开发记住各自要用的 token 名称，所以使用起来并没有很方便。

而现在，设计师可以通过 Figma Variables 来实现原生的设计稿 design token。

简单试玩一下官方提供的演示文件，可以看到当你的团队有了完善的设计系统，你可以很容易实现 Light Theme 和 Dark Theme 的切换，甚至是不同风格的设计稿。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F265aa352-0339-407f-8527-2a8d2499f60d%2FUntitled.png?id=c89c0e7d-51e0-4a4b-8fdc-bcd4603eb17c&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=2000&userId=&cache=v2)

还可以结合新的 Advanced Prototyping 来做过去做起来很麻烦或者只能模拟演示的交互效果。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4ac016b1-431b-4bb4-a001-44238ae74fde%2FUntitled.png?id=5e5746fb-70e7-46bd-bc7d-5ef56b2738a5&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=2000&userId=&cache=v2)

当然 Style 并不是完全没用了，当使用渐变色或者投影时，Style 依然发挥作用。

Figma 设计了一个全新的功能——Dev Mode，取代原有的 Inspect 检查器。

Dev Mode 包含了原有的 Inspect 功能，开发可以在设计稿上检查任意的设计元素。

现在，设计师可以通过这个功能标记已经确认设计稿的 section，以此来方便开发辨认哪些设计稿是可开发的。

这里可以看到 Figma 产品对于 section 功能的的布局，让它作为最小单位来运用于 Dev Mode，强化了 section 与 frame 这两个很相似的功能的区别。侧面也印证了官方是希望用户将 frame 的使用最大范围放置在页面这个单位上，提倡用户使用 section 来整理设计稿。

在这个模块里，可以看到已经 ready 的页面会有最后编辑时间，也是方便开发找到最新修改的设计稿。

最后是 layers 的展示，精简到了对应的 frame，同样也是方便开发找到对应功能的布局和元素。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1dac8eab-dbc3-41f4-af14-66ae96cf5f33%2FUntitled.png?id=3692a7ac-cdeb-46c1-a4c1-a30f1185689e&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=2000&userId=&cache=v2)

Figma 本次更新把 Inspect 检查器功能单独拆出来，整合到 Dev Mode 里。新的 Inspect 功能有了更强大的代码参考。

首先它包含了选中对应 frame 的布局和最后修改时间，甚至你可以去比较设计稿的变化，来确认本次设计调整改了哪些地方，以免忽略掉一些不容易察觉的视觉细节。

如果你选中的是一个设计的 component，同步这里会展示这个 component 的描述介绍，各个参数和变体，还可以通过【Open in playground】来查看这个组件的各个交互样式。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F75ae5cb9-d06b-478b-be80-6661815c74c9%2FUntitled.png?id=d3c8b1a6-0f39-44a5-be01-340321632496&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=2000&userId=&cache=v2)

第二点是加入了 Dev resoueces 模块，开发和设计同学都可以把这个设计模块或组建的 GitHub、Storybook 等链接添加到这里，同样也是用来联接 design 和 coding。

在代码片段的功能上，继承了原有的功能，并加强了布局的展示。这里结合了新的变量功能，使得被做成变量的 design tokens 可以直接显示在代码片段。，不得不说，这又是一次对 design token 运用的强化。

最后，新加入的 Assets 功能可以更方便开发查看组件和下载使用 svg 和图片素材。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F31927f63-f34d-43f7-9568-8474e4226efc%2FUntitled.png?id=68abee99-2336-4f84-b6a5-87a1c88c6049&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=2000&userId=&cache=v2)

另外再提一点，新的 Inspect 功能是允许在 VS Code 中打开 Figma 文件的，这意味着开发可以全程不离开 VS Code 就可以查看设计稿，更丝滑的开发体验。

最后的最后，提一下 Plugins，主要是针对设计稿代码化的操作。直接使用代码生成工具，是可以直接产生对应的代码的，至于可用性可以交由开发同学试试。

比如下面演示的这个 plugin，它可以直接帮我生成这个卡片的 JSX，用 tailwind 写好类。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2eeef92d-8d3f-42ac-8fb2-21a15485ecbd%2FUntitled.png?id=d21a1a2e-cc13-4af3-8fbe-9d7ce6e1a117&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=2000&userId=&cache=v2)

比较性感的功能是 Config 大会上演示的 AI 代码片段生成功能：

Noah L 分享了在 VS Code 中使用 Figma 的设计稿来生成代码的片段，这个 Dev 模式下的功能比起以前根据整篇设计稿来产出代码要来的更可行和落地。

从演示上看出，如果想让 AI run 起来，设计师真的需要了解现代 App 和 Web 是如何变成代码。你不能指望 AI 在一堆 rectangle 里给你画出包含合理的自适应和布局的界面。

via Twitter @tangchuan_CN

Auto Layout 功能是 Figma 在初期和 Sketch 形成比较大差异化的一个功能亮点。它用设计师更容易理解的方式将 CSS 布局的几个能力呈现出来，使得设计师可以通过类似 <div></div > 的方式来组织设计元素，是一个很好的 CSS 可视化最佳实践。

新加入的 Auto Layout 功能进一步扩大了能力，现在设计师可以通过设置 Max/Min Hight/Width 来给自适应布局设定最大最小尺寸。

而在 Direction 上加入了 Warp 模式，终于可以直接设计自适应布局下的自适应换行了，不用再担心像以前一样每行分开摆，同样这个属性可以在 Dev Mode 让开发查看到。

官方 twitter 关于 Adcanced Prototyping 功能的介绍

如果说 Auto Layout 是 CSS 的最佳实践，那更新的原型功能就是一个越来越强大的 no coding 工具，是 “JS 可视化配置浓缩版”。

除了前面提到的变量在原型中的运用，这次原型功能更是提供了表达式和条件判断这些交互操作——是的没错，你可以在 Figma Prototype 里设置”if else“了。

这一部分官方也提供了一个 playground 文件，可以看看何如在原型中做一些计算和变量变化的交互。

但是，事实上这一块的功能在试玩后并没有很让人很兴奋。反而让我在想，到底开发和设计的配合应该是怎样？不少设计师表示，还是喜欢无拘无束的画图，搭积木一样搬来搬去真的很无聊。

从实际应用场景来看，我如果做了这么复杂的原型，但是它实际上还是原型。记得很久以前刚接触 UI 设计的时候，曾经用 Axure 做了一个很完整的格瓦拉 app 交互 demo（可能很多朋友都不知道这个死掉的电影 app），我费了很大力气做的 demo，但它也只是原型，并不是代码。

所以作为设计师，如果我想自己实现一些效果，我为什么不去写代码？

新的字体选择工具终于更新了字体预览和分类功能了，难以置信这个如此刚需的功能在今天才上线。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F00022764-0149-4d8d-a373-e6e4c21bc2a2%2FUntitled.png?id=d601f62b-4581-4ae1-aaa9-d88df12a2c60&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=860&userId=&cache=v2)

过去我的解决方法是用 Moonvy 开发的 [Figma EX](https://moonvy.com/figmaEX/) 附带的字体选择器，这个插件真的很好用，插件侧边栏、字体选择器、Figma 汉化，三个功能都非常实用，过去基本每一个设计的小伙伴我都会推荐一边，再次强烈推荐。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F271bcaf6-a545-4732-adec-cb201d3751de%2FUntitled.png?id=dfa72242-b489-4b20-96c2-3444111931ef&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=2000&userId=&cache=v2)

产品设计里的悖论就是越强大的功能，带来的就是越高的上手成本。功能变复杂了变多了。这是一种很矛盾的心态，一方面希望 Figma 更强，但是又怕它变成 Adobe 系列或 Autodesk 系列的庞然大物。

过去几年，Figma 整体的 UI 和交互给人的感受就是浑然一体，简单耐看的 UI 和流畅的操作链路，非常的舒服。而这次上的功能却感觉失去了那种 “Figma” 感，很难让人不怀疑没有被 Adobe 加持过。

例如 Color scoping 功能，可以为这个变量选择它的使用属性范围。刚看到这个功能时我的第一感受就是：太实用了，这样就可以避免设计师用错 token。

不规范一点的话在 text 上用了 border 颜色也是没有问题的，因为它们的色阶都是灰色那一溜；

但是当你统一换颜色时，比如把某个模块的文字色调高或者调低一级，却发现里面有几个不跟着变化，点进去一看才发现用了其他的 token，虽然它们看起来色值是一样的。

不过事实上当我设置完 scoping 时，却发现并没有生效，我在描边和填充里依然可以看到 text color 的变量。

然后我 hover 到【Coming soon】按钮才看到这句话——

“Your choices will be applied once this feature launches”

太绝了这种产品功能预告的透出方式，如此精妙的设计，真是让人拍案叫绝。（此处需要一个阴阳表情包. gif）

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbea4074f-d1b1-415e-8a13-b48f3b38020a%2FUntitled.png?id=1fea5843-8909-43f0-a1f6-1b5d424ffda4&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=670&userId=&cache=v2)

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0d0b7d93-8165-4733-8ed7-67022f48fae2%2FUntitled.png?id=6230ac43-1708-4c2f-9090-a4d782d6b190&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=770&userId=&cache=v2)

我在此文件内修改它的排序，目前只能通过命名的方式，例如我这样手动给它加序号。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F22cf5dbc-7975-4a69-baf6-9440ca61342a%2FUntitled.png?id=46eab6f1-e7eb-44c0-914a-49943a3cc980&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=770&userId=&cache=v2)

但是在其他文件中若引用这个 Library，后面不管你怎么调整排序，它都只会按照你第一次引用时的顺序排序，不会帮你重新排序。

其实这个事儿还挺疼的，因为变量库里如果有多层引用的颜色变量，我一定是我最常用的 semantic 变量在最前面。（浅浅试用就被难受到了）

在变量使用时比较明显的一个 UI 就是在每个参数后面多了一个变量的 icon。

但是实际上发现这个功能的设置方式和触发位置五花八门及其混乱。hover、右键、常驻显示、设置后显示，什么方式都有。这么缺乏统一性的设计是我第一次在 Figma 上看到。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fea69c21e-c82f-4607-b191-976c97e49540%2FUntitled.png?id=64709fe3-0dba-4d3b-a0d6-1ea092bfc009&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=670&userId=&cache=v2)

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F352b2156-8c8d-46c1-a729-9885889acc5d%2FUntitled.png?id=0f4bcf07-06a8-4107-9d39-535c8088e1c1&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=670&userId=&cache=v2)

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff08ac820-78b1-4063-a09f-d988a2b55edc%2FUntitled.png?id=e292ad55-bdd4-4b08-985f-517c7cd8dd99&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=670&userId=&cache=v2)

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbc045d2e-3027-4c65-9e94-b921cda6a1ce%2FUntitled.png?id=aba56910-c382-4b16-9727-07e8d2039c38&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=670&userId=&cache=v2)

还有在字体这里的变量，居然是设置文本内容的，但是它又可以填入任意字符串。这个意思就是，你可以把 spacing 这个参数作为文本填入进去，着实有些让人看不懂。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Feb5dbae9-705d-497a-bef8-994145039f86%2FUntitled.png?id=825c6525-d0c7-425e-97d1-48fe3c1289ee&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=670&userId=&cache=v2)

设置过变量的参数，都会有个类似 tag 的样式，被一个圆角矩形包起来，但是为什么有的地方显示参数，有的地方显示变量名？这个也看不懂。

这个时候点击颜色是打开 Libraries 选择变量，点击参数却是输入框，甚至 hover 到参数上变成了鼠标变成左右滑动调参的指针。

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F27fa0164-5d34-4751-8cf3-89a7e86a0b39%2FUntitled.png?id=add7371f-6050-47bf-bd70-9839f9f3ea80&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=670&userId=&cache=v2)

![](https://tangchuan.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fa11cde40-b371-441a-9c9e-04f53d948394%2FUntitled.png?id=c62505d8-61f3-4bfb-8d2b-25afe9d1ddba&table=block&spaceId=e9b01f43-afb2-4df7-a8d2-cc5d9e9ceadc&width=2000&userId=&cache=v2)

官方态度是会有一年的 beta 期，一年后我们再回来看看这些问题改进了多少。（别告诉我只是 Dev Mode 付费策略一年后开始了）

本次更新的 beta 功能未来都只有付费版才可使用，进一步拉大了 Starter 版和 Professional 版的功能区别，以后开发同学来查看设计稿也是需要一个付费账户的。这也是作为一个 SaaS 工具比较合理的商业路径，更好的功能和服务一定是需要更多的付费增长点。

可以看到的是越来越多的商业团队和个人开始使用 Figma 完成 UI/UX 设计工作，例如上个月 Apple 也破天荒的发布了他们的 Figma 设计文件。

但是另一方面看的出来，本次更新的这些【重磅】功能很多都还是 beta 状态，小毛病还挺多的。不知道被 Adobe 收购后，是否有影响到 Figma 的做事风格，至少现在看起来或多或少是有点。