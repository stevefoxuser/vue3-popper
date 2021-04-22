# vue3-popper
Vue3+组件 -- 基于popperjs v2+的组件，比elementui的el-popover灵活且使用简单。

<img src="demo.gif" />

## 使用 How to use

### 需要安装依赖Popperjs  官方网站 https://popper.js.org/
step1: npm i @popperjs/core  

### 在vue文件中使用
step2: 
```
<template>
<div>
  <div>useage 1</div>
  <Popper>
    <template #anchor><button>open</button></template>
    <div>Content is here</div>
  </Popper>
  <div>useage 2</div>
  <button @click="$refs.PopperCom.show($event.target)">click me<button>
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

### 一共只有两个show和hide两个function可以调用
### 关于props参数 
placement 同 https://popper.js.org/ 定义弹窗的方向，默认是auto，自动选择
trigger：hover，focus，click 打开弹窗的方式，默认是click
hideclose：隐藏右上角的x，没什么屁屁用的参数
