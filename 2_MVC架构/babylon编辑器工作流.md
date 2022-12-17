# babylon编辑器的工作流

## 1.入口

在编辑器里创建一个空项目，然后VSCode里打开该项目的文件夹。具体参考[官方文档](https://doc.babylonjs.com/communityExtensions/editor/workspace/creatingWorkspace)。

一般的入口代码是这样的：

```js
//获取画布
var canvas = document.getElementById("renderCanvas"); 
//生成引擎实例
var engine = new BABYLON.Engine(canvas, true); 

//创建一个场景
var createScene = function () {
    // Create the scene space
    var scene = new BABYLON.Scene(engine);

    // Add a camera to the scene and attach it to the canvas
    var camera = new BABYLON.ArcRotateCamera("Camera", 0, 0, 10, BABYLON.Vector3.Zero(), scene);
    camera.attachControl(canvas, true);

    // Add lights to the scene
    var light1 = new BABYLON.HemisphericLight("light1", new BABYLON.Vector3(1, 1, 0), scene);

    // Add and manipulate meshes in the scene
    var sphere = BABYLON.MeshBuilder.CreateSphere("sphere", {diameter: 2}, scene);

    return scene;
};

// 获取场景
var scene = createScene();

//主循环
engine.runRenderLoop(function () {
    scene.render();
});
```

入口代码就做了这样几件事：

- 获取画布
- 生成引擎实例附加到画布
- 创建场景附加到引擎
- 主循环重复渲染场景

当然，场景不一定要用代码创建，还可以读取一个已经有了的文件场景。

```js
var scene = await BABYLON.SceneLoader.AppendAsync("scenes/", "scene.babylon", engine);

//do something with the scene
```
这个场景文件可以由babylon的编辑器导出，也可以用代码创建场景后调用`scene.serialize()`方法来序列化，然后下载到本地。详细操作请参考[babylon常用操作](./babylon常用操作.md)或者[babylon官方文档](https://doc.babylonjs.com/features/featuresDeepDive/Exporters/Save_Babylon)。

## 2.主循环

入口结束以后，就到了主循环。

在主循环里每执行一次是一帧，还可以执行其它操作。

```js
engine.runRenderLoop(function () {
    scene.render();
    //do something else
});
```

`scene`还有一个属性`scene._frameId`，它是一个整数，每一帧都会自动加1。

但是在编辑器里，如果我们要为节点附加脚本的话，脚本是这样的：

```js
    /**
     * Called on the scene starts.
     */
    public onStart(): void {
        // ...
    }

    /**
     * Called each frame.
     */
    public onUpdate(): void {
        // ...
    }
```

`onStart`函数会在场景开始时执行一次，`onUpdate`函数会在每一帧执行一次。

编辑器为了给我们省事，只暴露了一些方法，我们在里面写代码就可以完成一些逻辑。但是，入口文件的主循环里并没有调用这些方法，所以它们是怎么每帧都会执行的呢？

编辑器给了这样的一个库`tools.ts`，它提供了一个工具函数。

```js
//导入
import { appendScene } from "./scenes/tools";

//调用
await appendScene(this.scene, "./", "../scene/scene.babylon");
```

这个`appendScene`函数并不是babylon的官方接口，tools这个库封装了官方的`SceneLoader.AppendAsync`方法。

官方的`SceneLoader.AppendAsync`只是简单地把读取的文件里的资源附加到当前场景上，并不会执行脚本里的方法。

而`appendScene`函数做完前一件事以后，还会执行一些额外的操作，比如执行脚本里的`onStart`方法，然后把`onUpdate`方法放到主循环里。

部分代码：

```js
scene.executeWhenReady(() => instance["onStart"]());

scene.onBeforeRenderObservable.add(() => instance["onUpdate"]());
```
这里`scene`类的`executeWhenReady`方法是在场景加载完毕后执行一次，`onBeforeRenderObservable.add`是每帧调用一次。

这两个方法就是引擎提供的原生方法了。

它具体是怎么做到的，可以去看`tools.ts`里的源码。

从这个意义上来说，编辑器仅仅是一个可以方便地编辑场景的东西，而非什么深奥的东西。

编辑器更多用法请查阅[官方文档](https://doc.babylonjs.com/communityExtensions/editor)。

## 3.场景文件

babylon的场景文件是一个json文件，后缀名以`.babylon`结尾。

它包含了场景的所有信息，包括场景里的所有节点，所有的模型，灯光，以及一些物理参数等等。

当我们导入一个外部的模型以后，引擎会做两件事情：

1. 把模型的顶点信息储存到场景文件里，这叫*几何体信息（geometries）*。

```json
vertexData": [
            {
                "id": "245753c2-bff9-4bd1-8ea9-da06e057793e",
                "uniqueId": 32,
                "updatable": false,
                "positions": [
                    0.0024745550472289324,
                    0.002076843287795782,
                    -0.006877691484987736,
                    0.002513291547074914,
                    ...
                    ],
                "normals": [
                    ...
                ]
            }
        ]
```

2. 生成几张贴图到文件夹里，例如法线贴图，纹理贴图等等，然后利用这几张贴图生成一个材质文件放到文件夹里。场景文件里记录哪个几何体用了什么材质就好。

`mesh`代表网格体，它是由几何体和材质组成的。场景文件里还额外记录了这个网格体的位置，旋转，缩放等信息。

```json
"name": "BoomBox",
"id": "594118ce-c987-4315-89a3-1bbdbf3194e4",
"uniqueId": 31,
"type": "Mesh",
"position": [
    6.61083459854126,
    3.316915273666382,
    0
],
"scaling": [
    100,
    100,
    100
],
...
```



