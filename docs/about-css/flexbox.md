## Flexbox 小笔记

[返回首页](../../README.md)

### 基础用法

display: flex | inline-flex; (适用于父类容器元素上)
父类元素属性

### 父元素概览

* flex-direction
* flex-wrap
* flex-flow
* flex-flow
* justify-content
* align-items
* align-content

### 父元素详述

* flex-direction: row | row-reverse | column | column-reverse
* flex-wrap: nowrap | wrap | wrap-reverse
* flex-flow: <‘flex-direction’> || <‘flex-wrap’> `复合属性`
* justify-content: flex-start | flex-end | center | space-between | space-around `设置或检索弹性盒子元素在主轴（横轴）方向上的对齐方式`
* align-items: flex-start | flex-end | center | baseline | stretch `设置或检索弹性盒子元素在侧轴（纵轴）方向上的对齐方式`
* align-content: flex-start | flex-end | center | space-between | space-around | stretch `设置或检索弹性盒堆叠伸缩行的对齐方式`

### 子元素概览

* order - 排序
* flex-grow
* flex-shrink
* flex-basis
* flex
* align-self

### 子元素详述

* order: <integer> `数值小的排在前面。可以为负值`
* flex-grow: <number> (default 0) `设置或检索弹性盒的扩展比率`
* flex-shrink: <number> (default 1) `设置或检索弹性盒的收缩比率,根据弹性盒子元素所设置的收缩因子作为比率来收缩空间`
* flex-basis: <length> | auto (default auto) `设置或检索弹性盒伸缩基准值`
* flex：none | [ flex-grow ] || [ flex-shrink ] || [ flex-basis ] `复合属性。设置或检索伸缩盒对象的子元素如何分配空间。如果缩写flex:1, 则其计算值为：1 1 0`
* align-self: auto | flex-start | flex-end | center | baseline | stretch `设置或检索弹性盒子元素自身在侧轴（纵轴）方向上的对齐方式`


### 概念

* 剩余空间概念
* 剩余空间＝父容器空间－子容器1.flex-basis/width - 子容器2.flex-basis/width - …
* 如果父容器空间不够，就走压缩flex-shrink，否则走扩张flex-grow；
* 如果你不希望某个容器在任何时候都不被压缩，那设置flex-shrink:0；
* 如果子容器的的flex-basis设置为0 (width也可以，不过flex-basis更符合语义)，那么计算剩余空间的时候将不会为子容器预留空间。
* 如果子容器的的flex-basis设置为auto(width也可以，不过flex-basis更符合语义)，那么计算剩余空间的时候将会根据子容器内容的多少来预留空间。

> 作者简介

**谯洪敏** 滴滴上海前端团队负责人，前陆金所移动团队负责人，喜欢天文学和UFO研究，多次和UFO接触。

![](../../images/qiaohongmin.jpeg)