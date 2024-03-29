---
title: 晨光夕忆 ——— 基于3DSMax以及Arduino的自动化绷带包扎机
date: 2022.9.17
updated: 2022.9.17
tags: 
- 新活
- 3DSMax
- Arduino
- 科创
categories: 
- 技术向
keywords:  
- 编程 
- 单片机
description: take care of yourself
toc: true
toc_number: false
top_img: https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209181200621.png
cover: https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209181200620.png
---

那么一如既往的，这也是老文一篇。高一高二的时候我曾加入过学校的物理科技创新社，并协助其中成员参加了科创大赛。但是不要误会，这篇论文不是我写的，而是由主创herobrine 亲自撰写，所以啊，我是来《锐评》的。

> 那么这就是我锐评的形式

---



<center>
    <h3>
        以下正文
        <br>
        <br>
        <br>
    </h3>
</center>

<center>
    <h1>
        基于3DSMAX以及Arduino的自动化绷带包扎机
    </h1>
</center>



<center>
    <h3>
        摘		要
    </h3>
</center>

**近年来，随着科技的发展，日常生活中受伤的频率不断提高。**因而出现了各式的止血装置，例如野战用压力止血带。但是现有的止血装置基本不具备包扎的能力，且止血力度无法很好地控制，不便于日常生活中使用。同时市面上也没有具有包扎功能的设备，能对已经止血的肢体进行绷带包扎.

> 真是开幕第一句话惊天地，泣鬼神！论文在一开始便以这么一句话向全人类文明发出了深刻的批判：科技的发展会给我们的生活带来什么样的悲惨后果？我们不禁想到，科技一方面造福于人类，另一方面又反噬于人类，科技的发展使得我们越来越容易被日常生活中的一切万物所伤害，真是令人胆战心惊。为了解答这个时代命题，herobrine，一位年纪轻轻却胸怀大志的青年，大胆的在开幕抛出了这个问题，我们不得不佩服他的智慧与眼界......

于是，我们提出并设计了此种自动化绷带包扎机（以下简称**绷带机**），对已止血的肢体进行包扎，可以配合止血带使用，实现止血包扎的护理过程。而且，为了方便家庭日常使用，提出了自动包扎的功能，以便一个人能在无人协助的情况下，能自动进行止血包扎的护理过程。并且，基于其包扎的功能，也可以在平时的扭伤或者其他情况下，进行伤口敷药并包扎的简便处理。

本项目目前已初步实现了能在无人协助的情况下进行自动包扎的功能，可以应用于人们的日常生活。

   **关键词：**无人协助  自动化绷带包扎

---



<center>
    <h3>
        目		录
    </h3>
</center>


<center>
    pc端网页右边有目录，移动端在右下第二个按钮里有目录，这里就省略了
</center>



---

### 1. 前言

#### 1.1  研究背景及意义

**随着科技的发展，日常生活中受伤的频率不断提升**。而受伤后若要进行肢体的止血包扎等护理过程，传统的止血方式繁琐且需要多人合作，止血的力度也不易控制。目前市面上的止血护理装置基本只具备止血功能，不具备包扎功能；护理时也需要其他人协助，无法在无人协助的情况下进行绷带包扎的过程，即使有多人协助，不仅工作量大同时过程也是繁琐不易的。目前国内也没有能自动化包扎的机器。

> 他觉得说的不好，于是又说了一次



综上，面对国内所存在的条件不足，于是就针对目前存在的不足以及对已有的想法进行开发，提出了以下的绷带机。**配合市售止血带的使用，先进行止血的伤口处理，再使用本自动包扎机，可以自动将绷带缠绕在手上**，且不需要其他人的协作就能完成这一包扎过程。

> 有一说一，建议绷带机加一个止血带的功能



#### 1.2 绷带包扎可出现的问题

当个人开始使用绷带包扎时，会出现的问题如下一览：

① 在绷带开始包扎之前，绷带的一端无法很好的固定于肢体上，若无人协助时，则会导致绷带无法有力的缠绕于肢体上，导致绷带包扎不紧、易滑脱的问题。

② 绷带的缠绕没有固定的力度，且对于非专业人士来说，绷带的力度无法很好的控制，以至于绷带包扎时可能会导致伤口二次受伤、绷带缠绕过厚或过薄导致的透气问题或者易滑脱的问题。

