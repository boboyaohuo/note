## 选用`mobx`原因

1. 项目部分组件嵌套太深，缺少状态管理工具
2. 状态管理工具很多：`flux`  `redux`  `vuex`  `mobx`，选用`mobx`的原因是：**开发难度和学习成本低**、**性能好**、**夸端兼容**
3. 版本选择：由于小程序的`moxb-miniprogram`库的版本最高forked `mobx4`，所以暂时选用`mobx@4.13.1`

## 使用

由于`mobx`是一个与框架无关的数据层的管理工具，所以在不同的框架项目里，需要不同的链接库，具体版本兼容看对应README

- `Vue`：[mobx-vue](https://www.npmjs.com/package/mobx-vue)
- `React`：[mobx-react](https://www.npmjs.com/package/mobx-react)

- `Wx`小程序：[mobx-miniprogram](https://www.npmjs.com/package/mobx-miniprogram)
- `Angular`：[mobx-angular](https://www.npmjs.com/package/mobx-angular)

### store

在`mobx`的版本选择一致的前提下，`store`文件是可以公用的

```typescript
// store.ts
import { action, observable } from 'mobx';

const store = observable({
    // 数据字段
    age: 0,

    // 计算属性
    get computedAge() {
        return this.age + 18;
    },
		
    // action
    setAge: action(function(number) {
        this.age = number;
    })
})

export default store
```



### Vue

```typescript
// ---- ES6 syntax ----
import { Observer } from 'mobx-vue'
import store from './store'

export default Observer({
    data() {
        return {
            store: store
        }
    }
})
```

或者`vue-class`模式使用这种方式

```typescript
// ---- ESNext syntax with decorator syntax enabled ----
import { Vue, Component } from 'vue-property-decorator'
import { Observer } from 'mobx-vue'
import store from './store'

@Observer
@Component
export default class App extends Vue {
	store = store;
}
```



### React

```tsx
import { observer } from 'mobx-react'
import store from './store'

// ---- ES6 syntax ----
const View = observer(
    class View extends React.Component {
        render() {
            return <div>{this.props.store.title}</div>
        }
    }
)
 
// ---- ESNext syntax with decorator syntax enabled ----
@observer
class View extends React.Component {
    render() {
        return <div>{this.props.store.title}</div>
    }
}
 
// ---- or just use function components；example: hooks ----
const View = observer(({ store }) => <div>{ store.title }</div>)
```



### Wx小程序

1. 在 `Page` 构造器中使用（此 **手工绑定** 方法也适用于`Component`，通用方法）：

```typescript
import { createStoreBindings } from 'mobx-miniprogram-bindings'
import { store } from './store'

Page({
    onLoad() {
        this.storeBindings = createStoreBindings(this, {
            store, // mobx store
            fields: ['age', 'computedAge'], // 基本数据和计算数据（①.数组形式，2.映射形式，3.函数形式）
            actions: ['setAge'] // action方法（①.数组形式，2.映射形式）
        });
    },
    onUnload() {
        this.storeBindings.destroyStoreBindings() // 需清理，否则会内存泄漏
    },
    log() {
        this.data.age // 来自于 MobX store 的字段
    }
})
```

2. 在 `Component` 构造器中使用（ **behavior绑定** ）：

```typescript
import { storeBindingsBehavior } from 'mobx-miniprogram-bindings'
import store from './store'

Component({
    behaviors: [storeBindingsBehavior],
    storeBindings: {
        store, // mobx store
        fields: { // 基本数据和计算数据（1.数组形式，②.映射形式，③.函数形式）
            age: () => store.age,
            computedAge: 'computedAge'
        },
        actions: { // action方法（1.数组形式，②.映射形式）
            setAge: 'setAge'
        },
    },
    methods: {
    	log() {
            this.data.age // 来自于 MobX store 的字段
        }
    }
})
```

> 小程序中使用时，需注意 **数据** 和 **方法** 字段，重复的话`data`里的数据会被`mobx`替换掉



## 兼容方案

Store

```typescript
export default class Store {
    constructor(action) {
        return {
            age: 0,
        
            get computedAge() {
                return this.age + 18;
            },
        
            setAge: action(function(number) {
                this.age = number; // TODO this类型有问题
            })
        }
    }
}
```

h5

```typescript
import { action, observable } from 'mobx';
import Store from './index'

const store = observable(new Store(action))

export default store
```

小程序

```typescript
import { action, observable } from 'mobx-miniprogram';
import Store from './index'

const store = observable(new Store(action))

export default store
```
