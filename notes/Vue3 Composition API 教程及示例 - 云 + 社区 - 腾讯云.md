> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [cloud.tencent.com](https://cloud.tencent.com/developer/article/1603056)

> Vue 引入了 Composition API（基于功能的 API）作为当前基于 Option 的 API 的补充。该 API 将随 Vue 3 一起发布，但是现在您可以通过将 Vue...

Vue 引入了 Composition API（基于功能的 API）作为当前基于 Option 的 API 的补充。该 API 将随 Vue 3 一起发布，但是现在您可以通过将 Vue 3 Composition API 添加到您的 Vue 2 应用程序中来进行尝试。

在本教程中，我将向您展示：

*   新的 Vue Composition API 概述以及与基于经典 Vue Options 的 API 的比较
*   使用新 API 实现 Vue 组件的示例：Props，data，watch，生命周期钩子
*   充分利用新的 Vue 3 Composition API（基于函数的 API）的示例：将代码拆分为函数

**添加 Vue Composition API 会发生什么变化？**

一切仍将像以前一样工作。几乎没有任何变化：

*   命令行界面
*   模板语法
*   对象格式
*   数据响应
*   计算属性，观察者和组件生命周期的概念
*   SFC 格式
*   Vue 框架的渐进性

#### **基于选项的 API 与组合 API**

当前基于选项的 API 概念与新的合成 API（基于函数的 API）概念的区别在于：

*   基于选项的 API：组件包含属性 / 方法 / 选项的类型。
*   组合 API：组件将逻辑封装到函数中。

组件选项可能变得组织起来复杂且难以维护（怪异的组件）。逻辑可能涉及 props 和 data() 的属性，某些方法，某个钩子（beforeMount/mounted）以及值班的 watch。因此，一个逻辑将分散在多个选项中。

使用 Composition API，每个功能都是大型组件的一部分，它封装了与逻辑相关的所有代码（属性，方法，钩子，watch 观察者）。现在，较小的代码（函数）可以重复使用，并且组织得很好。

#### **在当前的 Vue 2 项目中使用 Vue 3 Composition API**

通过安装 @vue/composition-api 模块，我们可以在当前的 Vue 2.x 项目中使用新的 Vue 3 Composition API。

非常简单，只需运行以下命令：

```
npm install @vue/composition-api

or

yarn add @vue/composition-api
```

然后将其导入 main.js。

```
import Vue from 'vue';
import CompositionApi from '@vue/composition-api';

Vue.use(CompositionApi);
```

#### **Vue setup() 函数**

setup() 是新的组件选项，我们将使用新的 Vue Composition API 设置组件的逻辑。如果 setup() 函数变得复杂，我们可以轻松地将其拆分为多个具有逻辑主题的函数。

**何时调用 setup()？**

创建组件实例时，在 props 解析后调用它。

现在，使用 setup() 函数查看 Vue 组件：

```
const MyComponent = {
  props: {
    name: String
  },

  setup(props, context) {
    console.log(props.name);
    
    
    
    
    
  }
}
```

该函数有 2 个参数：

**–`props`**

**–`context`**

`context具有与this.$attrs，this.$slots，this.$emit，this.$parent，this.$root对应的属性（属性，插槽，emit，parent，root）。`

`即使在更新后，我们也可以使用解构attrs`来`获取`最新值`：`

```
const MyComponent = {
  setup(props, { attrs }) {
    
    function onClick() {
      attrs.foo 
    }
  }
}
```

> * 注意：this 关键字在 setup() 函数中不可用。

所以 this.$emit 不能这样使用：

```
setup() {
  function onClick() {
    this.$emit 
  }
}
```

#### **Composition API 中的 this.$refs**

为了获得对模板中元素或组件实例的引用，我们使用 ref API，以便 setup() 可以为渲染上下文返回可响应和可变对象。

```
import { ref } from '@vue/composition-api'

const MyComponent = {
  setup(props) {
    const name = ref('bezkoder.com')
    const appendName = () => {
      name.value = `hello ${props.name}`
    }
    return {
      name,
      appendName
    }
  },

  template: `<div @click="appendName">{{ name }}</div>`
}
```

ref 会自动解包为内部值，因此我们无需在模板中附加. value:。{{name}} 就足够了，而不是 {{name.value}}。

#### **Vue Composition API 计算值**

基于 Vue2 选项的 API 语法：

```
export default {
  props: {
    title: String
  },

  computed: {
    vTitle() {
      return '-' + this.title + '-';
    },
    itemsQuantity() {
      return this.items.length;
    }
  },

  data() {
    return {
      items: ['This', 'is'],
    };
  },
}
```

Vue 3 Composition API 语法：

```
import { ref, computed } from '@vue/composition-api';

export default {
  props: {
    title: String
  },
  setup(props) {
    const vTitle = computed(() => '-' + props.title + '-');

    const items = ref(['This', 'is']);
    const itemsQuantity = computed(() => items.value.length);

    return {
      vTitle,
      items,
      itemsQuantity,
    };
  }
};
```

使用新的计算 API，我们可以使用 get 和 set 函数创建可写的 ref 对象。

```
const count = ref(1)
const double = computed({
  get: () => count.value * 2,
  set: val => { count.value = val - 1 }
})

double.value = 3          
console.log(count.value)  
console.log(double.value)
```

#### **Vue Composition API Watch**

这就是我们使用基于 Vue2 选项的 API 语法的方式：

```
export default {
  data() {
    return {
      items: ['This', 'is'],
      append: ''
    };
  },

  watch: {
    items: {
      handler: function(value, oldValue) {
        this.append = '';
        value.forEach(item => {
          this.append += item + ' ';
        });
      },
      immediate: true
    }
  },
}
```

就像 watch 选项一样，每次状态改变时，我们都可以使用新的 Vue watch API 来执行副作用逻辑代码。

语法为：watch（源，回调，选项）

*   source：可以是 getter 函数，值包装器**或包含上述两种类型的数组**（如果要查看多个源）
*   callback：是类似于 Vue2 watcher 处理程序的函数，带有 2 个参数：newVal，oldVal。**每个参数都可以是一个数组 (用于观察多个源):** [newVal1，newVal2，... newValN]，[oldVal1，oldVal2，... oldValN]
*   options（可选）：用于配置观察者类型，其中包括：lazy，deep，flush。

```
import { ref, watch } from '@vue/composition-api';

export default {
  setup(props) {
    const items = ref(['This', 'is']);
    const append = ref('');

    watch(
      
      () => items.value,

      
      (items, oldItems) => {
        append.value = '';
        items.forEach(item => {
          append.value += item + ' ';
        });
      },

      
      {
        lazy: false 
      }
    )

    return {
      items,
      append
    };
  }
};
```

如果我们想观察多个源：

```
watch([aRef, bRef], ([a, b], [prevA, prevB]) => {
  
})
```

我们还可以将多个源观察程序拆分为较小的观察程序。这有助于我们组织代码并创建具有不同选项的观察程序：

```
watch(
  
  () => items.value,

  
  (items, oldItems) => {
    append.value = '';
    items.forEach(item => {
      append.value += item + ' ';
    });
  },

  
  {
    lazy: false 
  }
)

watch(
  
  () => todo.value.length,

  
  (length, oldLength) => {
    todoLength.value = length;
  },

  
  {
    lazy: true 
  }
)
```

#### **Vue Composition API 生命周期钩子**

使用 Vue2，我们通过以下方式实现 Lifecycle Hooks 函数：

```
export default {
  beforeMount() {
    console.log('V2 beforeMount!')
  },

  mounted() {
    console.log('V2 mounted!')
  }
};
```

新的 Vue 3 Composition API 具有等效的功能，我们可以在 setup() 函数内使用带前缀的功能：

```
import { onBeforeMount, onMounted } from '@vue/composition-api';

export default {
  setup() {
    onBeforeMount(() => {
      console.log('V3 beforeMount!');
    })

    onMounted(() => {
      console.log('V3 mounted!');
    })
  }
};
```

您可以在下表中看到 Vue2 生命周期 Options 与 Composition API 之间的映射：

![](https://ask.qcloudimg.com/http-save/yehe-1174931/zv4vloe0ck.jpeg)

#### **将逻辑封装到函数中**

现在，我们将以上所有代码组合到一个 Vue 组件中，您可以看到一个包含多个逻辑的复杂组件。我们需要为标题，待办事项和项目实现对应的逻辑：

```
import { ref, reactive, computed, watch, onBeforeMount, onMounted } from '@vue/composition-api';

export default {
  props: {
    title: String,
    initInput: String
  },

  setup(props) {
    const vTitle = computed(() => '-' + props.title + '-');

    const todo = ref(props.initInput);
    const todoLength = ref(0);

    const items = ref(['This', 'is']);
    const itemsQuantity = computed(() => items.value.length);
    const append = ref('');

    watch(
      
      () => items.value,

      
      (items, oldItems) => {
        append.value = '';
        items.forEach(item => {
          append.value += item + ' ';
        });
      },

      
      {
        lazy: false 
      }
    )

    watch(
      
      () => todo.value.length,

      
      (length, oldLength) => {
        todoLength.value = length;
      },

      
      {
        lazy: false 
      }
    )

    const add = () => {
      if (todo.value) {
        items.value.push(todo.value);
        todo.value = '';
      }
    };

    const remove = index => {
      items.value.splice(index, 1);
    };

    onBeforeMount(() => {
      console.log('V3 beforeMount!');
    })

    onMounted(() => {
      console.log('V3 mounted!');
    })

    return {
      vTitle,
      todo,
      todoLength,
      items,
      itemsQuantity,
      append,
      add,
      remove
    };
  }
};
```

是时候利用 Vue Composition API 了：将复杂的组件拆分为多个对应于不同逻辑的函数：

```
import { ref, computed, watch, onBeforeMount, onMounted } from "@vue/composition-api";

function useTitle(props) {
  const vTitle = computed(() => "-" + props.title + "-");

  return {
    vTitle
  };
}

function useTodoLength(todo) {
  const todoLength = ref(0);

  watch(
    
    () => todo.value.length,

    
    (length, oldLength) => {
      todoLength.value = length;
    },

    
    {
      lazy: false 
    }
  );

  return {
    todoLength
  };
}

function useItems(todo) {
  const items = ref(["This", "is"]);
  const itemsQuantity = computed(() => items.value.length);
  const append = ref("");

  watch(
    
    () => items.value,

    
    (items, oldItems) => {
      append.value = "";
      items.forEach(item => {
        append.value += item + " ";
      });
    },

    
    {
      lazy: false 
    }
  );

  const add = () => {
    if (todo.value) {
      items.value.push(todo.value);
      todo.value = "";
    }
  };

  const remove = index => {
    items.value.splice(index, 1);
  };

  return {
    items,
    itemsQuantity,
    append,
    add,
    remove
  };
}

export default {
  props: {
    title: String,
    initInput: String
  },

  setup(props) {
    const todo = ref(props.initInput);

    onBeforeMount(() => {
      console.log("V3 beforeMount!");
    });

    onMounted(() => {
      console.log("V3 mounted!");
    });

    return {
      todo,
      ...useTitle(props),
      ...useTodoLength(todo),
      ...useItems(todo)
    };
  }
};
```

#### **最后**

使用旧的基于 Vue 选项的 API 时，您可能会感到熟悉舒适；或者，您不希望将所有内容都作为函数，而是继续保持 OOP 思维方式的属性 / 方法。这些想法都没问题。同时开发者们也在积极开发 Vue，并使其逐年变得更好，并为我们提供更多选择。何不尝试一下，感觉也很不错哦。