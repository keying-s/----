# WEB3D

## 1. 桌面图形学API

### 1.1. OpenGL

`OpenGL`是一套规范，为我们提供了一组多语言，跨平台的API，用于渲染3D图形。

很多硬件厂商都实现了这套规范，例如`NVIDIA`，并且提供了硬件层面上的优化和加速，

### 1.2. DirectX

`DirectX`是微软公司自己开发的一套图形渲染API，用于渲染3D图形，主要在Windows平台上使用。

2009年，微软提出了DX11版本，有一个比较大的特性，计算着色器。简单说就是在图形的渲染能力上，可以更加充分的调用显卡的并行计算能力。

随着显卡能力的提升，OpenGL的全局状态机设计，已经无法调用和优化先进现代显卡的能力。

## 2. Web图形学API——对桌面图形学API的封装

### 2.1. WebGL

`WebGL`（Web 图形库）是一组 JavaScript API，与 OpenGL ES 2.0 的API非常一致。这种一致性使 `WebGL` 可以类似`OpenGL`利用用户设备提供的硬件图形加速。

`WebGL`依靠浏览器的内核，对OpenGL ES版本做打包和封装。

`WebGL` 用法与`OpenGL`类似，都比较繁琐:

一般有这么几步：

1. 创建`WebGL`上下文。

2. 清空画布

3. 设置相机朝向...

4. 创建，编译着色器程序(GLSL)

5. 创建绘制目标的顶点数组对象.........

### 2.2. WebGPU

`WebGPU`是一套基于浏览器的图形API，浏览器封装了现代图形API（Dx12、Vulkan、Metal），为 Web释放了更多的GPU 硬件的功能。

比起`WebGL`，`WebGPU`这种技术方案能够更好地调用GPU的性能，靠拢桌面级的渲染效果。

## 3. Babylon.js——对Web图形学API的封装

`Babylon.js`是一个3D引擎，它对`WebGL`和`WebGPU`做了封装，提供了一套简单易用的API，用于渲染3D图形。

它有许多强大易用的功能。

### 3.1. 简单 灵活 易拓展

从某种的角度来说，它只是提供了一套api。

不用下载任何东西，只需要几行代码就可以创建一个3D场景。

在html文件中引入`Babylon.js`：

```html
<script src="https://cdn.babylonjs.com/babylon.js"></script>
```

创建一个画布：

```html
<canvas id="renderCanvas"></canvas>
```

编写代码创建场景：

```js
var canvas = document.getElementById("renderCanvas");
var engine = new BABYLON.Engine(canvas, true);
var scene = new BABYLON.Scene(engine);
var camera = new BABYLON.ArcRotateCamera("Camera", 0, 0, 0, BABYLON.Vector3.Zero(), scene);
camera.setPosition(new BABYLON.Vector3(0, 5, -10));
var light = new BABYLON.HemisphericLight("light1", new BABYLON.Vector3(0, 1, 0), scene);
var sphere = BABYLON.MeshBuilder.CreateSphere("sphere1", {diameter: 2, segments: 32}, scene);
sphere.position.y = 1;
var ground = BABYLON.MeshBuilder.CreateGround("ground1", {width: 6, height: 6, subdivisions: 2}, scene);
engine.runRenderLoop(function () {
    scene.render();
});
```

现在在浏览器中打开demo，就能看到一个3D球体。

[demo1](./demo1.html)

这也意味着很高的灵活度和可拓展性，稍微改一改html文件就能够在网页中添加自己想要的其他内容。

还可以轻易地融合前端社区的别的库，比如构建前端APP的Vue，来一起构建网页。

### 3.2. 轻量级 高性能

`Babylon.js`天生就是在web端的，比起其他的3D引擎，它更加适合web端的渲染。

`BABYLON.JS 5.0`以后，开始对`WebGPU`进行支持，这意味着它可以更好地利用GPU的性能，接近桌面级别的渲染效果。

[WebGPU](https://developer.chrome.com/docs/web-platform/webgpu/)

### 3.3. 游乐场

一个更加复杂一些的demo：

[demo2](https://playground.babylonjs.com/#3I55DK#0)

### 3.5. 可视化的编辑器和材质蓝图

### 3.6. 丰富的社区资源

资源管理系统