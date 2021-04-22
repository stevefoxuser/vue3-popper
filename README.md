# vue3-popper
Vue3+组件 -- 基于popperjs v2+的组件，比elementui的el-popover灵活且使用简单。

<img src="demo.gif" />

## 使用 How to use

### 需要安装依赖Popperjs  官方网站 https://popper.js.org/
step1: npm i @popperjs/core  

### 在vue文件中使用
step2: 
```javascript
<template>
<div>
  <div>useage 1</div>
  <Popper placement="right" trigger="hover">
    <template #anchor><button>open</button></template>
    <div>Content is here</div>
  </Popper>
  <div>useage 2</div>
  <button @click="$refs.PopperCom.show($event)">click me<button>
  <Popper ref="PopperCom">
    <div>Content is here</div>
  </Popper>
</div>
</template>
<script>
  import Popper from './Popper'
  export default {
    components:{ Popper }
  }
</script>
```
### 一共只有两个function可以调用
<button @click="$refs.PopperCom.show($event)">click me<button>
<button @click="$refs.PopperCom.hide()">click me<button>
### 关于props参数 
@placement 定义弹窗的方向，top/top-start/top-end/bottom/bottom-start/bottom-end/left/left-start/left-end/right/right-start/right-end  默认是auto，自动选择
  
@trigger：hover，focus，click 打开弹窗的方式，默认是click

@hideclose：隐藏右上角的x，弹出框很小的时候可以把x去掉，比如做tooltip的时候
