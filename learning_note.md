skia重要的类：

SkCanvas：用于绘制2D图形的画布类。

SkPaint：用于定义绘制样式（如颜色、线条宽度、字体等）的绘制笔类。

SkPath：用于定义和操作2D路径的类，可以绘制各种形状，如线条、曲线、矩形等。

SkBitmap：用于表示位图图像的类，可以进行像素级别的操作和处理。

SkShader：用于创建渐变、图案等特殊填充效果的类。

SkSurface：用于创建可绘制的图形表面的类，可以将绘制操作输出到图像、屏幕等目标上。

SkImage：用于表示图像数据的类，可以加载、解码和操作图像。

SkColorFilter：用于对颜色进行变换和过滤的类，可以调整颜色的亮度、对比度等属性。

SkTypeface：用于表示字体的类，可以加载和使用各种字体文件。

SkRegion：用于表示和操作图形区域的类，可以进行区域的合并、裁剪等操作。



https://zhuanlan.zhihu.com/p/438707724

Skia在结构上大致分成三层：画布层，渲染设备层和封装适配层。

![img](.\pic\v2-064e7d32fc32819cf87a7a5fa6e15776_r.jpg)

1. `/skia/codec/`目录：该目录可能包含了Skia图像编解码库的主要实现代码，包括图像编解码器的实现和相关的工具类。
2. `/skia/include/codec/`目录：该目录可能包含了Skia图像编解码库的头文件，定义了图像编解码库的公共接口和类。
3. `/skia/src/codec/`目录：该目录可能包含了Skia图像编解码库的源代码文件，包括编解码器的具体实现和辅助函数。

- `/skia/codec/SkCodec.h`：该文件定义了Skia图像编解码库的主要接口和类。
- `/skia/codec/SkCodec.cpp`：该文件包含了Skia图像编解码库的实现代码。
- `/skia/codec/BitmapUtils.h`：该文件包含了与位图处理相关的实用函数和工具类。
- `/skia/codec/codec/` 目录：该目录可能包含了不同的图像编解码器实现，例如JPEG、PNG等。
- `/skia/codec/skottie/` 目录：该目录可能包含了Skottie动画解码器的实现，用于解码和渲染Lottie动画。



```
  +-------------------+        +-------------------------+        +------------------+
  |                   |        |                         |        |                  |
  |   SkCanvas        |        |      Skia GPU Backend   |        |    OpenGL        |
  |                   |        |                         |        |                  |
  +-------------------+        +-------------------------+        +------------------+
            |                           |                             |
            |                           |                             |
            |                           |                             |
            |        drawRect()         |                             |
            |-------------------------->|                             |
            |                           |                             |
            |          GrContext        |                             |
            |-------------------------> |                             |
            |                           |                             |
            |          GrPaint          |                             |
            |-------------------------> |                             |
            |                           |                             |
            |                           |          OpenGL             |
            |                           |---------------------------->|
            |                           |         Render Quad         |
            |                           |<----------------------------|
            |                           |                             |
            |                           |                             |
            |                           |         Draw Quad           |
            |                           |<----------------------------|
            |                           |                             |
            |                           |                             |
            |        Finish drawRect()  |                             |
            |<--------------------------|                             |
            |                           |                             |
            |                           |                             |

```



关键函数和类的说明：

- SkCanvas: Skia提供的绘图工具，用于绘制图形和文本。
- drawRect(): SkCanvas的函数，用于在画布上绘制矩形。
- GrContext: Skia的GPU上下文，用于管理GPU资源和操作。
- GrPaint: Skia的绘制参数对象，用于设置矩形绘制的样式、颜色等。
- OpenGL: 一种开放的图形API，用于与GPU进行交互。
- Render Quad: GPU执行的渲染命令，用于将矩形渲染到OpenGL的渲染缓冲区。
- Draw Quad: 渲染缓冲区中的绘制命令，用于将渲染结果绘制到屏幕上。

整个流程的大致步骤如下：

