## 图形渲染管线
### 概念
图形渲染管线指的是对一些原始数据经过一系列的处理变换并最终把这些数据输出到屏幕上的整个过程。
### 过程
图形渲染管线的整个处理流程可以被划分为几个阶段，上一个阶段的输出数据作为下一个阶段的输入数据，是一个串行的，面向过程的执行过程。每一个阶段分别在GPU上运行各自的数据处理程序，这个程序就是着色器。
部分着色器允许我们使用着色语言编写自定义的着色器，这样就可以更为细致的控制图像渲染流程中的特定处理过程了，下图是一个图形渲染管线每一个阶段的抽象表示，蓝色部分代表允许自定义着色器。

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210727182112.png)

``顶点数据``是一些顶点的集合，顶点一般是3维的点坐标组成。
``基本图元``（Primitives）包括点，线段，三角形等，是构成实体模型的基本单位，需要在传入顶点数据的同时通知OpenGL这些顶点数据要组成的基本图元类型。
``顶点着色器``（Vertex Shader）包含对一些顶点属性（数据）的基本处理。
``基本图元装配``（Primitive Assembly）把所有输入的顶点数据作为输入，输出制定的基本图元。
``几何着色器``（Geometry Shader）把基本图元形式的顶点的集合作为输入，可以通过产生新顶点构造出新的（或是其他的）基本图元来生成其他形状。
``细分着色器``（Tessellation Shaders）可以把基本图元细分为更多的基本图形，创建出更加平滑的视觉效果。
``光栅化``（Rasterization）即像素化，把细分着色器输出的基本图形映射为屏幕上网格的像素点，生成供片段着色器处理的片段（Fragment），光栅化包含一个剪裁操作，会舍弃超出定义的视窗之外的像素。
``片段着色器``（Fragment Shader）的主要作用是计算出每一个像素点最终的颜色，通常片段着色器会包含3D场景的一些额外的数据，如光线，阴影等。
``测试与混合``是对每个像素点进行深度测试，Alpha测试等测试并进行颜色混合的操作，这些测试与混合操作决定了屏幕视窗上每个像素点最终的颜色以及透明度。

在整个渲染管线中需要自定义处理的主要是顶点着色器和片段着色器。 

## 重要概念
- VBO ： Vertex Buffer Object，顶点缓冲对象。
顶点缓冲对象VBO是在显卡存储空间中开辟出的一块内存缓存区，用于存储顶点的各类属性信息，如顶点坐标，顶点法向量，顶点颜色数据等。在渲染时，可以直接从VBO中取出顶点的各类属性数据，由于VBO在显存而不是在内存中，不需要从CPU传输数据，处理效率更高。

所以可以理解为VBO就是显存中的一个存储区域，可以保持大量的顶点属性信息。并且可以开辟很多个VBO，每个VBO在OpenGL中有它的唯一标识ID，这个ID对应着具体的VBO的显存地址，通过这个ID可以对特定的VBO内的数据进行存取操作。 

- VAO ： Vertex Array Object，顶点数组对象。
如果仅仅靠VBO把一堆顶点交付给渲染管线，可是却不告诉渲染管线哪部分是位置坐标哪部分是纹理坐标那么肯定也无法渲染出任何东西，此时VAO就出现了。
VAO是对顶点的结构的抽象描述，它告诉渲染管线哪部分是位置哪部分是颜色，以便渲染管线中的着色器去做相应的着色。VAO相当于是对很多个VBO的引用，把一些VBO组合在一起作为一个对象统一管理。

- IBO：Index Buffer Object，索引缓冲对象。
有些文章也称其为EBO，Element Buffer Object。这样命名不太直观，这里我使用IBO作为术语。
顾名思义IBO是创建了一个索引缓存，使用索引缓存可以实现顶点复用，减少内存空间浪费，提高执行效率。当需要使用重复的顶点时，通过顶点的位置索引来调用顶点，而不是对重复的顶点信息重复记录，重复调用。EBO中存储的内容就是顶点位置的索引indices，EBO跟VBO类似，也是在显存中的一块内存缓冲器，只不过EBO保存的是顶点的索引。
以立方体为例，一般来讲我们使用三角形作为基本图元，一个立方体6个面12个三角形，需要36个顶点来描述。可是我们都知道立方体有8个顶点，36个顶点定义了大量的重复的顶点。通知渲染管线每个顶点的编号实现顶点复用就是IBO的作用。

参考文档：[OpenGL图形渲染管线、VBO、VAO、EBO概念及用例](https://blog.csdn.net/dcrmg/article/details/53556664)