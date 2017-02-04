---
layout: post
title:  "关于 SpriteKit"
date:   2016-07-17
categories: SpriteKit
---

> 原文链接：[Introduction](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013043-CH1-SW1)

SpriteKit 提供图形渲染和动画的基础设施，你可以用来驱动任意纹理图像或 sprites。SpriteKit 使用在每一帧渲染前进行处理的传统渲染循环。你的游戏决定场景的内容以及这些内容在每一帧中的变化。SpriteKit 可以高效的使用图形硬件去渲染动画。SpriteKit 优化使 sprites 的位置可以在每一帧动画任意改变。

SpriteKit 还提供了其他易于制作游戏的功能，包括基础播放支持和物理仿真。此外，Xcode 提供 SpriteKit 的内置支持，这样你可以直接在 Xcode 中创建复杂的特殊效果和纹理地图集。这种框架组和工具包使得SpriteKit 成为需要相同类型动画的游戏或 Apps 一个很好的选择。对于其他种类的用户界面动画，用 Core Animation 替代。

> 重要：这是一个初步的文件，包括在开发中的 OS X 技术的描述。此信息是有变化的，根据本文档实现的软件应使用最终的操作系统软件和最终文档进行测试。

![update_loop](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/Art/update_loop_2x.png)

### 概述

SpriteKit 可在 iOS 和 OS X 上使用。它使用图形硬件可在主机设备上以高帧速率合成二维图像。SpriteKit 支持许多不同种类的内容，包括：

1. 无纹理或有纹理的矩形（sprites）
2. 文本
3. 任意 CGPath-based 形状
4. 视频

SpriteKit 也提供裁剪和其他特殊效果的支持；你可以对你的内容全部或一部分使用这些效果。你可以在每个帧中使这些元素动画化或改变这些元素。你也可以把物理身体连接到这些元素，使他们正确的支持力和碰撞。

因为 SpriteKit 支持丰富的渲染架构并将所有的底层工作提交绘图命令到 OpenGL 处理，你可以把你的精力集中在解决更高层次的设计问题和创造优秀的游戏上。

#### Sprite Content 被绘制在 Sprite View 呈现的 Scenes 中