因此，对于个人绷带包扎的以上问题，设计了以下机械结构解决以上问题。

> 事实上，后面绷带机也出现了这些问题，而且比真人更加严重......

---



### 2. 研究内容

#### 2.1 机械结构

本机体主要是通过一系列机械结构来实现自动缠绕绷带并且能无人协助使用.

机体的总构图如下图所示：

![image-20220918203857864](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182038122.png)

> 接下来你会看见一大堆A点，B点，C点的结构描述，看不懂没关系，因为herobrine后面自己都有点搞混了（



##### 2.1.1 绷带缠绕模块

本结构使用大齿轮作为旋转框架，齿轮上附有绷带固定轴A点。动力皆使用360度舵机。
结构图如下图所示：

![image-20220918115211088](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209181152130.png)

A点作为绷带固定轴，可以达到绷带固定及缠绕的目的。
①	将此元件固定于大齿轮上，将360度舵机安装在一小齿轮上。当舵机开始转动时，会带动元件A点转动。若将绷带固定于元件A点上，当齿轮开始旋转时，即可将绷带缠绕于手上。
②	当需要包扎的部位较长时，则会用到42步进电机及伸缩丝杆螺旋模块。将整体机器的一部分固定于伸缩丝杆的轴套上，并在机器底部安装轮子。当伸缩丝杆开始运动时，即可带动整体机器模块在前后的方向上运动。此时若绷带开始缠绕，则能在肢体上缠绕出一段较长的距离。若要控制缠绕厚度，则改变伸缩丝杆的运动速度即可。

##### 2.1.2 无人包扎自动协助模块 

B点作为无人包扎自动协助模块，能够在绷带缠绕之始，临时固顶绷带的游离端，以便施力及之后的缠绕过程。这是无人协助自动包扎的关键。
结构图如下所示:

![image-20220918204137286](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182041323.png)

① 将B点固定于大齿轮2上，并通过将舵机固定于小齿轮上的方式，通过舵机来控制大   			 齿轮的旋转，从而带动大齿轮上的元B点运动。

②B点各零件详解：

```
前端的带凹槽的长杆：在绷带缠绕之前，将绷带游离端缠绕于长杆上，即可实现在绷带缠绕之前固定绷带的游离端，使绷带缠绕时不至于无法施力。长杆上的凹槽与绷带的缠绕方向垂直，为了增大摩擦，防止滑落。

长杆后端的齿条：用于控制长杆的伸缩，目的是为了在临时固定绷带游离端，绷带的缠绕能够稳定进行之后能够抽掉长杆，以至于长杆不会阻碍到绷带的缠绕。

齿条上的齿轮：连接一个小型360度舵机，将其固定于齿条上方若干厘米高，让齿轮和齿条能够恰好吻合。目的是为了让齿轮能够控制齿条的伸缩

齿条上连接的压板：固定于齿条上并连接一180度舵机，使用舵机来控制压板的运动，目的是为了能将长杆上的绷带游离端压住不会滑落，进一步防止了绷带的滑脱。

支架：为了固定以上的结构。并且，为了节约成本，采用三角形镂空结构，基本不影响支撑强度，并且能极大的节约材料，减少成本。
```





##### 2.1.3 其他供电及控制模块

​		控制以上的模块的一切为主控制板ArduinoMega2560。

​		由于主控制板ArduinoMega2560固定于底板上而以上所有的结构都必须在旋转的齿轮上运转，因此给齿轮上的舵机供电及传输讯号成了问题。

​		因此为了解决以上问题，所有齿轮上的结构都会使用齿轮上的电池槽所带的两节18650电池供电。在传输讯号上，则使用全碳电刷配合镍带条进行讯号传输。传输具体方式为：

​		将镍带条贴于齿轮上预留好的两条凸起上，将全碳电刷固定于支架上，让其能与镍带条接触。当齿轮旋转时，电刷持续在镍带条上滑动，保持讯号连线稳定，时刻都能与主控制板传输讯号。

​		由于无人包扎自动协助模块上连接两个舵机总共需要3条电刷导轨线，分别为舵机讯号线1、舵机讯号线2以及电池负极GND共地线，而预留出来的电刷导轨线只有两条，因此舵机讯号线使用一条线路，电池负极GND共地线使用一条线路。舵机讯号线使用同一条线路为了防止其互相干扰，则在其线路末端安装一个电磁继电器，使用Arduino串口通入高低电平来决定连接的是哪一个舵机。

