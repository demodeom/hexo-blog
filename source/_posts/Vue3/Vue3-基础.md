---
title: Vue3 - 基础
date: 2021-08-12 07:24:03
tags:
    - 笔记
categories:
    - Vue3
---

Vue3 - 基础

<!-- more -->

## 准备

nrm 配置 npm 镜像

```bash
npm install nrm -g
```

使用淘宝镜像

```bash
nrm use taobao
```

下载 vue-cli

```bash
npm install -g @vue/cli
```

## 创建项目

创建项目有以下两种方式

1. 使用命令行: `vue create hello_world`
1. 使用图形化界面: `vue ui`


## Vue

### data

在 `setup()` 中定义 data

```js
import { ref } from 'vue'
export default {
	name: "HelloWorld",
	setup(){
		const count = ref(0)

		return { content_str }
	}
}
```

在 template 中使用 data

```html
<span>{{ count }}<span>
```

在函数中调用或者修改 data

```js
import { ref } from 'vue'
export default {
	name: "HelloWorld",
	setup(){
		const count = ref(0)

		const increase = () => {
			count.value++
		}

		return { content_str, increase }
	}
}
```

使用 reactive 将数据和函数组合起来

```js
import { reactive } from 'vue'
export default {
	name: "HelloWorld",
	setup(){
		const data = reactive({
			count: 0,
			increase: () => {
				count++
			}
		})
		return { data }
	}
}
```

在 template 中使用 data

```html
<span>{{ data.count }}<span>
```

如何省略 `data.count` 中的 `data.`

```js
import { reactive } from 'vue'
export default {
	name: "HelloWorld",
	setup(){
		const data = reactive({
			count: 0,
			increase: () => {
				count++
			}
		})
		return {
			count: data.count,
			increase: data.increase
		}
	}
}
```

如果有十个变量, 十个方法, 有没有简单点的方法

```js
import { reactive, toRefs } from 'vue'
export default {
	name: "HelloWorld",
	setup(){
		const data = reactive({
			count: 0,
			increase: () => {
				count++
			}
		})
		const refData = toRefs(data)
		return {
			... refData
		}
	}
}
```

Vue3 解决了数组或者对象属性的动态修改渲染问题


## 生命周期函数

|Vu2|Vue3|
|:---|:---|
|beforeCreate|setup()|
|created|setup()|
|beforeMount|onBeforeMount|
|mounted|onMounted|
|beforeupdate|onBeforeupdate|
|updated|onUpdated|
|beforeDestroy|onBeforeunmount|
|destroyed|onunmounted|
|activated|onActivated|
|deactivated|onDeactivated|
|errorcaptured|onErrorCaptured|

新增的生命周期函数

onRenderTracked
onRenderTriggered


```js
import { reactive, toRefs } from 'vue'
export default {
    name: "HelloWorld",
    setup(){
        onMounted(()=>{
            console.log('mounted')
        })
    }
}
```