1. SkCanvas调用drawRect()函数，指定要绘制的矩形形状和样式。
2. drawRect()函数将矩形绘制命令发送给GrContext。
3. GrContext将绘制命令转换为GrPaint对象，其中包含绘制的参数和样式。
4. GrContext将GrPaint对象和矩形数据发送给OpenGL进行渲染。
5. OpenGL执行渲染命令，将矩形渲染到渲染缓冲区。
6. 渲染缓冲区中的绘制命令将渲染结果绘制到屏幕上，完成矩形的渲染。



```
   +----------------------+
   |       drawRect       |
   +----------------------+
              |
              v
   +----------------------+
   |    validateRect      |
   +----------------------+
              |
              v
   +----------------------+
   |  prepareForDraw      |
   +----------------------+
              |
              v
   +----------------------+
   |  createDrawCommand   |
   +----------------------+
              |
              v
   +----------------------+
   | drawRectInternal     |
   +----------------------+
              |
              v
   +----------------------+
   |  createVertexBuffer  |
   +----------------------+
              |
              v
   +----------------------+
   |   compileShader      |
   +----------------------+
              |
              v
   +----------------------+
   |   attachShader       |
   +----------------------+
              |
              v
   +----------------------+
   |    linkProgram       |
   +----------------------+
              |
              v
   +----------------------+
   |    useProgram        |
   +----------------------+
              |
              v
   +----------------------+
   |    createTexture     |
   +----------------------+
              |
              v
   +----------------------+
   |    setUniforms       |
   +----------------------+
              |
              v
   +----------------------+
   | setVertexAttributes  |
   +----------------------+
              |
              v
   +----------------------+
   | bindVertexBuffer     |
   +----------------------+
              |
              v
   +----------------------+
   | bindIndexBuffer      |
   +----------------------+
              |
              v
   +----------------------+
   |    drawElements      |
   +----------------------+

```

在Skia中，`drawRect`函数通过一系列的函数调用将绘制命令传递给底层的OpenGL接口来实现矩形的绘制。下面是这个过程的详细步骤：

1. 首先，在调用`drawRect`之前，你需要通过调用`createRenderContext`创建一个渲染上下文，这个渲染上下文会提供给后续的绘制函数使用。
2. 接下来，在`drawRect`函数内部，会调用`validateRect`函数来验证矩形的参数和状态。
3. 在通过验证后，会调用`prepareForDraw`函数来准备绘制的上下文状态。
4. 接下来，会调用`createDrawCommand`函数创建一个绘制命令对象，该对象包含矩形的绘制信息和所需的渲染数据。
5. 然后，会调用`drawRectInternal`函数来执行矩形的实际绘制操作。在这个函数内部，会涉及到创建顶点缓冲区、编译和链接着色器程序、创建纹理等一系列的操作。
6. 在创建顶点缓冲区时，会调用`createVertexBuffer`函数来生成一个用于存储矩形顶点数据的缓冲区对象。
7. 在编译和链接着色器程序时，会调用一系列的函数，包括`compileShader`、`attachShader`、`linkProgram`和`useProgram`，来完成着色器程序的创建和使用。
8. 在创建纹理时，会调用`createTexture`函数生成一个用于存储矩形纹理数据的纹理对象，并使用其他相关函数来设置纹理的参数、上传纹理数据和绑定纹理。
9. 接下来，会调用`setUniforms`函数来设置着色器程序中的uniform变量的值，以便对矩形进行正确的着色。
10. 然后，会调用`setVertexAttributes`函数来设置顶点属性，例如顶点位置和纹理坐标等。
11. 在绘制之前，需要绑定顶点缓冲区和索引缓冲区，这是通过调用`bindVertexBuffer`和`bindIndexBuffer`函数来完成的。
12. 最后，通过调用`drawElements`函数来执行绘制操作，实际上是调用了底层的OpenGL绘制函数，例如`glDrawElements`。

通过这些函数调用的组合，Skia可以将绘制命令传递给底层的OpenGL接口，以在GPU上进行矩形的绘制操作。





