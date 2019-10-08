---
title: svg
date: 2019-10-08 17:00:20
tags:
---

## VUE-CLI3.0配置自动化svg

### 编写原因
项目使用的是`阿里巴巴矢量图标库`[iconfont](https://www.iconfont.cn/home/index?spm=a313x.7781069.1998910419.2), 使用的是Font class模式, 由于项目迭代，管理不严格。 出现了新增新图标异常麻烦, 需要新建一个新建的font文件夹, 还存在字体冲突的原因。

### 参考文档
[vue-cli3.0][vue-cli-url]
[vue-admin-template](https://github.com/PanJiaChen/vue-admin-template)
[require.context](https://www.jianshu.com/p/c894ea00dfec)
[svg-sprite-loader](https://github.com/kisenka/svg-sprite-loader)


### 版本相关,安装依赖
vue v2.6.10
vue-cli v3.11
node v10.16.0
npm v6.9.0
svg-sprite-loader v4.1.6

**创建项目**
```
vue create svg-demo
```

**安装svg-sprite-loader**
```
npm install --save svg-sprite-loader
```

### 代码
**vue.config.js**
进入到项目svg-demo中, 创建vue.config.js文件。参考 [vue-cli3.0][vue-cli-url]
```js
const path = require('path')

function resolve(dir) {
	return path.join(__dirname, '', dir)
}

module.exports = {
	//	选项
	chainWebpack: config => {
		const svgRule = config.module.rule('svg');
		const urlRule = config.module.rule('url-loader')

		// 清除已有的所有loader
		svgRule.uses.clear();
		urlRule.uses.clear();

		// 添加需要替换的loader
		svgRule
			.test(/\.svg$/)
			.use('svg-sprite-loader')
			.loader('svg-sprite-loader')
			.options({
				symbolId: 'icon-[name]'
			})
			.end()
			.include
			.add(resolve('src/icons'))
			.end()

		// 其他的svg使用url-loader
		urlRule
			.test(/\.svg$/)
			.use('url-loader')
			.loader('url-loader')
			.options({
				limit: 10000
			})
			.end()
			.exclude
			.add(resolve('src/icons'))
			.end()
	}
}
```
**组件**
```js
<template>
  <svg :class="className" class="svg-icon" aria-hidden="true">
    <use :xlink:href="iconName" />
  </svg>
</template>

<script>
export default {
  name: "svg-icon",
  props: {
    name: {
      type: String,
      required: true
    },
    className: {
      type: String,
      default: ""
    }
  },
  computed: {
    iconName() {
      return `#icon-${this.name}`;
    }
  }
};
</script>
```
**自动化导入**
>icons (文件夹)
>>index.js
>>svg (文件夹存放svg)
```js
import Vue from 'vue'
import SvgIcon from '@/components/SvgIcon'

// 注册全局组件
Vue.component('svg-icon', SvgIcon)

// 给svg下面的文件夹批量导入
const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./svg', false, /\.svg$/)
requireAll(req)
```
**注入**
在`main.js`加入
```
import '@/icons'
```
**调用**
```html
<svg-icon name="facebook"></svg-icon>
```

### 遇到的问题
1. 在`vue.config.js`中, `include, exclude`添加文件路径出问题. 原因是由于`path.join(__dirname, '', dir)`路径的拼合错误. 在`vue-admin-template`是`path.join(__dirname, '__', dir)`导致loader也解析不了svg
2. 有点不理解`index.js`中的`require.context`, 通过参考文档原来是webpack提供的一个api,批量导入文件夹中的文件

[vue-cli-url]: https://cli.vuejs.org/zh/guide/html-and-static-assets.html#从相对路径导入