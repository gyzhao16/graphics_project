#### 计算机图形学Project1项目报告

16307130167 赵广源

##### 一、项目概述

编程实现音乐节奏或旋律的可视化。

##### 二、代码实现

[JavaScript Web Audio](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API) + [HTML Canvas](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLCanvasElement)在开发过程中是一套操作简便且效果优秀的音频可视化工具，因此本次project选用这两个工具来完成音频可视化的实现。

项目的主要思路为通过Web Audio解码音频文件，分析音频频谱，再把具体的音频采样数据通过HTML的Canvas元素绘制在网页上。

- Web Audio

  Web Audio API使用户可以在音频上下文(AudioContext)中进行音频操作，具有模块化路由的特点。在音频节点上操作进行基础的音频， 它们连接在一起构成音频路由图。即使在单个上下文中也支持多源，尽管这些音频源具有多种不同类型通道布局。这种模块化设计提供了灵活创建动态效果的复合音频的方法。音频源可以提供一个片段一个片段的音频采样数据（以数组的方式），一般，一秒钟的音频数据可以被切分成几万个这样的片段。这些片段可以是经过一些数学运算得到 ，也可以是音频或视频的文件读出来的，又或者是音频流。

- HTML Canvas

  HTML5\<canvas\>元素提供了一个通过JavaScript来绘制图形的方式。它可以用于动画、游戏画面、数据可视化、图片编辑以及实时视频处理等方面。

在具体的代码实现中，项目主要分为三个部分来完成：

1. 创建AudioContext处理音频

   - 解码音频文件，在调用`decodeAudioData()`之后，就会开始解码音频文件，解码成功之后会调用传入的回调函数。

     ```
     var DecodeMusic = function() {
     	audioContext.decodeAudioData(input_file,
     								function(buffer) {
        									MusicProcess(buffer);
         							});
     }
     ```

   - 创建两个音频处理节点，`BufferSourceNode`和`AnalyserNode`，分别用于播放解码出来的音频缓存和分析音频频谱。

     ```
     var BufferSourceNode, AnalyserNode;
     
     BufferSourceNode = audioContext.createBufferSource();
     AnalyserNode = audioContext.createAnalyser();
     ```

   - 连接上述两个节点，将待播放的音频输出到`audioContext.destination`，这样在解码完毕后就可以开始播放音频。

     ```
     BufferSourceNode.connect(AnalyserNode);
     
     AnalyserNode.connect(audioContext.destination);
     ```

2. 在Canvas元素中绘制音频频谱

   - 用一个数组保存之前在`AnalyserNode`中解析得到的音频频谱数据。

     ```
     var MusicArray = new Uint8Array(AnalyserNode.frequencyBinCount);
     AnalyserNode.getByteFrequencyData(MusicArray);
     ```

   - 创建并清理画布，之后在数组采样，依据采样得到的数据沿y轴正负两个方向延伸，画出相应比例的长条，得到最终的可视化图形。

     ```
     ctx = canvas.getContext("2d");
     ctx.strokeStyle = "#9900FF";
     ctx.lineWidth = 2;
     ctx.clearRect(0, 0, canvas.width, canvas.height);
     
     var step = Math.round(MusicArray.length / 60);
     for (var i = 0; i < 40; i++) {
     	var energy = (MusicArray[step * i] / 256.0) * 50;
     	for (var j = 0; j < energy; j++) {
     		...
     		...
     	}
     }
     ```

3. 编写一个简单的展示界面

   项目采用HTML编写了一个简单的网页用于选择本地的音频文件并展示可视化的效果，CSS样式和JavaScript处理逻辑也一并包含在内。

   ```
   <html>
   	<head>
   		<style type="text/css">
   		...
   		</style>
   		<script type="text/javascript">
   		...
   		</script>
   	</head>
   	<body style="background-color: #ffffff">
   		<h4>Graphics Project 1: Music Visualization</h1>
   		<div id="class1">
   		...
   		</div>
   	</body>
   </html>
   ```

##### 三、项目运行

使用浏览器打开`/project/project_1/project_1.html`。

##### 四、结果展示

页面和音频可视化效果如下图所示。

![image-20191226193218656](C:\Users\zgy\AppData\Roaming\Typora\typora-user-images\image-20191226193218656.png)

![image-20191226193509839](C:\Users\zgy\AppData\Roaming\Typora\typora-user-images\image-20191226193509839.png)

