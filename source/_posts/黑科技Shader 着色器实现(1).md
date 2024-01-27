---
title: 黑科技Shader着色器实现（一） 冲击波纹扩散效果
date: 2022.9.13
updated: 2022.9.13
tags: 
- 新活
- lua
- shader
categories: 
- 技术向
keywords:  
- 编程 
- 着色器
description: 老文一篇
top_img: https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/1202209131047065.png
cover: https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/1202209131047064.png
---

Shader又名渲染着色器，是电脑GPU流水线上一些可以高度编程的阶段，即可以在GPU上运行的代码。

Shader有一些特定类型的着色器。（如顶点着色器、片元着色器等），依靠着色器我们可以控制渲染流水线中的渲染细节，例如用顶点着色器（Vertex）来进行顶点变换以及传递数据，用片元着色器（Pixel）来进行逐像素渲染。

简单来说，shader就是对一张纹理图片或一个物理模型的【人形tas，单帧操作】，接下来我们来实现以下冲击波特效的效果。

![](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/1/202209131050551.gif)

 看到这个效果，我们发现：

1.   这个效果的实现只需要操作pixel着色器
2.   实现效果的核心是像素偏移

那么什么是像素偏移呢？简单来说，就是让图片移动位置，但是shader可以实现像素级的操作。



---



![](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/1/202209131050552.png)



我们可以把冲击波看成是一个圆环的扩散扭曲，因此我们可以定义圆环的半径和宽度。

``` glsl
sampler2D Texture0;                 //定义图片纹理名

float2 center=float2(0.5,0.5);      //效果中心坐标

float radius;                       //圆环半径
float width;                        //圆环宽度

float PI=3.1415926535897;

float fViewportWidth;               //窗口宽度
float fViewportHeight;              //窗口高度
```



同时，我们可以用一个aspect来使我们渲染的圆环保持正圆，而不会被窗口比例拉升

``` glsl
float aspect=fViewportWidth/fViewportHeight;

 				  //aspect的作用是对圆进行修正，保证在不同的分辨率下都为正圆
```



定义一个dir，获得从效果坐标到当前像素的向量
再定义一个edgeWidth，获得从当前像素到圆环的距离

``` glsl
float2 dir = center - uv;  
                    //uv为屏幕贴图当前UV坐标

float edgeWidth = length(float2(dir.x * aspect, dir.y)) - radius;   
                    //当前点离环形中边（以内外圆半径平均值为半径的圆）的距离
```



有了这些，我们就获得了一个可控制半径，宽度，拉升比例的圆环
接下来要做的就是扭曲圆环内的像素了

``` glsl
float sinX =  width +edgeWidth;
                    //计算uv坐标偏移 

float2 offsetUV = dir            
                    //偏移量 = 偏移方向 

float2 resultUV = lerp(uv, uv + offsetUV, step(abs(edgeWidth) >width, 0.5));
                    //控制扭曲范围，只有在圆环内的像素才会被扭曲
                    //再用线性插值拟合函数曲线

float4 getpic=tex2D(Texture0, resultUV );

return getpic;		//输出扭曲后的图像
```



---



![](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/1/202209131050553.png)



但是，我们可以很明显的看到，扭曲范围与原图之间有明显的切割迹象，体现不出波动的效果，为了解决这个问题，我们可以用sin函数来实现扭曲与原图的自然过渡。

``` glsl
float weight = 1 *width*sin((0.5*PI/width)*sinX); 
                        //weight为扭曲权重，正弦函数： sin(2π/4d x)

float2 offsetUV =dir * weight;            
                        //偏移量 = 偏移方向 * 偏移权重 
```



这样一来，我们的波动效果就完成啦！

我们还可以让半径随时间变大，实现动态效果

![](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/1/202209131050551.gif)

