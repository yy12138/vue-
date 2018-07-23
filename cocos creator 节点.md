一、查找子节点

有时候，游戏场景中会有很多个相同类型的对象，像是炮塔、敌人和特效，它们通常都有一个全局的脚本来统一管理。如果用 属性检查器 来一个一个将它们关联到这个脚本上，那工作就会很繁琐。为了更好地统一管理这些对象，我们可以把它们放到一个统一的父物体下，然后通过父物体来获得所有的子物体：
```
// CannonManager.js

cc.Class({
    extends: cc.Component,

    start: function () {
        var cannons = this.node.children;
        // ...
    }
});
```

你还可以使用 getChildByName：
```
this.node.getChildByName("Cannon 01");
```
如果子节点的层次较深，你还可以使用 cc.find，cc.find 将根据传入的路径进行逐级查找：

```
cc.find("Cannon 01/Barrel/SFX", this.node);
```

二、更改节点位置

分别对 x 轴和 y 轴坐标赋值：

```
this.node.x = 100;
this.node.y = 50;
```
使用 setPosition 方法：
```
this.node.setPosition(100, 50);
this.node.setPosition(cc.v2(100, 50));
```
设置 position 变量：
```
this.node.position = cc.v2(100, 50);
```

三、更改节点旋转
```
this.node.rotation = 90;
//或
this.node.setRotation(90);
```

四、更改节点缩放
```
this.node.scaleX = 2;
this.node.scaleY = 2;

//或

this.node.setScale(2);
this.node.setScale(2, 2);
```
五、更改节点尺寸
```
this.node.setContentSize(100, 100);
this.node.setContentSize(cc.v2(100, 100));

//或

this.node.width = 100;
this.node.height = 100;
```
六、更改节点锚点位置
```
this.node.anchorX = 1;
this.node.anchorY = 0;

//或

this.node.setAnchorPoint(1, 0);
```
七、颜色和不透明度
```
mySprite.node.color = cc.Color.RED;

//设置不透明度：

mySprite.node.opacity = 128;
```
八、常用组件接口
cc.Component 是所有组件的基类，任何组件都包括如下的常见接口（假设我们在该组件的脚本中，以 this 指代本组件）：

this.node：该组件所属的节点实例
this.enabled：是否每帧执行该组件的 update 方法，同时也用来控制渲染组件是否显示
update(dt)：作为组件的成员方法，在组件的 enabled 属性为 true 时，其中的代码会每帧执行
onLoad()：组件所在节点进行初始化时（节点添加到节点树时）执行
start()：会在该组件第一次 update 之前执行，通常用于需要在所有组件的 onLoad 初始化完毕后执行的逻辑

生命周期回调[http://docs.cocos.com/creator/manual/zh/scripting/life-cycle-callbacks.html](https://note.youdao.com/)

九、通过常驻节点进行场景资源管理和参数传递

引擎同时只会运行一个场景，当切换场景时，默认会将场景内所有节点和其他实例销毁。如果我们需要用一个组件控制所有场景的加载，或在场景之间传递参数数据，就需要将该组件所在节点标记为「常驻节点」，使它在场景切换时不被自动销毁，常驻内存。我们使用以下接口：
```
cc.game.addPersistRootNode(myNode);
```
上面的接口会将 myNode 变为常驻节点，这样挂在上面的组件都可以在场景之间持续作用，我们可以用这样的方法来储存玩家信息，或下一个场景初始化时需要的各种数据。

如果要取消一个节点的常驻属性：
```
cc.game.removePersistRootNode(myNode);
```
需要注意的是上面的 API 并不会立即销毁指定节点，只是将节点还原为可在场景切换时销毁的节点。

十、动态加载
动态加载资源要注意两点，一是所有需要通过脚本动态加载的资源，都必须放置在 resources 文件夹或它的子文件夹下。resources 需要在 assets 文件夹中手工创建，并且必须位于 assets 的根目录，就像这样：

[http://docs.cocos.com/creator/manual/zh/scripting/load-assets/resources-file-tree.png](https://note.youdao.com/)

这里的 image/image(SpriteFrame), prefab, anim, font 都是常见的 Asset，而 atom(particle), audio 则是常见的 Raw Asset。

resources 文件夹里面的资源，可以关联依赖到文件夹外部的其它资源，同样也可以被外部场景或资源引用到。项目构建时，除了已在 构建发布 面板勾选的场景外，resources 文件夹里面的所有资源，连同它们关联依赖的 resources 文件夹外部的资源，都会被导出。如果一份资源不需要由脚本直接动态加载，那么千万不要放在 resources 文件夹里。

第二个要注意的是 Creator 相比之前的 Cocos2d-html5，资源动态加载的时都是异步的，需要在回调函数中获得载入的资源。这么做是因为 Creator 除了场景关联的资源，没有另外的资源预加载列表，动态加载的资源是真正的动态加载。

十一、Cocos Creator 中内置了一个简单易用的碰撞检测系统，他会根据添加的碰撞组件进行碰撞检测。
当一个碰撞组件被启用时，这个碰撞组件会被自动添加到碰撞检测系统中，并搜索能够与他进行碰撞的其他已添加的碰撞组件来生成一个碰撞对。
需要注意的是，一个节点上的碰撞组件，无论如何都是不会相互进行碰撞检测的。
