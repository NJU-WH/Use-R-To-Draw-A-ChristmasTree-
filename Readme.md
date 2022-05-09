**博客文章地址：https://nju-wh.github.io/2022/05/09/UseRDrawChritmasTree/**

# 简介

通过**R语言**的**ggplot2**包及**ggThemeAssist**包根据预先导入的数据绘制圣诞树

# 画图步骤

## Step 1：绘制一颗基础的树

这里选择一棵圣诞树的图片，对其进行网格划分，并将结果存在表格中。

数据集和源码均可在GitHub仓库中获取：https://github.com/NJU-WH/Use-R-To-Draw-A-ChristmasTree-

```R
#将数据导入项目中，路径根据自己保存的位置进行调整
ChristmasTree <- read.csv(".......\\Christmas_tree.csv")

#载入ggplot2程揖包
#若未提前安装程揖包需在console窗口执行以下指令
#install.package("ggplot2")
library(ggplot2)

#通过ggplot2包进行绘制
tree <- ggplot() + 
  geom_tile(data = ChristmasTree, aes(x = Tree.X, y = Tree.Y, fill = Tree.Colour)) +       
  scale_fill_identity() + 
  theme_bw() +
  scale_x_continuous(breaks = NULL) + 
  scale_y_continuous(breaks = NULL) +
  labs(x = "", y = "")
tree

```

**参数解释**

**geom_tile()** ：该函数将每个数据点都绘制成一个实心的正方形。

**theme_bw()**：该函数将背景颜色更改为白色。

**scale_x_continuous(breaks = NULL)**：该函数将x轴标记和网格线去掉。

**scale_y_continuxous(breaks = NULL)**：该函数将y轴标记和网格线去掉。

**labs(x = "", y = "")**：该函数将坐标轴删去。

**运行后效果如下：**