动画和渲染是由一个 [SKView](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKView/index.html#//apple_ref/occ/cl/SKView) 对象执行的。将这个 view 放置在一个 window 中，然后向它提供内容。因为它是一个 view，它的内容可以与 view 层次结构中的其他 view 相结合。

你游戏中的内容被组织成一个以 [SKScene](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKScene_Ref/index.html#//apple_ref/occ/cl/SKScene) 对象为代表的 scenes。一个场景呈现 sprites 和其他内容。它还实现了每帧逻辑和内容处理。在任何给定的时间，the view 呈现一个 scene。只要一个 scene 正在被展示，它的动画和每帧逻辑就会被自动执行。

用SpriteKit制作一个游戏，你需要用 [SKScene](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKScene_Ref/index.html#//apple_ref/occ/cl/SKScene) 类的子类或创建一个 scene delegate 进行 major game-related tasks。例如，您可以创建单独的 scene 类来显示主菜单、游戏画面和游戏结束后显示的内容。你可以很容易地在你的窗口中使用一个单一的 [SKView](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKView/index.html#//apple_ref/occ/cl/SKView) 对象，在不同的 scene 之间切换。当你切换 scene 时，你可以使用 [SKTransition](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKTransition_Ref/index.html#//apple_ref/occ/cl/SKTransition) 类来在两个场景之间进行动画。

> 相关章节：[Jumping into SpriteKit](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/GettingStarted/GettingStarted.html#//apple_ref/doc/uid/TP40013043-CH2-SW1), [SpriteKit Best Practices](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/DesigningGameswithSpriteKit/DesigningGameswithSpriteKit.html#//apple_ref/doc/uid/TP40013043-CH7-SW1)

#### 用 Node Tree 定义一个 Scene 中会出现的事物

[SKScene](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKScene_Ref/index.html#//apple_ref/occ/cl/SKScene) 类是 [SKNode](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKNode_Ref/index.html#//apple_ref/occ/cl/SKNode) 类的后代。用 SpriteKit 时，nodes 是所有内容的基本构建块，其中 scene 对象作为 node 对象的树的根 node。scene 和它的后代决定了哪些内容被绘制，及其如何渲染。

每个 node 的位置都在它的父 node 定义的坐标系中指定。一个 node 也能把其他属性应用到内容和其后代的内容上。例如，当一个 node 被旋转时，它的所有的子代也都旋转。你可以使用一个 node 树，然后旋转，比例缩放建立一个复杂的图像，通过调整顶层 node 的属性融合整个图像。

SKNode 类没有画任何东西，但它将它的属性应用于它的后代。在 SpriteKit 中每一种可画的内容是由一个不同的子类代表。一些其他的 node 子类不绘制自己的内容，而是修改他们的后代的行为。例如，你可以使用一个 [SKEffectNode](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKEffectNode_Ref/index.html#//apple_ref/occ/cl/SKEffectNode) 对象应用一个 Core Image 过滤器在一个 scene 中的整个子树中。通过精确地控制 node 树的结构，你决定了 node 呈现的顺序。

所有的 node 对象都是从 [UIResponder](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIResponder_Class/index.html#//apple_ref/occ/cl/UIResponder) 或 NSResponder 传承的可响应的对象，所以你可以使用任意 node 类的子类，并创建新的接受用户输入的类。view 类自动地扩展响应链包括了 scene 的 node 树。

> 相关章节：[Working with Sprites](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/Sprites/Sprites.html#//apple_ref/doc/uid/TP40013043-CH9-SW8), [Building Your Scene](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/Nodes/Nodes.html#//apple_ref/doc/uid/TP40013043-CH3-SW1)

#### 纹理保持可重复使用的图形数据

纹理是用来渲染 sprites 的共享图像。当你需要将相同的图像应用到多个 sprites 时常常使用的纹理。通常你通过加载存储在你的应用程序包中的图像文件创建纹理。然而，SpriteKit 也可以在你运行时从其他来源创建纹理，纹理的来源包括 Core Graphics 图像甚至渲染过的 node 树。

SpriteKit 通过给处理较低级别的需要加载纹理的代码提供图形硬件来简化纹理管理。纹理管理由 SpriteKit 自动进行。然而，如果你的游戏使用了大量的图像，你可以通过控制进程的部分来提高它的性能。首先，你需要明确地告诉 SpriteKit 载入一种纹理。

一个纹理贴图集是一组在你的游戏中一起使用且互相关联的纹理。例如，你可能会使用一个纹理贴图集来存储所有使一个角色进行动画动作的纹理或一个游戏级的背景下需要渲染的所有瓷砖。SpriteKit 使用纹理贴图集提高渲染性能。

> 相关章节：[Working with Sprites](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/Sprites/Sprites.html#//apple_ref/doc/uid/TP40013043-CH9-SW8)

#### Nodes 执行对动画内容的操作

一个 scene’s 的内容是用 actions 进行动画。每一个 action 都是由 [SKAction](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKAction_Ref/index.html#//apple_ref/occ/cl/SKAction) 类定义的一个对象。你告诉 nodes 去执行 actions。然后，当场景处理动画帧时，actions 被执行。有些 actions 是在一个动画帧内完成的，还有一些则是在变化了多个动画帧后完成的。actions 最常见的用途是动画改变 nodes 的属性。例如，您可以创建 actions，移动一个 node，缩放或旋转它，或者是让它透明。然而，actions 也可以改变 node 树，播放声音，甚至执行自定义代码。

Actions 是非常有用的，不过你也可以结合 actions 创造更复杂的效果。你可以创建同时运行的 actions 组或 actions 连续运行的序列。你可以使 actions 自动重复。

Scenes 也可以执行自定义的单帧处理。你可以通过重写 scene 子类的方法来执行额外的游戏任务。例如，如果一个 node 每帧都需要移动，你可以直接调整它的属性，而不是使用一个 action 来这样做。

> 相关章节：[Adding Actions to Nodes](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/AddingActionstoSprites/AddingActionstoSprites.html#//apple_ref/doc/uid/TP40013043-CH11-SW1), [Advanced Scene Processing](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/Actions/Actions.html#//apple_ref/doc/uid/TP40013043-CH4-SW1)

#### 添加 Physics Bodies 和 Joints 来模拟你的 Scene 中的物理

虽然你可以控制 scene 中的每一个 node 的确切位置，往往你希望这些 nodes 相互作用，相互碰撞并通知你在这个过程中的速度变化。你可能还想做很多事，但并不想通过行动系统来处理他们，如模拟重力和其他力量。要做到这一点，你只需创造 physics bodies（[SKPhysicsBody](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKPhysicsBody_Ref/index.html#//apple_ref/occ/cl/SKPhysicsBody)）并将他们附加到 scene 中的 nodes 上。每个 physics body 都是由形状、大小、质量和其他物理特性所定义的。scene 为物理模拟在一个有关联的 [SKPhysicsWorld](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKPhysicsWorld_Ref/index.html#//apple_ref/occ/cl/SKPhysicsWorld) 对象中定义了全局特征。你使用 physics world 定义了整个模拟的重力，并定义了模拟的速度。

当 physics bodies 被包含在 scene 中时，scene 会模拟这些 bodies 的物理。一些力，如摩擦离和重力，都会自动施加。其他力通过添加 [SKFieldNode](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKFieldNode_Ref/index.html#//apple_ref/occ/cl/SKFieldNode) 对象到 scene 中可以自动应用到多个 physics bodies 上。你也可以通过直接修改特定的 field body 的速度或是对它施加力或冲量来直接影响它。bodies 相互碰撞前每一个 body 的加速度和速度都会被计算出来。然后，在仿真完成后，相应的 nodes 的位置和角度会被更新。

你能精确的控制哪些物理效应有相互作用。例如，你可以说，一个特定的物理 field body 只影响 scene 中的 physics bodies 的一个子集。你也可以决定有哪些 physics bodies 可以相互碰撞，并单独决定有哪些相互作用引发您的应用程序被调用。你用这些回调函数添加游戏逻辑。例如，你的游戏可能会破坏掉一个 node 当这个 node 的 physics body 被另一个 physics body 撞击时。

你也可以使用 physics world 在 scene 中找到 physics bodies，并使用一个 joint ([SKPhysicsJoint](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKPhysicsJoint_Ref/index.html#//apple_ref/occ/cl/SKPhysicsJoint)) 将 physical bodies 连接在一起。Connected bodies 基于 joint 同时被模拟。

> 相关章节：[Simulating Physics](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/Physics/Physics.html#//apple_ref/doc/uid/TP40013043-CH6-SW1)

#### 如何使用本文档

阅读 [Jumping into SpriteKit](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/GettingStarted/GettingStarted.html#//apple_ref/doc/uid/TP40013043-CH2-SW1) 大致了解如何实现 SpriteKit 游戏。然后通过其他章节了解 SpriteKit 的细节特征。有些章节推荐的练习能帮助你理解 SpriteKit。最好的学习 SpriteKit的方法：把一些 sprites 放在 scene 上然后进行实验！

最后一章，[SpriteKit Best Practices](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/DesigningGameswithSpriteKit/DesigningGameswithSpriteKit.html#//apple_ref/doc/uid/TP40013043-CH7-SW1)，了解更多用 SpriteKit 设计游戏的细节。

#### 先决条件

在试图用 SpriteKit 创建一个游戏前，你应该已经熟悉的 app 开发的基础。特别是，你应该熟悉以下概念：

1. 使用 Xcode 开发 apps
2. Objective-C，包括了解 blocks
3. 视图和窗口系统

了解更多信息：

1. 在 iOS 上看 *Start Developing iOS Apps Today*。
2. 在 OS X 上看 *see Start Developing Mac Apps Today*。

> 注释：虽然本指南描述许多对用 SpriteKit 创造游戏很有用的技术，但它不是一个完整的游戏设计或游戏开发指南。

#### 参见

当你需要在SpriteKit框架中函数和类的具体细节看 [SpriteKit Framework Reference](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041)。一些 SpriteKit 的功能不在这个编程指南中描述，但在参考资料中有描述。

关于如何使用 Xcode 内置支持 SpriteKit 请看 [Texture Atlas Help](https://developer.apple.com/library/ios/recipes/xcode_help-texture_atlas/_index.html#//apple_ref/doc/uid/TP40013290) 和 [Particle Emitter Editor Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/xcode_guide-particle_emitter/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013297)。

参见代码：深入观察 Adventure 来说明一个基于 SpriteKit 的游戏。

以下的例子只有在 OS X 的库可用，但仍为 iOS apps 中有用的 SpriteKit 实例：

1. 请看 *Sprite Tour*，以了解 [SKSpriteNode](https://developer.apple.com/library/ios/documentation/SpriteKit/Reference/SKSpriteNode_Ref/index.html#//apple_ref/occ/cl/SKSpriteNode) 类的细节。
2. 请看 *SpriteKit Physics Collisions* 以理解 SpriteKit 中的物理系统。

