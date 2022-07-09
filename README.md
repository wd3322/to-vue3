# Vue2 Opitons api to Vue 3 Composition api

[Github](https://github.com/wd3322/vue2opitons-to-vue3composition)
[Gitee](https://gitee.com/wd3322/vue2opitons-to-vue3composition)

## 在线使用

![home](https://gitee.com/wd3322/sample-resourcessample-resources/raw/master/vue2opitons-to-vue3composition/home.png)

## Props / Data 数据转换
![props_data](https://gitee.com/wd3322/sample-resourcessample-resources/raw/master/vue2opitons-to-vue3composition/props_data.png)

## Computed 计算器属性转换
![computed](https://gitee.com/wd3322/sample-resourcessample-resources/raw/master/vue2opitons-to-vue3composition/computed.png)

## Watch 侦听器转换
![watch](https://gitee.com/wd3322/sample-resourcessample-resources/raw/master/vue2opitons-to-vue3composition/watch.png)

## Life cycle 生命周期转换
![life_cycle](https://gitee.com/wd3322/sample-resourcessample-resources/raw/master/vue2opitons-to-vue3composition/life_cycle.png)

## Methods 方法转换
![methods](https://gitee.com/wd3322/sample-resourcessample-resources/raw/master/vue2opitons-to-vue3composition/methods.png)

## Install 安装
```node
npm install vue2opitons-to-vue3composition
```

## Conversion 使用转换
```javascript
import Vue2opitonsToVue3composition from 'vue2opitons-to-vue3composition'

const vue2ScriptContentStr = `
export default {
  name: 'Sample',
  props: {
    userInfo: {
      type: Object,
      required: false,
      default: () => ({
        userName: 'Todd Cochran',
        userAge: 20
      })
    }
  },
  data() {
    return {
      firstName: '',
      lastName: ''
    }
  }
}`
const vue3ScriptContentStr = Vue2opitonsToVue3composition(vue2ScriptContentStr)
console.log('Hello! Composition API\n', vue3ScriptContentStr)
```

## 无法解析的外部内容

请不要键入Mixin、Component等外部内容，转换器无法解析外部的文件，Mixin混入内部的变量与方法都需手工处理

```javascript
export default {
  name: 'Sample',
  mixins: [myMixin],
  components: { Echart }
}
```

## Template中的Data变更

转换后需为Template中的Data数据需加上'.data'前缀，其原因是许多开发者在Options API语法中做了改变引用类型数据地址的行为（如下），Data将会被转换为一个完整的对象以兼容此类操作，此方式额外产生的迭代成本更小

### Before: 

```html
<template>
  <div>{{ userInfo }}</div>
</template>
```

```javascript
export default {
  name: 'Sample',
  data() {
    return {
      userInfo: {}
    }
  },
  created() {
    this.userInfo = { name: 'Casey Adams', age: 80 }
  }
}
```

### After: 

```html
<template>
  <div>{{ data.userInfo }}</div>
</template>
```

```javascript
import { reactive } from 'vue'

const data = reactive({
  userInfo: {}
})

data.userInfo = { name: 'Casey Adams', age: 80 }
```

## Template中的Filter变更

Filter将会被转换为外部的Function内容，需要在Template中改变Filter的调用方式

### Before: 

```html
<template>
  <div>{{ married | toMarried }}</div>
</template>
```

```javascript
export default {
  name: 'Sample',
  filters: {
    toMarried(value) {
      return value ? 'Yes' : 'No'
    }
  }
}
```

### After: 

```html
<template>
  <div>{{ toMarried(data.married) }}</div>
</template>
```

```javascript
function toMarried(value) {
	return value ? 'Yes' : 'No'
}
```


## Data中键入的Function内容

Data中并不建议直接键入Function内容，在Options API中，Function内容可以使用this指向获取到Data，但由于转换后的Function内容无法直接获取Data自身，需要手动添加useData()方法获取数据

### Before: 

```javascript
export default {
  name: 'Sample',
  data() {
    return {
      userName: 'Casey Adams',
      getUserName: () => {
        return 'Hi!' + this.userName
      }
    }
  }
}
```

### After: 

```javascript
import { reactive } from 'vue'

const useData = () => data
const data = reactive({
  userName: 'Casey Adams',
  getUserName: () => {
    const data = useData()
    return 'Hi!' + data.userName
  }
})
```

## Vue2.7中延用Router3.x、Vuex3.x

如若不想在Vue2.7项目中更新Router4与Vuex4，可以从vue实例中获取Router、Router、Store

```javascript
import { getCurrentInstance } from 'vue'

const $vm = getCurrentInstance()
const router = $vm.proxy.$router
const route = $vm.proxy.$route
const store = $vm.proxy.$store
```

---

Package: vue2opitons-to-vue3composition

Version: 1.0.0

Date: 2020-07-10

E-mail: diquick@qq.com

Author: wd3322