供电部分至此结束。

> 还记得贴镍带条的痛苦，以及电刷实际上不太稳定，另外，我还记得好像是步进电机还是什么的，成功的把实验室大电脑USB口电涌烧坏了

---



#### 2.2 整体运作过程

① 包扎之前先让无人包扎自动协助模块中的齿条带动长杆伸出，与A点的绷带的固定轴平行。

② 在绷带包扎之前，先将绷带置于A点上，拉出其绷带游离端，缠绕于无人包扎自动协助模块的长杆上，令其压板压住绷带游离端，不让其滑脱。

③ 开始缠绕:控制舵机带动齿轮转动，让齿轮上的A点随齿轮转动而转动，让绷带开始缠绕于肢体上。此时步进电机不要运动，让A点原地旋转两圈，待到绷带能够固定于肢体上之后，让无人包扎自动协助模块上的小型舵机控制压板松开，并让另一个小舵机控制齿条收回，带动长杆收回。这时绷带已能固定于肢体上不滑落，此时无人包扎自动协助模块的使命基本结束。

④ A点继续转动，带动绷带缠绕于肢体上，此时步进电机带动丝杆轴套前进，从而带动所有组件向前运动。这时即可实现纵向的包扎，让包扎的长度可以增加。此时改变步进电机的运转速度，即可改变单位时间内缠绕在一定长度内的绷带圈数，从而改变绷带缠绕的厚度。

⑤ 当包扎完成之后，A点停止转动并锁死。这时只要自行用切割工具剪下绷带，贴上备好的透气胶带，即可将手抽出，包扎过程完毕。

![image-20220918205218204](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182052390.png)

> 以前还做了动图的，可惜丢了，不过看上图差不多能知道是怎么工作的。

---

### 3. 研究重点

#### 3.1主要创新点

​	本产品创新之处为：

① 提出了外旋转缠绕模式
     根据已有的缠绕装置来看，目前所存在的所有缠绕装置基本为缠绕器固定，而被缠绕体旋转，这种被称为内旋转缠绕模式。而本装置采用外旋转缠绕模式，即被缠绕体固定，而缠绕装置旋转。由于被包扎的肢体不能旋转，因此此种模式可应用于伤口包扎。

![image-20220918210054111](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182100178.png)

> 是在吃早餐的时候herobrine脑洞一开想出来的，工业朋克风满满的~



② 自主创新设计了此种协助包扎模块，即B点。

> 考考读到这里的你:还记得A点是啥吗?



#### 3.2 装置制作

本装置由于零件多且结构复杂，使用传统的木制手作无法实现其功能。因此，使用3Ds Max 2020设计并采用3D打印制作一系列零件和模块。成本低廉，并且能自行修改参数。除去步进电机丝杆等需要高精的装置之外，其他部件皆使用3d打印制作。
动力装置基本上使用舵机以及步进电机，由于有现成的函数库，因此可以实现代码简洁的优点。再之，舵机以及步进电机可控制角度以及运转速度和时间，因此可以达到精准控制装置运动和旋转的优点，实现以上提到的功能。

> 虽然但是，一卷打印材料200块钱，小实验室打印不了那么大的齿轮，最后外包去了



---

### 4．不足与展望

#### 4.1 不足之处

​	目前由于经费与时间的关系，无法很好的**实现以下的不足之处**

​    ① 绷带的缠绕不能很好的控制缠绕的力度。

​    ② 绷带包扎完成后需要人为的剪短绷带，并且需要人为粘贴透气胶带。

> "实现不足之处", 为什么要实现不足之处啊？理解不能。



#### 4.2 未来展望解决方案

##### 4.2.1关于绷带缠绕不能很好的控制力度，提出了以下的模型

![image-20220918210023008](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182100050.png)

​	如图所示，在A点安装一个弹簧，并在A点上安装两个凸起点，用于连接两根尼龙细线。在尼龙细线的末端安装一个180度舵机，使用舵机牵拉尼龙细线，使得A点能够被压下。
​	当A点被压下时，绷带轴与肢体之间的距离变短，使得绷带拉伸。此时绷带就能有更强的缠绕力度。
​	通过改变舵机的偏转角度，改变A点被压下的长度，既能改变绷带的缠绕力度，实现力度控制。

