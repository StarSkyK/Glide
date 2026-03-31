---
name: "glide-engine"
description: "Glide 2D game engine assistant. Invoke when user asks about Glide engine integration, components, or development."
---

# Glide Engine 2D 游戏引擎助手

基于 SpriteKit 和 GameplayKit 的 2D 横版游戏引擎，支持 iOS/macOS/tvOS。

## 集成方式

Swift Package Manager：
```swift
dependencies: [
    .package(url: "https://github.com/cocoatoucher/Glide.git", from: "2.0.0")
]
```

或 Xcode: `File → Swift Packages → Add Package Dependency`，输入 `https://github.com/cocoatoucher/Glide.git`，添加 `GlideEngine` 产品。

## 核心架构：Entity-Component-System

### 1. 创建场景 (GlideScene)

```swift
// 方式1: 使用 Tiled Map Editor 地图文件
let loader = TiledMapEditorSceneLoader(
    fileName: "map.json",
    bundle: Bundle.main,
    collisionTilesTextureAtlas: atlas,
    decorationTilesTextureAtlas: decorationAtlas
)
let sceneTileMaps = loader.tileMaps
let scene = GlideScene(
    collisionTileMapNode: sceneTileMaps.collisionTileMap,
    zPositionContainers: containers
)
skView.presentScene(scene)

// 方式2: 推荐 - 创建 GlideScene 子类
class MyScene: GlideScene {
    override func didMoveTo(from previousScene: GlideScene?) {
        // 初始化场景
    }
}
```

### 2. 创建实体 (GlideEntity)

```swift
let entity = GlideEntity(initialNodePosition: TiledPoint(x: 10, y: 10).point(with: scene.tileSize))

// 添加组件
let spriteComponent = SpriteNodeComponent(nodeSize: CGSize(width: 24, height: 24))
entity.addComponent(spriteComponent)

let kinematicsComponent = KinematicsBodyComponent()
entity.addComponent(kinematicsComponent)

scene.addEntity(entity)
```

### 3. 常用组件速查

| 类别 | 组件 | 用途 |
|------|------|------|
| **核心** | `SpriteNodeComponent` | 渲染精灵节点 |
| **核心** | `KinematicsBodyComponent` | 控制速度和位移 |
| **核心** | `ColliderComponent` | 碰撞检测 |
| **核心** | `ColliderTileHolderComponent` | 与地图碰撞体交互 |
| **核心** | `TextureAnimatorComponent` | 纹理动画 |
| **移动** | `HorizontalMovementComponent` | 水平移动 |
| **移动** | `VerticalMovementComponent` | 垂直移动 |
| **移动** | `CircularMovementComponent` | 圆周运动 |
| **能力** | `PlayableCharacterComponent` | 可操控角色 |
| **能力** | `JumpComponent` | 跳跃 |
| **能力** | `WallJumpComponent` | 蹬墙跳 |
| **能力** | `ProjectileShooterComponent` | 发射投射物 |
| **能力** | `HealthComponent` | 生命值 |
| **自主** | `SelfMoveComponent` | 自动移动 |
| **自主** | `SelfFollowWaypointsComponent` | 沿路径移动 |
| **自主** | `SelfShootOnObserveComponent` | 发现目标时射击 |
| **场景** | `CameraComponent` | 相机控制 |
| **场景** | `CameraFollowerComponent` | 跟随相机 |
| **环境** | `CheckpointComponent` | 检查点 |
| **平台** | `PlatformComponent` | 可站立的平台 |
| **平台** | `FallingPlatformComponent` | 踩踏后掉落平台 |
| **平台** | `BouncingPlatformComponent` | 弹跳平台 |
| **UI** | `SpeechBubbleTemplateEntity` | 对话气泡 |

### 4. 输入支持

```swift
// 支持的输入方式
// - 🎮 手柄: MFI/USB/蓝牙
// - ⌨️ 键盘: macOS/iPadOS
// - 🖱 鼠标: macOS/iPadOS
// - 👆 触摸: iOS

// 使用 PlayableCharacterComponent 自动处理输入
let playableComponent = PlayableCharacterComponent(playerIndex: 0)
entity.addComponent(playableComponent)
```

### 5. Tiled Map Editor 地图制作

1. 下载 [Tiled Map Editor](https://www.mapeditor.org) (推荐 1.2.x 版本)
2. 创建正交地图，保存为 JSON 格式
3. 使用 Glide 碰撞体贴图绘制可碰撞层，命名为 `Ground`
4. 创建装饰层用于视觉效果

碰撞块类型：
- `ground`: 普通地面
- `one_way`: 单向平台
- `jump_wall_right/left`: 蹬墙跳墙壁
- `slope_*`: 斜坡 (多种角度)

### 6. 相机系统

```swift
// 自动跟随 - 使用 CameraFollowerComponent
let cameraFollower = CameraFollowerComponent()
entity.addComponent(cameraFollower)

// 手动控制
scene.cameraNode.position = entity.transform.node.position
```

### 7. 常用代码片段

```swift
// 碰撞检测
let collider = ColliderComponent(
    categoryMask: DemoCategoryMask.player,
    size: CGSize(width: 24, height: 24),
    offset: .zero,
    leftHitPointsOffsets: (5, 5),
    rightHitPointsOffsets: (5, 5),
    topHitPointsOffsets: (5, 5),
    bottomHitPointsOffsets: (5, 5)
)

// 移动风格
enum MovementStyle {
    case fixedVelocity(CGFloat)  // 固定速度
    case acceleration(TimeInterval, CGFloat, CGFloat)  // 带加速度
}

// Z 轴层级管理
enum DemoZPositionContainer: Int, ZPositionContainer {
    case background
    case foregroundDecoration
    case entity
    case frontDecoration
}
```

## 文档链接

- [快速入门指南](https://github.com/cocoatoucher/Glide/blob/master/Docs/QuickStartGuide.md)
- [完整组件列表](https://github.com/cocoatoucher/Glide/blob/master/Docs/Components.md)
- [输入方式详解](https://github.com/cocoatoucher/Glide/blob/master/Docs/InputMethods.md)
- [Tiled 地图制作](https://github.com/cocoatoucher/Glide/blob/master/Docs/TiledMapEditorMaps.md)