![image-20220509124711419](https://gitee.com/njuwh/img-repository/raw/master/img/rchristmas.jpg)

## Step 2：添加灯饰

现在就来往圣诞树上挂“灯饰”，这一过程通过在之前绘制好的基础树图上叠加散点实现。这些散点的坐标是基于均匀分布随机产生的。

示例代码中给灯设置的数量是 50 ，底部分布的数量为 35% ，顶部为 5% ，参数值可以根据个人想法修改。

最后一个变量的值从1到4，用以控制每个点的透明度。

```R
##创建灯饰
Desired.Lights <- 50
Total.Lights <- sum(round(Desired.Lights * 0.35) + round(Desired.Lights * 0.20) + 
                      round(Desired.Lights * 0.17) + round(Desired.Lights * 0.13) +
                      round(Desired.Lights * 0.10) + round(Desired.Lights * 0.05))

Lights <- data.frame(Lights.X = c(round(runif(round(Desired.Lights * 0.35), 4, 18), 0),
                                       round(runif(round(Desired.Lights * 0.20), 5, 17), 0),
                                       round(runif(round(Desired.Lights * 0.17), 6, 16), 0),
                                       round(runif(round(Desired.Lights * 0.13), 7, 15), 0),
                                       round(runif(round(Desired.Lights * 0.10), 8, 14), 0),
                                       round(runif(round(Desired.Lights * 0.05), 10, 12), 0)))
Lights$Lights.Y <- c(round(runif(round(Desired.Lights * 0.35), 4, 6), 0),
                          round(runif(round(Desired.Lights * 0.20), 7, 8), 0),
                          round(runif(round(Desired.Lights * 0.17), 9, 10), 0),
                          round(runif(round(Desired.Lights * 0.13), 11, 12), 0),
                          round(runif(round(Desired.Lights * 0.10), 13, 14), 0),
                          round(runif(round(Desired.Lights * 0.05), 15, 17), 0))
Lights$Lights.Colour <- c(round(runif(Total.Lights, 1, 4), 0))
##将灯添加到树上
tree <- tree +
          geom_point(data = Lights, aes(x = Lights.X, y = Lights.Y, alpha = Lights.Colour),
                     colour = "lightgoldenrodyellow", shape = 16) +
          theme(legend.position = "none")
tree
```

**运行后效果如下：**

![image-20220509125310326](https://gitee.com/njuwh/img-repository/raw/master/img/rchristmas-1.jpg)

## Step 3：添加装饰物

在第二步的图上添加了另一种散点(这一次是加权散点)。手动绘制了每个点的坐标，并手动分配每个点的颜色和大小。也可以任意修改这些位置和大小。

```R
##创建装饰物
Baubles <- data.frame(Bauble.X = c(6, 9, 15, 17, 5, 13, 16, 7, 10, 14, 7, 9, 11, 
                                   14, 8, 14, 9, 12, 11, 12, 14, 11, 17, 10))
Baubles$Bauble.Y <- c(4, 5, 4, 4, 5, 5, 5, 6, 6, 6, 8, 8, 8, 8, 10,
                      10, 11, 11, 12, 13, 10, 16, 7, 14)
Baubles$Bauble.Colour <- factor(c(1, 2, 2, 3, 2, 3, 1, 3, 1, 1, 1, 2, 1, 2,
                                  3, 3, 2, 1, 3, 2, 1, 3, 3, 1))
Baubles$Bauble.Size <- c(1, 3, 1, 1, 2, 1, 2, 2, 2, 1, 1, 1, 3, 3, 3,
                         2, 3, 1, 1, 2, 2, 3, 3, 2)
                         
##将装饰物添加到树上
tree <- tree + 
          geom_point(data = Baubles, aes(x = Bauble.X, y = Bauble.Y, 
                                         colour = Bauble.Colour, size = Bauble.Size),
                     shape = 16) +
          scale_colour_manual(values = c("firebrick2", "gold", "dodgerblue3")) +
          scale_size_area(max_size = 12)
tree
```

**运行后效果如下：**

![image-20220509125631877](https://gitee.com/njuwh/img-repository/raw/master/img/rchristmas-2.jpg)

## Step 4：装饰礼物

树下的礼物看起来太单调了，可以用缎带来装饰一下，使用 geom_segment() 函数来实现这一点。

```R
tree <- tree +
  geom_segment(aes(x = 2.5, xend = 4.5, y = 1.5, yend = 1.5), colour = "lightgrey", size = 2) + 
  geom_segment(aes(x = 5.5, xend = 8.5, y = 1.5, yend = 1.5), colour = "gold", size = 2) +
  geom_segment(aes(x = 13.5, xend = 16.5, y = 1.5, yend = 1.5), colour = "black", size = 2) +
  geom_segment(aes(x = 17.5, xend = 19.5, y = 1.5, yend = 1.5), colour = "#00BFFF", size = 2) +
  geom_segment(aes(x = 3.5, xend = 3.5, y = 0.5, yend = 2.5), colour = "lightgrey", size = 2) +
  geom_segment(aes(x = 7.0, xend = 7.0, y = 0.5, yend = 2.5), colour = "gold", size = 2) +
  geom_segment(aes(x = 15.0, xend = 15.0, y = 0.5, yend = 2.5), colour = "black", size = 2) +
  geom_segment(aes(x = 18.5, xend = 18.5, y = 0.5, yend = 2.5), colour = "#00BFFF", size = 2)
tree
```

可以看到，对于水平色带，设置了一个 x坐标范围，但为了得到一条直线，设置了两个y坐标的相同值，而对于垂直线，则设置了相反的值。还可以使用颜色和大小参数分别改变彩带的颜色和厚度。

**运行效果如下：**

![image-20220509132517346](https://gitee.com/njuwh/img-repository/raw/master/img/image-20220509132517346.png)

## Step 5：添加祝福语

为了使图片美观，我们使用extrafont包导入一些额外的字体。

```R
#载入extrafont程揖包
#若未提前安装程揖包需在console窗口执行以下指令
#install.packages("extrafont")
library(extrafont)
font_import()
loadfonts()

# Calling Windows Fonts
windowsFonts(Times_New_Roman=windowsFont("Luminari"))

tree <- tree +
  annotate("text", x = 11, y = 20, label = "Merry Christmas!", 
           family = "Luminari", size = 12,color="white")
tree

#载入ggThemeAssist程揖包
#若未提前安装程揖包需在console窗口执行以下指令
#install.packages('ggThemeAssist')
library(ggThemeAssist)  
ggThemeAssistGadget(tree)
tree + theme(plot.subtitle = element_text(family = "Bookman",
    face = "italic", colour = "white"), plot.caption = element_text(family = "Bookman",
    size = 15, face = "bold.italic", colour = "white"),
    panel.grid.major = element_line(colour = "green4"),
    panel.background = element_rect(fill = "brown3"),
    plot.background = element_rect(fill = "seagreen4",
        colour = NA, size = 1)) +labs(x = NULL, y = NULL, caption = "Work By WangHao")


```

使用带有 “text”参数的注释选项来插入“Merry Christmas!”字样 。

位置选在坐标(11,20)处。字体选择的是大小为 12 的 *Luminari*，如要修改字体，可以通过 family 和size 参数来设置。

**ggThemeAssistGadget(图名)**用来交互式的设置图片细节，通过窗口选择想要的属性后点击Done则会自动生成代码插入。

![image-20220509133735631](https://gitee.com/njuwh/img-repository/raw/master/img/image-20220509133735631.png)

**最终效果如下：**

![image-20220509133340775](https://gitee.com/njuwh/img-repository/raw/master/img/image-20220509133340775.png)

## 参考资料

**[1] https://t-redactyl.io/blog/2016/12/a-very-ggplot2-christmas.html**

