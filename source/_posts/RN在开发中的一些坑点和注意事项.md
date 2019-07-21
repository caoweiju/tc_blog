---
title: RN在开发中的一些坑点和注意事项
date: 2019-07-21 18:58:20
tags:
- react-native
categories: 
- react-native
---

# react-native
`react-native`是一个可以使用JavaScript+react编写移动端native应用的框架，是目前一种很是热门的一种移动应用开发方案。但是react-native通用存在着很多的问题，这让很多开发人员不得不注意其中的问题所在，并避免踩坑，这里记录一下常见的坑点，主要是分为三类：
1. 通用坑点
2. Android坑点
3. IOS的坑点

<!-- more -->


## 通用坑点
通用坑点：是指在移动端常见的问题，无论是`Android`还是`IOS`的问题。
1. `Text`组件问题，仅设置`paddingLeft=15`，实际效果是`paddingLeft`和`paddingRight`都是15，如果仅设置`paddingLeft`，需要手动将`paddingRight=0`
2. `ImageBackground`设置`style`的`resizeMode`属性无效，需要设置在`imageStyle`上面
3. 设置多个tag标签单行显示，超出隐藏，不显示截断，自适应
    ````
    <View style={[styles.tag]}>
        tags-0
    </View>

    const styles = StyleSheet.create({
      tag: {
        overflow: 'hidden',//超出部分剪裁
        height: 24,//限制高度，换行后被剪裁
        width: 300,//显示指定宽度
        //必须指定一个背景，否则该容器不一定会生成native中的一个View造成无法剪裁内容，依然换行。
        backgroundColor: '#fff',
        flexDirection: 'row'
        flexWrap: 'wrap', //包裹内容，能换行
        alignItems: 'center'
      }
    })
    ````
4. `KeyboardAvoidingView`组件需要设置在最外层，这样键盘弹起的时候全屏才生效。
5. 设置 `FlatList` 组件的`data=null` 仍会触发 `keyExtractor` 和 `renderItem` 导致页面白屏,可以设置兜底的`data`或者`item`
6. 在使用`TextInput`的时候，如果页面中存在可以滑动的`ScrollView`等滚动组件的时候，在键盘弹起的时，会存在点击两次才能触发`button`或者其他控件的点击事件，可以将`FlatList`或者`ScrollView`的`keyboardShouldPersistTaps`属性的值设置为 `always`即可，其默认是`never`
7. `StatusBar hidden` 会影响 native 的状态栏，在 `android` 上设置 `hidden/color` 在 iOS 上使用 `setBarStyle light-content` 之类适用方案
8. 字母与数字在安卓和ios下占位高度不同，样式无法统一的问题,可以通过设置相同字体来处理`fontFamily: 'PingFangSC-Regular'`
9. 在设置Image组件的source路径时，require 中的图片名字必须是一个静态字符串【不能是变量】。如果需要业务逻辑动态获取，应将条件判断的语句放到require 外
10. 实现动画或者交互效果的时候，慎重使用reduce里的数据。因为使用action更新数据会相比setState有更长的延时。涉及动画或复杂交互，建议用setState来更新
11. TextInput 只设 Padding 不设宽度，自适应有问题，文本展示不全
12. 使用 zIndex 在iOS 设备上不一定会符合显示要求
13. scrollview和touchabe组件之间的相互嵌套和多层级嵌套，会存在滚动和点击问题【暂时没有好的梳理】

## 安卓坑点
1. TextIput出现黑色下划线`<TextInput underlineColorAndroid={'transparent'} /> `
2. android的`textinput `的placeholder 多行文本垂直居中显示，而ios左上对齐; `textAlignVertical='top'`
3. Text不要设置height，Android字体会展示不全。若有高度要求，设置lineHeight
4. render里面view函数不要加分号，Android View会绘制不出来,  并且会红屏crash
    ````
    render (<View>{this.renderChild()};</View>) // {}后面的分号删除
    ````
5. Toast组件在安卓上展示位置吸底（应该居中显示）
    ````
    不推荐：Toast.show(message, Toast.SHORT) 
    推荐使用： Toast.showWithGravity(message, Toast.SHORT, Toast.CENTER)
    ````
6. 小米手机，部分控件字体展示不全，设置fontFamily属性, 安卓设置 fontFamily: ''，但iOS不能设施否则会crash
7. TextInput 只设 Padding 不设宽度，自适应有问题，文本展示不全
8. 部分组件在使用 style  display:'none' 不生效，不推荐使用display来隐藏组件，通过条件渲染方式来处理
9. overflow: visiable，属性设置在android上无效，超出部分会被隐藏，所以对于超出父容器的展示，不要使用visiable来控制
10. Android 部分机型的 TextInput 中 onKeyPress 回调不会被执行，用 onTextChanged 来监听输入
11. 机型兼容，三星s8手机 `TouchableOpacity`, 若包含多个子组件，不能用一个`View`来包装它们，不然点击无法响应
12. Margin、Padding等属性设置为负数，在iOS能展示正常，但在Android上出现异常（AndroidMargin、Padding等属性设置为负数对写法有依赖）
13. Text等基础组件color、size等属性设置缺失，造成两端展示不一致
14. RN小米手机UI适配问题解决,文本截断不展示完整，给展示不全的文本，设置一个fontFamily属性，值为空''，展示不全和不容截断就都能解决
15. 使用`&&`可以进行条件渲染，但是实际使用时在`Android`上渲染会出现问题，导致`View`显示问题，使用双重取反` !!Condition && <View/>`


## IOS的坑点
有的时候，坑可能是是只在IOS测，比如以下：
1. `Text`设置`borderRadius`在iOS不生效，需要添加`borderWidth: 0`, 同时Text组件只设置某一边`border`[borderTop]的时候失效。
2. `SafeArea` 内仅有一个` FlatList `时，`FlatList`顶部会莫名其妙的多一部分 `contentInset`（iOS10）;可以再顶部添加一个空`<View />`组件
3. 使用 Modal 的时候，如果在上一个 Modal 消失前出现另一个 alert、toast、Modal，会导致上一个 Modal 无法消失，只能重启应用。暂时没有好办法处理
4. `alignVertical` 不生效，可以考虑嵌套一次View
5. 隐藏导航栏会带条线
    ````
    headerTransparent: true,
    headerMode: "none",
    header: null,
    ````
6. TextInput在iOS上没有办法输入中文
    ````
    export default class LLTextInput extends Component {
      shouldComponentUpdate (nextProps){
          return Platform.OS !== 'ios'
              || (this.props.value === nextProps.value && (nextProps.defaultValue == undefined || nextProps.defaultValue == '' ))
              || (this.props.defaultValue === nextProps.defaultValue && (nextProps.value == undefined || nextProps.value == '' ))
      }
      render() {
          return <TextInput {...this.props} />
      }
    }
    ````
7. list组件的全屏模式是，会导致IOS的左滑返回手势失效，左侧边沿设置一个10px的透明view
