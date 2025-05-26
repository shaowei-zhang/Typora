Vue

### 1、组件之间的通讯

>**props（父组件 → 子组件） ****父组件通过 props 传递数据给子组件，子组件通过 props 接收。**

**示例
****父组件 (Parent.vue)**

```vue
<template>
  <div>
    <ChildComponent :message="parentMessage" />
  </div>
</template>

<script>
  import ChildComponent from "./ChildComponent.vue";
  export default {
    components: { ChildComponent },
    data() {
      return {
        parentMessage: "Hello from Parent"
      };
    }
  };
</script>
```

**子组件 (**`**ChildComponent.vue**`**)**

```vue
<template>
  <div>{{ message }}</div>
</template>

<script>
  export default {
    props: ["message"]
  };
</script>
```

✅ **适用于：父组件向子组件传递数据**。





>**2.** `**$emit**`**（子组件 → 父组件）**

子组件使用 `$emit` 触发自定义事件，父组件监听该事件并接收数据。

**示例**

**子组件 (**`**ChildComponent.vue**`**)**

```vue
<template>
  <button @click="sendMessage">Click me</button>
</template>

<script>
export default {
  methods: {
    sendMessage() {
      this.$emit("childEvent", "Hello from Child");
    }
  }
};
</script>
```

**父组件 (**`**Parent.vue**`**)**

```vue
<template>
  <div>
    <ChildComponent @childEvent="receiveMessage" />
    <p>Received: {{ message }}</p>
  </div>
</template>

<script>
import ChildComponent from "./ChildComponent.vue";
export default {
  components: { ChildComponent },
  data() {
    return {
      message: ""
    };
  },
  methods: {
    receiveMessage(msg) {
      this.message = msg;
    }
  }
};
</script>
```

> 3、兄弟组件通信

Event Bus（适用于 Vue 2，不推荐 Vue 3）
Event Bus 是一个 Vue 实例，兄弟组件可以通过它发送和监听事件。

示例
创建 eventBus.js

```vue
import Vue from "vue";
export const EventBus = new Vue();
```

**组件 A (**`**ComponentA.vue**`**)**

```vue
<template>
  <button @click="sendMessage">Send to B</button>
</template>

<script>
import { EventBus } from "./eventBus";
export default {
  methods: {
    sendMessage() {
      EventBus.$emit("sendMessage", "Hello from A");
    }
  }
};
</script>
```

**组件 B (**`**ComponentB.vue**`**)**

```vue
<template>
  <p>Received: {{ message }}</p>
</template>

<script>
import { EventBus } from "./eventBus";
export default {
  data() {
    return { message: "" };
  },
  created() {
    EventBus.$on("sendMessage", (msg) => {
      this.message = msg;
    });
  }
};
</script>
```

⚠️ **Vue 3 中不推荐** `**Event Bus**`**，建议使用** `**mitt**` **或 Vuex/Pinia。**

✅ **适用于：无直接父子关系的组件（兄弟组件）通信**。