###### 4.2.1.1 关于力度控制参数的来源

![image-20220918210304443](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182103482.png)

​	如上图，在装置的两齿轮之间安装一根齿条，并且安置一个齿轮舵机组合，通过控制舵机的旋转来控制齿轮在齿条上的移动。在齿轮舵机组合上安装一根长杆，使得长杆的顶端恰好和肢体的中心保持统一高度。在长杆末端安装一个微触开关。
​	使用本设备前，先将肢体伸入两齿轮的中间，然后启动舵机，让舵机带动长干匀速前进，直到微触开关触碰到肢体为止。当微触开关触碰到肢体，即给主控制板Arduino通一个高电平，收到高电平后，舵机停止运动，此时通过舵机的运动时间呈上舵机运动的速度得出运动的距离。再用大齿轮内半径减去舵机运动距离得出肢体的半径。
​	通过一系列实验可得出最佳的肢体缠绕力度与肢体半径的关系。

> 吹牛大家都会

##### 4.2.2 关于无法剪断带并粘贴胶带的改进

###### 4.2.2.1 剪断绷带模块

​	在大齿轮2上安装第二个B点，位于原本B点的90度方向

![image-20220918210431742](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182104788.png)

​	如图所示，将原本的带凹槽的长杆替换为**刀片**，并且删除压板及其传动装置。
​	使用方式：在结束缠绕之后，将刀片伸出并与绷带保持同一竖直位置。此时以绷带缠绕的逆方向旋转刀片，即可切断绷带。或者将刀片改为**高能激光发射头，使用高能激光束切断绷带**。

> 全篇最高潮一句话出现了!出现了！
>
> 我们可以预见，一位手臂已经受伤，忍着伤痛前来使用绷带机的伤者，他把手放上去，启动绷带机，于是长痛不如短痛，绷带机热心的帮他直接用刀片or高能激光束给他的手臂做了个托马斯超级大回旋，杨过附体！
>
> 好像也可以性转(?)
>
> 另外，这个想法在校内答辩时一经提出，语惊四座，把指导老师都绷的说不出话来



###### 4.2.2.2 胶带粘贴装置

在大齿轮1前方安装下图装置

![image-20220918210929167](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182109210.png)

​		如图所示，本装置两端有两根齿条，齿条上安有齿轮舵机组合，通过控制舵机来控制齿轮的运动。在舵机齿轮组合安装两个牵拉夹，用两个牵拉夹分别夹住胶带的两端。然后让两个舵机同时往下运动，让胶带贴在肢体上。当胶带受到一定的力时会自行从牵拉夹中断开。从而实现自动胶带粘贴的目的。

---

### 5. 研究历程

2019.5.1	本项目于2019年5月1日正式立项，是由于观察到受伤的舍友在自行给扭伤的部位包扎消炎止痛膏时的繁琐与不便所启发，因而想要开发一款能够自行包扎患部的机器。
先是对已有的自动化护理仪器进行了调研，发现现有的仪器大多只有止血功能，并无包扎功能，因而更加坚定地想要开发一款自动化包扎的机器。

2019.5.30   开始设计这款机器，使用3DSMAX软件进行建模，并运用3D打印技术，打印零部件。

2019.6.30   便决定使用齿轮传动的形式作为机器的主要部件。

2019.10.1   所有的零件皆已经设计完毕，并打印完毕。在这3个半月以来，经历了多次模型重构，最终决定了此种机械结构。

在2019年10月至今，所有动力设施皆已搭建完善，并将控制代码构建完毕，正在进一步调试。



<center><figure><img src="https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182135049.png" />···<img src="https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/2/202209182136814.png" /></figure></center>





> 论文到此结束。
>
> 现在看来，这是只属于高中时代的浪漫吧。半年的时间，herobrine能够做出这个东西，最后还能拿省二，属实是非常牛逼了。如果用专业知识来看，这个东西问题非常多，力学结构（特别是用齿轮做传动的同时还起支撑作用），电路设计都毛病不少，但是，最让我们感到兴奋的一瞬间，就是它真的动起来的那一刻。