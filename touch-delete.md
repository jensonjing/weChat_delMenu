## 前言

之前写过一篇[微信小程序使用movable-view实现左滑删除功能](https://github.com/XNAL/wxapp-movable-delete/blob/master/movable-delete.md)，有同学使用了这种方案并提出我之前没有发现的bug（现在bug已进行了修复😏），感谢🙏。

有同学问了如果不使用`movable-view`的话如何来实现左滑删除？那这次就写一下如何只使用`touch`事件来实现左滑删除。


## 实现原理

1. 此次组件需要处理`touchstart`、`touchmove`和`touchend`三个`touch`事件

2. `touchstart`事件时需要先禁用掉`transition`动画，同时记录下触摸点的`pageX`和`pageY`的值，以及此时滑动的起始点，**即删除按钮的状态，隐藏还是显示**。

3. `touchmove`事件处理手指的滑动，此时需要进行以下不同情况的判断：

   - 手指上下移动的距离超过左右滑动的距离，此时我们可以把用户的操作理解为上下滑动而不是左右滑动，此时我们对组件不进行移动

   - 判断如果是以下两种情况时不进行组件的移动：

     > 1. 在最右边时向右滑动;
     > 2. 在最左边时向左滑动

   -  如果手指滑动超出了删除按钮的宽度时（包括左滑和右滑两个方向），取按钮宽度作为移动距离

   - 其他情况：手指滑动了多少就对组件位移多少

4. `touchend`事件则是处理手指离开屏幕之后组件的一些后续动作:

   - 先根据`touchmove`中记录的手指位移方向进行判断，非左右滑动时则对组件不进行移动

   - 左右移动超出右滑最大位移时，设置位移终点为0

   - 左右移动超出左滑最大位移时，设置位移终点为`负的删除按钮宽度`

   - 以下两种情况组件都滑动到右边起点（即删除按钮隐藏的状态）：

     > 1. 从右边起点左滑但未超过最大位移的一半，回退到右边起点
     > 2.  从左边起点右滑且超过最大位移的一半，继续滑到到右边起点

   - 其他情况组件都滑到左边起点（即删除按钮显示的状态）
   - 设置位移值，并打开组件的`transition`动画



## 效果图

![左滑](https://github.com/XNAL/wxapp-movable-delete/blob/master/screenshorts/touch-delete.gif)

