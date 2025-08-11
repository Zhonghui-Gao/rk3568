| 选择器名称                           | 选择的内容                                                   | 示例                                                         |
| ------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 元素选择器（也称作标签或类型选择器） | 所有指定类型的 HTML 元素                                     | `p` 选择 `<p>`                                               |
| ID 选择器                            | 具有特定 ID 的元素。单一 HTML 页面中，每个 ID 只对应一个元素，一个元素只对应一个 ID | `#my-id` 选择 `<p id="my-id">` 或 `<a id="my-id">`           |
| 类选择器                             | 具有特定类的元素。单一页面中，一个类可以有多个实例           | `.my-class` 选择 `<p class="my-class">` 和 `<a class="my-class">` |
| 属性选择器                           | 拥有特定属性的元素                                           | `img[src]` 选择 `<img src="myimage.png">` 但不是 `<img>`     |
| 属性选择器（宽泛）                   | 匹配特定属性的元素                                           | p[class~="special"]选择<p class="my special class"></p>      |
| 伪类选择器                           | 特定状态下的特定元素（比如鼠标指针悬停在链接上时）           | `a:hover` 选择仅在鼠标指针悬停在链接上时的 `<a>` 元素        |
| 伪元素选择器                         | 选择一个元素的某个部分而不是元素自己                         | 例如，p::first-line { } ，`::first-line`是会选择一个元素（下面的情况中是`<p>`）中的第一行 |
| “空格选择器”                         | 指定类型的指定类型HTML元素                                   | `li` `p` 选择`li`下的`p`                                     |
| “加号选择器”                         | 指定类型后面的指定类型HTML元素                               | `li` + `p` 选择li后面的p                                     |
| "逗号选择器"                         | 逗号前后指定类型的HTML元素                                   | `li`,`p` 选择li与p                                           |
| "大于选择器"                         | 用运算符（`>`）选择了`<article>`元素的初代子元素。           | article > p { }                                              |





最后，`text-shadow` 对元素的文本内容施加阴影。它的四个值是：

- 第一个像素值设置阴影与文本的**水平偏移量**：阴影水平方向移动的距离（译者注：向右为正值）。
- 第二个像素值设置阴影与文本的**垂直偏移量**：阴影垂直方向移动的距离（译者注：向下为正值）。
- 第三个像素值设置阴影的**模糊半径**。值越大产生的阴影越模糊。
- 第四个值设置阴影的基色。



`undefined` 值在布尔类型环境中会被当作 `false` 。例如，下面的代码将会执行函数 `myFunction`，因为数组 `myArray` 中的元素未被赋值：

```javascript
var myArray = [];
if (!myArray[0]) myFunction();
```

数值类型环境中 `undefined` 值会被转换为 `NaN`。

```javascript
var a;
a + 2; // 计算为 NaN
```



全局变量var 局部变量let 局部常量const

由于存在变量提升，一个函数中所有的`var`语句应尽可能地放在接近函数顶部的地方。这个习惯将大大提升代码的清晰度。



实际上，全局变量是*全局对象*的属性。在网页中，（译注：缺省的）全局对象是 [`window`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window) ，所以你可以用形如 `window.`*`variable`* 的语法来设置和访问全局变量。



| 原义字符 | 等价字符引用 |
| -------- | ------------ |
| <        | &lt;         |
| >        | &gt;         |
| "        | &quot;       |
| '        | &apos;       |
| &        | &amp;        |

```html
<p>HTML 中用 &lt;p&gt; 来定义段落元素</p>
```



### [在下载链接时使用 download 属性](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Introduction_to_HTML/Creating_hyperlinks#在下载链接时使用_download_属性)

当你链接到要下载的资源而不是在浏览器中打开时，你可以使用 `download` 属性来提供一个默认的保存文件名。下面是一个 Firefox 的 Windows 最新版本下载链接的示例：

```html
<a
  href="https://download.mozilla.org/?product=firefox-latest-ssl&os=win64&lang=zh-CN"
  download="firefox-latest-64bit-installer.exe">
  下载最新的 Firefox 中文版 - Windows（64 位）
</a>
```



### [HTML 验证](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Introduction_to_HTML/Debugging_HTML#html_验证)

阅读以上示例后，你发现保持良好 HTML 格式的重要性。那么应该如何做呢？以上示例规模较小，查找错误还不难，但是一个非常庞大、复杂的 HTML 文档呢？

