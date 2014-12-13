title: 弹性模量
date: 2014-12-12 19:44:30
tags: Geophysics
description: 
---
弹性模量描述了当有力施加于物体或材料上时，它抵抗弹性形变的能力。主要有以下几种类型：

* 杨氏模量
* 泊松比
* 拉梅常数
* 体积模量

<!--more-->

![](/img/121215_01.jpeg)


## 杨氏模量（Young's modulus）
线弹性理论中应力与应变是线性关系，因此$\sigma_x$和$\epsilon_x$有线性关系：

$$\epsilon_x = \frac{1}{E} \sigma_x$$

杨氏模量是描述固体材料抵抗形变能力的物理量，反映了材料的硬度。

## 泊松比 （Poisson's ratio）
由于应力$\sigma_x$的作用，导致材料宽度$D$的增加，可以用泊松比来描述。因为水平方向
$\epsilon_y = \epsilon_z = (D - D')/D$是一个负数，所以这样定义：

$$v = - \frac{\epsilon_y}{\epsilon_x}$$

泊松比描述了轴向压缩时横向扩张的能力。

## 拉梅常数 （Lame's parameters）
对于各向同性介质，应力应变满足：

![](/img/121215_02.jpeg)

其中$G$又被称为刚性模量（the modulus of rigidity）或者剪切模量（the shear modulus），它描述了
材料对剪切形变的抵抗能力。

## 体积模量 （the bulk modulus）
它定义了静水压力与体积应变的比，在静水压力下，$\sigma_p = \sigma_x = \sigma_y = \sigma_z$，而
$\epsilon_{vol} = \epsilon_x + \epsilon_y + \epsilon_z$，所以

$$K = \frac{\sigma_p}{\epsilon_{vol}} = \lambda + \frac{2}{3}G$$

$K$描述了材料对静水压力的抵抗能力

## compaction modulus or oedometer modulus

$$H = \lambda + 2G$$

**上述所有模量的单位与应力单位相同，例如Pa, psi, bar；而应变属于无量纲的量**

最后是这些模量的转换关系如下：

![](/img/121215_03.jpeg)

