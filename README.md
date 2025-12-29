# OpenGL Learning: FirstForm (Hello Triangle) 

这是一个基于 **OpenGL 3.3 Core Profile** 的基础渲染程序，使用 C++ 编写。该项目展示了如何创建一个窗口、编译着色器（Shader）、配置顶点数据（VAO/VBO）并绘制一个简单的白色三角形。

> **Created By:** Easy
> **Date:** 2025.12.18

##  项目简介

该程序是 OpenGL 学习过程中的里程碑（Hello World），主要实现了以下功能：
1.  **GLFW 窗口管理**：创建并管理一个 800x600 的渲染窗口。
2.  **GLAD 加载**：初始化 OpenGL 函数指针。
3.  **渲染管线配置**：
    *   编写并动态编译 **顶点着色器 (Vertex Shader)**。
    *   编写并动态编译 **片段着色器 (Fragment Shader)**。
    *   链接 **着色器程序 (Shader Program)**。
4.  **数据管理**：
    *   使用 **VBO (Vertex Buffer Object)** 将顶点数据发送至显存。
    *   使用 **VAO (Vertex Array Object)** 记录顶点属性状态。
5.  **交互功能**：支持 `ESC` 键退出，支持窗口大小拖拽调整。

##  依赖库 (Dependencies)

在编译此项目前，请确保你的开发环境已经配置了以下库：

*   **[GLFW](https://www.glfw.org/)** (3.3+)
    *   用于创建窗口、处理输入和管理上下文。
*   **[GLAD](https://glad.dav1d.de/)**
    *   用于管理 OpenGL 的函数指针（需选择 OpenGL 3.3 Core 模式）。

##  代码核心逻辑解析

### 1. 渲染流程 (The Rendering Loop)
程序的核心在于 `while` 循环，每一帧都遵循严格的 **“状态机”** 逻辑：
```cpp
// 1. 激活着色器程序
glUseProgram(shaderProgram);

// 2. 绑定 VAO (拿出这一帧要用的数据档案袋)
glBindVertexArray(VAO);

// 3. 执行绘制命令
Render(); // 内部调用 glDrawArrays

// 4. 解绑 VAO (保持良好的状态管理习惯)
glBindVertexArray(0);
```

### 2. 顶点数据 (Vertex Data)
定义了一个等边三角形的三个顶点坐标（标准化设备坐标 NDC）：
*   左下角: `(-0.5, -0.5, 0.0)`
*   右下角: `( 0.5, -0.5, 0.0)`
*   正上方: `( 0.0,  0.5, 0.0)`

### 3. 着色器 (Shaders)
*   **Vertex Shader**: 接收位置数据 `location=0`，直接输出。
*   **Fragment Shader**: 输出固定的白色 `vec4(1.0f, 1.0f, 1.0f, 0.5f)`。

##  注意事项 (Notes)

*   **窗口回调与 VAO**：在调整窗口大小时，`size_callback` 函数会触发重绘。由于 `VAO` 在代码中定义在 `main` 函数内（局部变量），如果希望在拖拽窗口时三角形不消失，建议将 `VAO` 提升为 **全局变量**，并在 `size_callback` 中添加绑定逻辑。
*   **背景色**：默认背景色设置为深蓝色 (`0.2f, 0.4f, 0.8f`) 以便清晰观察白色三角形。

---
*Keep Coding, Keep Learning!* 

```C
#pragma region 完整思路
/*
    本项目在窗口中绘制了一个三角形。
    关键概念在于顶点数组对象VAO、顶点缓冲对象VBO、顶点着色器VertexShader、片段着色器FragmentShader。
    这次感觉写不到所谓的1.2.3.4，就紧着这几个概念说吧
    VertexShader的目的是为了将输入到显卡的坐标转换为标准化坐标：（-1-1）；
    FragmentShader主要是为了给绘制图形渲染颜色；
    VS和FS均是用OpenGL着色器语言GLSL编写的，而非C++，因此在开始定义一个着色器指针char来编写着色器；
    再在主程序中用glCompliShader函数将其编译，同时C++并不会报错GLSL，因此还需要写一个if语句显示报错信息；
    顶点缓冲对象VBO，设计出来的目的是为了一次性向显卡发送绘制数据，如果同步发送则会导致CPU效率过低；
    顶点数组对象VAO，可以将其看作一个录音机，它把VBO所干的事包括发给显卡数据glBufferData、显卡读取数据glVertxAttriPointer、打开几号通道glEnableVertexAttriArray，全部都记录下来以便在绘制中直接调用VAO即可，不用再次复写一大段VBO代码；
    有一个bug，就是在改变窗体大小的时候无法绘制出三角形。这个原因其实也简单，因为在创建窗体的代码里我把背景色渲染代码都放在Render里了，然后while循环调用这个Render绘制，主要是为了回调函数不用再写一遍绘制渲染代码，然后导致回调函数里面记录不到VA0记录的VBO，所以改变窗体大小时候没法渲染出三角形。解决方法也简单，就是把VAO放到全局变量里面，再把VAO传进回调函数里，不然就得再在callback里面传个参了。
    但是交给后人的智慧解决吧。
    还有个搞笑的点是：最初我还不懂VAO的用法，没写VAO的结束语句，倒是可以画出这个三角形了😂
*/
#pragma endregion
```