Skia中，调用OpenGL的接口主要位于`/skia/src/gpu/gl/GrGLInterface.h`文件中。这个文件定义了用于与OpenGL交互的接口函数。

以下是一些常用的OpenGL接口函数：

1. `fBindBuffer`: 绑定缓冲区对象
2. `fBufferData`: 在缓冲区对象中分配并初始化数据
3. `fCreateProgram`: 创建着色器程序
4. `fAttachShader`: 将着色器附加到程序对象
5. `fLinkProgram`: 链接着色器程序
6. `fUseProgram`: 使用指定的着色器程序
7. `fGenBuffers`: 生成缓冲区对象
8. `fDeleteBuffers`: 删除缓冲区对象
9. `fEnable`: 启用指定的OpenGL功能
10. `fDisable`: 禁用指定的OpenGL功能
11. `fClearColor`: 设置清除颜色
12. `fClear`: 清除指定的缓冲区
13. `fViewport`: 设置视口大小和位置
14. `fDrawArrays`: 绘制基本图元，使用数组中的顶点数据
15. `fDrawElements`: 绘制基本图元，使用索引缓冲区中的索引数据





要加载一个JPEG图像，你可以使用Skia的skcodec模块提供的函数来实现。下面是一个加载JPEG图像的简单示例代码

```cpp
#include "include/core/SkCodec.h"

// 加载JPEG图像
bool LoadJPEGImage(const char* filename, SkBitmap* bitmap) {
    // 创建一个SkCodec对象
    sk_sp<SkData> encodedData = SkData::MakeFromFileName(filename);
    if (!encodedData) {
        return false;
    }
    sk_sp<SkCodec> codec = SkCodec::MakeFromData(encodedData);

    // 检查解码是否成功
    if (!codec) {
        return false;
    }

    // 获取图像信息
    SkImageInfo info = codec->getInfo();

    // 分配像素缓冲区
    SkBitmap decodedBitmap;
    if (!decodedBitmap.tryAllocPixels(info)) {
        return false;
    }

    // 解码图像数据
    if (codec->getPixels(info, decodedBitmap.getPixels(), decodedBitmap.rowBytes()) != SkCodec::kSuccess) {
        return false;
    }

    // 将解码后的图像复制到输出bitmap
    decodedBitmap.readPixels(info, bitmap->getPixels(), bitmap->rowBytes(), 0, 0);

    return true;
}

```

上述代码中，我们首先通过`SkData::MakeFromFileName`函数将JPEG图像文件加载到内存中，然后使用`SkCodec::MakeFromData`创建一个`SkCodec`对象，该对象负责解码图像数据。

接下来，我们检查解码是否成功，并获取图像的信息。然后分配一个`SkBitmap`对象作为解码后的图像数据存储缓冲区。

最后，我们调用`getPixels`函数将解码后的图像数据复制到输出的`SkBitmap`对象中，以便后续使用。



在Skia中，图像编解码的抽象接口是`SkCodec`类，它的定义位于`include/core/SkCodec.h`文件中。`SkCodec`类定义了一系列方法，用于图像的解码和相关操作。

以下是一些`SkCodec`类的重要方法和功能：

- `getInfo()`：获取图像的基本信息，如宽度、高度、颜色类型等。
- `getPixels()`：获取解码后的像素数据。
- `getFrameCount()`：获取图像的帧数。
- `getFrameInfo()`：获取指定帧的信息，如帧的持续时间、偏移量等。
- `getFrameXxx()`：获取指定帧的像素数据、延迟时间等。
- `rewind()`：将解码器重置到初始状态。
- `decodeXxx()`：解码图像数据。
- `getSupportedDecodeConfigs()`：获取支持的解码配置。

这些方法提供了图像解码的基本操作和查询功能。具体的解码操作会根据图像的编码格式而有所不同。Skia会根据配置和可用的编解码库，选择适合的解码实现。

值得注意的是，Skia还提供了一些辅助类和函数，用于简化图像编解码的操作，如`SkImageDecoder`、`SkImageEncoder`等。这些类和函数也定义在`include/core`目录下的相应头文件中。