最好的方法就是让你的 HTML 页面通过 [Markup Validation Service](https://validator.w3.org/)。由 W3C（制定 HTML、CSS 和其他网络技术标准的组织）创立并维护的标记验证服务。把一个 HTML 文档加载至本网页并运行，网页会返回一个错误报告。



- WebM 容器通常包括了 Vorbis 或 Opus 音频和 VP8/VP9 视频。这在所有的现代浏览器中都支持，除了某些老版本浏览器。
- MP4 容器通常包括 AAC 或 MP3 音频和 H.264 视频。这在所有的现代浏览器中都支持。
- Ogg 容器倾向于使用 Vorbis 音频和 Theora 视频。其在 Firefox 和 Chrome 当中受到完美的支持，不过这个容器已经被更强大的 WebM 格式所取代。

```html
<video
  controls
  width="400"
  height="400"
  autoplay
  loop
  muted
  preload="auto"
  poster="poster.png">
  <source src="rabbit320.mp4" type="video/mp4" />
  <source src="rabbit320.webm" type="video/webm" />
  <p>你的浏览器不支持此视频。可点击<a href="rabbit320.mp4">此链接</a>观看</p>
</video>
```



```css
html {
    background-color: #1f1f1f;
    border-color: #1f1f1f;
    color: #4a9cb3;
    font-size: 5em;
}
li {
  list-style-type: none; 你可能不想要li的项目符号
}
```

![img](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/First_steps/How_CSS_works/rendering.svg)





来源优先权总是胜过选择器优先级。





### 盒模型

两种盒子模型，通过box-sizing:border-box确定是否计算mborder和padding

margin的计算规则：

- 两个正外边距将合并为一个外边距。其大小等于最大的单个外边距。
- 两个负外边距会折叠，并使用最小（离零最远）的值。
- 如果其中一个外边距为负值，其值将从总值中*减去*

#### 理解布局

##### 记住这个，就能很快理解inline、block、inline-block三个的区别

1. inline行内元素，width和height无效，类似文字的展示方式，是属于**“紧缩式”**布局方式，无论怎么放都挤在一起。
2. block**大块头**，放到哪里，就要把一整行占着；display:flex的情况除外；
3. inline-block，兼具两者的好处，可以挤在一起，也可以有“**尺度**”（可以设置width和height）的挤在一起。

然后，flex主要是影响修饰目标的子项，对于目标自己，就是默认block；当然，如果你用inline-flex，那么目标自己就是inline了



### 图像、媒体和表单元素

```html
object-fit: cover; // 维持比例
object-fit: contain；// 维持包含
object-fit: fill; // 充满
```



## JS

```javascript
<label for="customname" id="lbl-customname">请输入自定义的名字：</label>
/* 如果想要获取label输入框中内容 ... */
const customName = document.getElementById("customname");
const customName = document.getElementById('lbl-customname').innerText;
/* querySelector是找类 */
```

## 事件处理： 生存期管理

事件处理器也可以通过传递 [`AbortSignal`](https://developer.mozilla.org/zh-CN/docs/Web/API/AbortSignal) 到 [`addEventListener()`](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)，然后在拥有 `AbortSignal` 的控制器上调用[`abort()`](https://developer.mozilla.org/zh-CN/docs/Web/API/AbortController/abort)，从而删除事件处理器。例如，要添加一个可以使用 `AbortSignal` 来删除的事件处理器，可以这样做：

```jav
const controller = new AbortController();

btn.addEventListener("click",
  () => {
    const rndCol = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
    document.body.style.backgroundColor = rndCol;
  },
  { signal: controller.signal } // 向该处理器传递 AbortSignal
);
```

然后可以像这样删除上面代码创建的事件处理器：

```javascript
controller.abort(); // 移除任何/所有与该控制器相关的事件处理器
```

## 事件处理：event的使用

有时候在事件处理函数内部，你可能会看到一个固定指定名称的参数，例如 `event`、`evt` 或 `e`。这被称为**事件对象**，它被自动

传递给事件处理函数，以提供额外的功能和信息。例如，让我们稍稍重写一遍我们的随机颜色示例：

```javascript
const btn = document.querySelector("button");

function random(number) {
  return Math.floor(Math.random() * (number + 1));
}

function bgChange(e) {
  const rndCol = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
  e.target.style.backgroundColor = rndCol;
  console.log(e);
}

btn.addEventListener("click", bgChange);
```

在这里，可以看到我们在函数中包括一个事件对象 `e`，并在函数中设置背景颜色样式在 `e.target` 上——它指的是按钮本身。事件对象 `e` 的 `target` 属性始终是事件刚刚发生的元素的引用。所以在这个例子中，我们在按钮上设置一个随机的背景颜色，而不是页面。

## 事件处理：默认行为的控制

```javascript
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
  </head>
  <body>
    <form id="formId">
    <div>
        <label for="fname">First name: </label>
        <input id="fname" type="text" />
    </div>
    <div>
        <label for="lname">Last name: </label>
        <input id="lname" type="text" />
    </div>
    <div>
        <input id="submit" type="submit" />
    </div>
    </form>
    <p></p>
    <script>
      const form = document.querySelector("form");
      const formId = document.getElementById("formId");
      const fname = document.getElementById("fname");
      const lname = document.getElementById("lname");
      const para = document.querySelector("p");

      formId.addEventListener("submit", (e) => {
        if (fname.value === "" || lname.value === "") {
            e.preventDefault();
            para.textContent = "You need to fill in both names!";
        }
      });
    </script>
  </body>
</html>
```





## 操作文档

```javascript
Document.querySelector("p")
Document.createElement("p")
Document.getElementById()
Document.getElementsByTagName()
Document.createTextNode() 
Node.appendChild()
```
