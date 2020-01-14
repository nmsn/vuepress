---
sidebar: auto
---

# css

## css性能优化

原文：[CSS性能优化的8个技巧](https://juejin.im/post/5b6133a351882519d346853f?utm_source=gold_browser_extension)

### 1. 内联首屏关键css(critical css)

### 2. 异步加载css

1. 使用JavaScript动态创建样式表link元素，并插入到DOM中

    ```js
    // 创建link标签
    const myCSS = document.createElement( "link" );
    myCSS.rel = "stylesheet";
    myCSS.href = "mystyles.css";
    // 插入到header的最后位置
    document.head.insertBefore( myCSS, document.head.childNodes[ document.head.childNodes.length - 1 ].nextSibling );
    ```

2. 将link元素的media属性设置为用户浏览器不匹配的媒体类型（或媒体查询），如media="print"，甚至可以是完全不存在的类型media="noexist"。对浏览器来说，如果样式表不适用于当前媒体类型，其优先级会被放低，会在不阻塞页面渲染的情况下再进行下载

3. `rel="preload"`这一Web标准指出了如何异步加载资源，包括CSS类资源

    `<link>` 元素的 rel 属性的属性值preload能够让你在你的HTML页面中 `<head>`元素内部书写一些声明式的资源获取请求，可以指明哪些资源是在页面加载完成后即刻需要的。对于这种即刻需要的资源，你可能希望在页面加载的生命周期的早期阶段就开始获取，在浏览器的主渲染机制介入前就进行预加载。这一机制使得资源可以更早的得到加载并可用，且更不易阻塞页面的初步渲染，进而提升性能

### 3. 文件压缩

  文件的大小会直接影响浏览器的加载速度，这一点在网络较差时表现地尤为明显。相信大家都早已习惯对CSS进行压缩，现在的构建工具，如webpack、gulp/grunt、rollup等也都支持CSS压缩功能。压缩后的文件能够明显减小，可以大大降低了浏览器的加载时间。

  webpack中可以使用
  `mini-css-extract-plugin`插件导出css
  `optimize-css-assets-webpack-plugin`压缩代码
  
### 4. 删除无用css

  找到并删除代码中无用的css，可以借助`Uncss`库来实现自动删除
  
### 5. 有选择地使用选择器

1. 保持简单，不要使用嵌套过多过于复杂的选择器
2. 通配符和属性选择器效率最低，需要匹配的元素最多，尽量避免使用
3. 不要使用类选择器的ID选择器修饰元素标签
4. 不要为了追求速度而放弃可读性和可维护性

### 6. 减少使用昂贵的属性

在浏览器绘制屏幕时，所有需要浏览器进行操作或计算的属性相对而言都需要花费更大的代价。当页面发生重绘时，它们会降低浏览器的渲染性能。所以在编写CSS时，我们应该尽量减少使用昂贵属性，如`box-shadow`/`border-radius`/`filter`/`透明度`/`:nth-child`等

### 7. 优化重排(reflow)和重绘(repaint)

导致重绘的操作:

1. 改变`font-size`和`font-family`
2. 改变元素的内外边距
3. 通过js改变css类
4. 通过js获取DOM元素的位置相关属性
5. css伪类激活
6. 滚动滚动条或者改变窗口大小

### 4. 不要使用@import

原因：

1. 使用@import引入css会影响浏览器的并行下载。使用@import引用的css闻不见只有在引用它的那个css文件被下载、解析之后，浏览器才会知道还有另外一个css需要下载，这时才去下载，然后下载后开始解析、构建render树等一系列操作。这就导致浏览器无法并行下载所需的样式文件

2. 多个@import会导致下载顺序混乱。在IE中，@import会引发资源文件的下载顺序被打乱，即排列在@import后面的js文件先于@import下载，并且打乱甚至破坏@import自身的并行下载