1. [Skia图形引擎解析](https://juejin.cn/post/6844903618537080845)
2. [浅谈Skia图形引擎](https://www.jianshu.com/p/768dd82d2ea0)
3. [Skia图形引擎初探](https://zhuanlan.zhihu.com/p/72633120)
4. [Skia绘图引擎初探](https://blog.csdn.net/weixin_30564577/article/details/101651068)
5. [Skia图形引擎综述](https://blog.csdn.net/goldenfish1919/article/details/107953802)
6. [浅谈Skia图形库及其在Flutter中的应用](https://segmentfault.com/a/1190000019883191)
7. [Flutter图形渲染引擎Skia解析](https://www.jianshu.com/p/3fd1a0b8ff6e)
8. [Skia图形引擎初探](https://blog.csdn.net/chenqinghuai/article/details/79730794)
9. [Skia和SkiaSharp的区别](https://blog.csdn.net/sxf1061926959/article/details/117187381)
10. [跨平台绘图引擎Skia](https://blog.csdn.net/a1954620059/article/details/118091453)



1. [Inside Skia: Building a Cross-Platform 2D Graphics Library](https://medium.com/@brendan_duncan/inside-skia-building-a-cross-platform-2d-graphics-library-aa8d1a5b4736)
2. [Introduction to Skia Graphics Library](https://medium.com/@prohorovaolga/introduction-to-skia-graphics-library-91b19c53f60)
3. [Understanding Skia: The Graphics Engine Behind Flutter](https://levelup.gitconnected.com/understanding-skia-the-graphics-engine-behind-flutter-1a093bc2bdf1)
4. [An Introduction to SkiaSharp](https://www.michielpost.nl/Blog/an-introduction-to-skiasharp)
5. [Skia Graphics Engine: A Journey from SkCanvas to SkImage](https://medium.com/@shaktiprasadsk/skia-graphics-engine-a-journey-from-skcanvas-to-skimage-6e3704422d1e)
6. [Skia Graphics Library: High-Performance 2D Graphics on the Web](https://spin.atomicobject.com/2020/07/22/skia-graphics-library/)
7. [Introduction to Skia Graphics Library](https://martinmitrevski.com/2021/04/10/introduction-to-skia-graphics-library/)
8. [Introduction to Skia Graphics Library with C++](https://deepai.org/machine-learning-model/introduction-to-skia-graphics-library-with-c)
9. [Rendering a UI with SkiaSharp](https://medium.com/@denismigol/rendering-a-ui-with-skiasharp-4b6a75411d88)
10. [Exploring SkiaSharp: A .NET Wrapper for the Skia Graphics Library](https://nicksnettravels.builttoroam.com/exploring-skiasharp-a-net-wrapper-for-the-skia-graphics-library/)



中文：

1. Skia图形库介绍与剖析
2. 深入解析Skia图形引擎
3. Skia源码解析与应用实践
4. Skia图形渲染引擎原理解析
5. Skia图形库架构与设计解析
6. Skia渲染引擎剖析与优化实践
7. Skia图形引擎渲染流程解析
8. Skia图形库绘图原理与性能优化
9. Skia渲染引擎在移动端应用中的实践
10. Skia图形引擎在游戏开发中的应用分析

英文：

1. Inside Skia: A Deep Dive into Skia Graphics Library
2. Exploring Skia: Understanding the Graphics Engine
3. Skia Graphics Engine: Architecture and Design Overview
4. Skia Rendering Pipeline: A Comprehensive Analysis
5. Skia Graphics Library Internals: A Closer Look
6. Skia Graphics Engine: Rendering Performance Optimization Techniques
7. Skia Graphics Engine: A Detailed Walkthrough
8. Skia Graphics Library: Drawing Principles and Best Practices
9. Skia Graphics Engine in Mobile Application Development: Insights and Strategies
10. Skia Graphics Engine in Game Development: Analysis and Applications

Skia-discuss Google Group: https://groups.google.com/g/skia-discuss
