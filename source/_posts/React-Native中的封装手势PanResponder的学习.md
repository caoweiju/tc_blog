---
title: React-Native中的封装手势PanResponder的学习
date: 2019-07-08 23:26:15
tags:
- react-native
categories: 
- react-native
---

# react-native
> React Native使你只使用JavaScript也能编写原生移动应用。 它在设计原理上和React一致，通过声明式的组件机制来搭建丰富多彩的用户界面。

React Native产出的并不是“网页应用”， 或者说“HTML5应用”，又或者“混合应用”。 最终产品是一个真正的移动应用，从使用感受上和用Objective-C或Java编写的应用相比几乎是无法区分的。 React Native所使用的基础UI组件和原生应用完全一致。 你要做的就是把这些基础组件使用JavaScript和React的方式组合起来。

React Native完美兼容使用Objective-C、Java或是Swift编写的组件。 如果你需要针对应用的某一部分特别优化，中途换用原生代码编写也很容易。 想要应用的一部分用原生，一部分用React Native也完全没问题 —— Facebook的应用就是这么做的。

## 手势系统

移动设备上的手势识别要比在 web 上复杂得多。用户的一次触摸操作的真实意图是什么，App 要经过好几个阶段才能判断。比如 App 需要判断用户的触摸到底是在滚动页面，还是滑动一个 widget，或者只是一个单纯的点击。甚至随着持续时间的不同，这些操作还会转化。此外，还有多点同时触控的情况。

**手势响应系统可以使组件在不关心父组件或子组件的前提下自行处理触摸交互。**

<!-- more -->
参考官方文档--[响应者的生命周期](https://facebook.github.io/react-native/docs/gesture-responder-system):  

一个 View 只要实现了正确的协商方法，就可以成为触摸事件的响应者。我们通过下面两个方法的其中一种，去“询问”一个 View 是否愿意成为响应者：
* View.props.onStartShouldSetResponder: (evt) => true
    在用户开始触摸的时候（手指刚刚接触屏幕的瞬间），是否愿意成为响应者？
* View.props.onMoveShouldSetResponder: (evt) => true
    如果 View 不是响应者，那么在每一个触摸点(手指触点)开始移动（没有停下也没有离开屏幕）时，仅在滑动的最开始这次询问一次：是否愿意响应触摸交互呢？后面的滑动并不会再触发，  
    如果 View 返回 true，并开始尝试成为响应者，

**这需要注意的是：上面两个方法都是在触点【onStartShouldSetResponder --> 每次手指刚触摸瞬间、onMoveShouldSetResponder --> 每次刚开始滑动瞬间(手指滑动完--停止一段时--再次滑动时也会触发)】**

那么会触发下列事件之一:
* View.props.onResponderGrant: (evt) => {}
    View 现在要开始响应触摸事件了。这也是需要做高亮的时候，使用户知道他到底点到了哪里。
* View.props.onResponderReject: (evt) => {}
    响应者现在“另有其人”而且暂时不会“放权”，请另作安排。
    如果 View 已经开始响应触摸事件了，那么下列这些处理函数会被一一调用：

* View.props.onResponderMove: (evt) => {}
    用户正在屏幕上移动手指时（没有停下也没有离开屏幕）。
* View.props.onResponderRelease: (evt) => {}
    触摸操作结束时触发，比如"touchUp"（手指抬起离开屏幕）。
* View.props.onResponderTerminationRequest: (evt) => true
    有其他组件请求接替响应者，当前的 View 是否“放权”？返回 true 的话则释放响应者权力。
* View.props.onResponderTerminate: (evt) => {}
    响应者权力已经交出。这可能是由于其他 View 通过onResponderTerminationRequest请求的，也可能是由操作系统强制夺权（比如 iOS 上的控制中心或是通知中心）。

**某个组件成为当前手势响应者的话，那么将会阻止手势和相应事件的冒泡，单一手势操作只有一个响应者**

在使用的时候就会影响手势作用的组件，目前封装好的`touchable**`组件内部如果没有其他的`touchable**`子组件，那么将会默认成为在该组件上手势的响应者。
**最底层的`touchable**`组件在默认情况下会成为对应区域手势的最优先响应者**

看这样一个例子，这是一个在`TouchableWithoutFeedback`组件的内部，有一个滚动组件，如果不让内部的`ScrollView`先作为手势响应者，会导致`ScrollView`组件不滚动，
````
<TouchableWithoutFeedback onPress={() => {}}>
    {other content}   
    <View>
        <ScrollView onStartShouldSetResponder={() => true}>
            {scrollable content}
        </ScrollView>
    </View>
</TouchableWithoutFeedback>
````

## PanResponder的学习使用
[PanResponder类可以将多点触摸操作协调成一个手势](https://facebook.github.io/react-native/docs/panresponder), 上面说了，在任何组件上都可以添加`on[StartShouldSet|MoveShouldSet]Responder[Grant|Reject|Move|Release|Termination|TerminationRequest]`属性来设置响应手势，而`PanResponder`可以快捷的设置这些属性，添加到对应的组件上。如下面的示例：
````
componentWillMount: function() {
    this._panResponder = PanResponder.create({
      // 要求成为响应者：
      onStartShouldSetPanResponder: (evt, gestureState) => true,
      onStartShouldSetPanResponderCapture: (evt, gestureState) => true,
      onMoveShouldSetPanResponder: (evt, gestureState) => true,
      onMoveShouldSetPanResponderCapture: (evt, gestureState) => true,

      onPanResponderGrant: (evt, gestureState) => {
        // 开始手势操作。给用户一些视觉反馈，让他们知道发生了什么事情！

        // gestureState.{x,y} 现在会被设置为0
      },
      onPanResponderMove: (evt, gestureState) => {
        // 最近一次的移动距离为gestureState.move{X,Y}

        // 从成为响应者开始时的累计手势移动距离为gestureState.d{x,y}
      },
      onPanResponderTerminationRequest: (evt, gestureState) => true,
      onPanResponderRelease: (evt, gestureState) => {
        // 用户放开了所有的触摸点，且此时视图已经成为了响应者。
        // 一般来说这意味着一个手势操作已经成功完成。
      },
      onPanResponderTerminate: (evt, gestureState) => {
        // 另一个组件已经成为了新的响应者，所以当前手势将被取消。
      },
      onShouldBlockNativeResponder: (evt, gestureState) => {
        // 返回一个布尔值，决定当前组件是否应该阻止原生组件成为JS响应者
        // 默认返回true。目前暂时只支持android。
        return true;
      },
    });
  },

  render: function() {
    return (
      <View {...this._panResponder.panHandlers} />
    );
  },
````

