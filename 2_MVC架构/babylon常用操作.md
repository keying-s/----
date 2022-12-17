# Babylon.js常用操作

## 1. 网格体

### 1.1 网格体的创建

创建基本网格体，立方体，球等等，更多用法[查阅文档](https://doc.babylonjs.com/features/featuresDeepDive/mesh/creation)。
```js
// 创建一个球

//scene 是要球所在的场景，如果不给这个参数，会自动附加到默认场景。
//options 是球的属性，比如半径，位置，可以是空对象
const sphere = BABYLON.MeshBuilder.CreateSphere("sphere", options, scene); 
```

导入自己的模型
```js

```

## 2. 场景

### 2.1 场景的创建

```js
var scene = new BABYLON.Scene(engine);
```

### 2.2 场景的保存

现将场景序列化为json格式，然后再将json格式转化为字符串。
    
```js
var serializedScene = BABYLON.SceneSerializer.Serialize(scene);

var strScene = JSON.stringify(serializedScene);
```

创建一个下载链接，然后点击下载链接，就可以下载场景了。

```js
var blob = new Blob([strScene], { type: "octet/stream" });

  // turn blob into an object URL; saved as a member, so can be cleaned out later
  objectUrl = (window.webkitURL || window.URL).createObjectURL(blob);
  var link = window.document.createElement("a");
  link.href = objectUrl;
  link.download = filename;//自己定义下载的名称
  link.click();
```