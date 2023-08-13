> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [juejin.cn](https://juejin.cn/post/7264395683125510203)

在前端开发中，Vue 一直以其简单、高效的框架而备受开发者青睐。然而，随着 React 在市场上的流行，许多开发者开始对 JSX（JavaScript XML）这种声明式编程风格产生兴趣。本文将探讨 JSX 在 Vue3 中的应用，并对其是否成为 Vue3 前端开发的未来进行论证。

Vue3 与 JSX 的爱恨情仇
----------------

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/626e3b8c5db8499e8ceb17199b0010db~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

在开始之前，我们先来了解一下 Vue 本身的模版语法和 JSX 分别是什么。

### Vue3 模版语法

Vue3 模版语法是 Vue.js 中常用的一种声明式模板语法，使用 HTML 语法来描述视图。在模板语法中，可以通过插值、指令和事件绑定等方式来将数据与视图关联起来。这是其简单易用和上手快的主要原因之一。

```
<template>
  <div>
    <h1>{{ title }}</h1>
    <p v-if="showText">{{ text }}</p>
    <ul>
      <li v-for="item in list" :key="item.id">{{ item.name }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: 'Vue3 Template Syntax',
      text: 'This is a demo text.',
      showText: true,
      list: [
        { id: 1, name: 'Item 1' },
        { id: 2, name: 'Item 2' },
        { id: 3, name: 'Item 3' },
      ],
    };
  },
};
</script>


```

Vue3 的模板语法使用双花括号（`{{ }}`）进行数据插值，使用`v-if`和`v-for`等指令处理条件和循环逻辑。

### 什么是 JSX？

JSX 是一种 JavaScript 的语法扩展，它允许在 JavaScript 代码中编写类似于 XML 的结构。React 是第一个广泛使用 JSX 的框架，它将组件的结构和逻辑封装在 JSX 中，用于描述 UI 组件的层次结构。随着 React 的成功，也随着时间的推移，JSX 逐渐成为了一种通用的模式，被许多其他框架和库所采用支持。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7d4a54c75d3a47b5a0f636b31e7c5d19~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

React 示例：

```
import React, { useState } from 'react';

function JSXComponent() {
  const [title, setTitle] = useState('JSX in React');
  const [showText, setShowText] = useState(true);
  const list = [
    { id: 1, name: 'Item 1' },
    { id: 2, name: 'Item 2' },
    { id: 3, name: 'Item 3' },
  ];

  return (
    <div>
      <h1>{title}</h1>
      {showText && <p>This is a demo text.</p>}
      <ul>
        {list.map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default JSXComponent;


```

Vue3 示例：

```
import { defineComponent, ref } from 'vue';

const JSXComponent = defineComponent({
  setup() {
    const title = ref('JSX in Vue3');
    const showText = ref(true);
    const list = [
      { id: 1, name: 'Item 1' },
      { id: 2, name: 'Item 2' },
      { id: 3, name: 'Item 3' },
    ];

    return {
      title,
      showText,
      list,
    };
  },
  render() {
    return (
      <div>
        <h1>{this.title}</h1>
        {this.showText && <p>This is a demo text.</p>}
        <ul>
          {this.list.map((item) => (
            <li key={item.id}>{item.name}</li>
          ))}
        </ul>
      </div>
    );
  },
});

export default JSXComponent;


```

从上面不难看出，在 JSX 中，我们可以直接使用 JavaScript 表达式（如`{title}`），也可以使用条件语句（如`{showText && <p>This is a demo text.</p>}`）和数组的`map`方法来处理循环逻辑。

这些只是举例，有很多的 JavaScript 语法和方法等，都可以在这里使用。总之，使用 JSX 开发，可以很大程度上利用好 JavaScript，开发更加方便。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ad02a6caa794406af3dbd336d14d515~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

### 如何配置 JSX？

在 Vue3 中，我们可以通过 Vue 官方提供的项目脚手架工具 create-vue 来新建一个支持 JSX 的项目。首先，确保你安装了最新版本的 [Node.js](https://link.juejin.cn?target=https%3A%2F%2Fnodejs.org%2F "https://nodejs.org/")（我用的是 16+ 版本），然后执行以下命令：

```
npm init vue@latest


```

这个命令将会安装并执行 [create-vue](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fvuejs%2Fcreate-vue "https://github.com/vuejs/create-vue") 工具。在执行过程中，你会看到一些可选的功能配置提示，其中会有如下内容：

```
Vue.js - The Progressive JavaScript Framework

✔ Project name: … vue-project
✔ Add TypeScript? … No / Yes
✔ Add JSX Support? … No / Yes
✔ Add Vue Router for Single Page Application development? … No / Yes
✔ Add Pinia for state management? … No / Yes
✔ Add Vitest for Unit Testing? … No / Yes
✔ Add an End-to-End Testing Solution? › No
✔ Add ESLint for code quality? … No / Yes
✔ Add Prettier for code formatting? … No / Yes

Scaffolding project in ...


```

执行到`✔ Add JSX Support?`时选择`Yes`，这样 JSX 就会自动安装。如果不确定是否要开启某个功能，你可以直接按下回车键选择 `No`。

当然，你也可以在已有的 Vue 项目中配置 JSX。

在已有项目中配置 JSX，首先需要安装相关依赖：

```
npm install --save-dev @vue/babel-plugin-jsx


```

然后，在项目的`vite.config.ts`文件中进行配置。具体的配置内容如下图所示：

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6ba5c1d3b8b542d3b4c25e3f4739c2bb~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

配置完成后，现在我们就可以在项目中使用 JSX 语法来开发了。这样，我们可以根据具体的场景选择使用 Vue 模板或 JSX 语法。

总的来说，配置 JSX 是非常简单的，通过上述步骤，我们可以轻松地在 Vue 项目中使用 JSX，发挥其简洁、易读的优势，让我们的代码更加优雅和高效。

模板语法 vs JSX
-----------

现在，我们来对比一些具体的代码示例，看看 Vue3 模板语法和 JSX 之间的差异。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4235e6a73947457e8125af4788be8209~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

### 1. 数据插值

Vue3 模板语法使用双花括号`{{}}`进行数据插值，而 JSX 使用花括号`{}`。

模板示例:

```
<template>
  <p>{{ message }}</p>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const message = ref('Hello, JSX!');
    return { message };
  },
};
</script>


```

JSX 示例：

```
import { defineComponent } from 'vue'; 

const DynamicData = defineComponent({
  setup() {
    const message = ref('Hello, JSX!');
    return { message };
  },
  render() {
    return <p>{this.message}</p>;
  },
});


```

### 2. 条件渲染

在 Vue3 中，我们可以使用`v-if`指令进行条件渲染，而在 JSX 中，我们使用 JavaScript 的条件表达式。

模板示例:

```
<template>
  <div>
    <p v-if="showContent">Content is visible.</p>
    <p v-else>Content is hidden.</p>
    <button @click="toggleContent">Toggle</button>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const showContent = ref(true);

    function toggleContent() {
      showContent.value = !showContent.value;
    }

    return { showContent, toggleContent };
  },
};
</script>


```

JSX 示例：

```
import { defineComponent } from 'vue'; 

const ConditionalRender = defineComponent({
  setup() {
    const showContent = ref(true);
    return { showContent };
  },
  render() {
    return (
      <div>
        {this.showContent ? <p>Content is visible.</p> : <p>Content is hidden.</p>}
        <button onClick={() => this.showContent = !this.showContent}>Toggle</button>
      </div>
    );
  },
}};


```

### 3. 列表渲染

在 Vue3 中，我们使用`v-for`指令来处理列表渲染，而在 JSX 中，我们使用 JavaScript 的`map`方法。

模板示例：

```
<template>
  <ul>
    <li v-for="item in items" :key="item">{{ item }}</li>
  </ul>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const items = ref(['Apple', 'Banana', 'Orange']);
    return { items };
  },
};
</script>


```

JSX 示例：

```
import { defineComponent } from 'vue'; 

const ListRendering = defineComponent({
  setup() {
    const items = ref(['Apple', 'Banana', 'Orange']);
    return { items };
  },
  render() {
    return (
      <ul>
        {this.items.map(item => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    );
  },
});


```

模板语法和 JSX 的优劣势
--------------

接下来，我们将对 Vue 模板和 JSX 进行对比，从多个方面分析它们的优势与劣势。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cbfed75ab43546bea36538de4c7e22c3~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

### a. 语法简洁性

Vue 模板使用 HTML 语法，更加接近常规 HTML 结构，因此对于前端开发者来说比较容易上手。然而，**随着项目的复杂性增加，模板中可能会包含大量的指令和插值，导致代码变得冗长。** 例如，条件渲染、循环遍历等情况都需要使用 Vue 特定的指令。**相比之下，JSX 在 JavaScript 语法的基础上，使用类似 XML 的结构，使得代码更加紧凑和直观。**

模板示例：

```
<template>
  <div>
    <h1 v-if="showTitle">{{ title }}</h1>
    <ul>
      <li v-for="item in items" :key="item.id">{{ item.name }}</li>
    </ul>
  </div>
</template>


```

JSX 示例：

```
const MyComponent = () => {
  return (
    <div>
      {showTitle && <h1>{title}</h1>}
      <ul>
        {items.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
};


```

从上面的对比可以看出，JSX 语法更加简洁，尤其在`条件渲染`和`循环遍历`方面更加灵活。

### b. 组件化开发

Vue.js 本身就支持组件化开发，但是在 Vue 模板中，组件的定义与使用是通过特定的 HTML 标签和 Vue 指令实现的。这种方式虽然简单易懂，但是在代码重用和维护方面可能会有一些限制。相比之下，JSX 在 React 中的组件化开发非常强大，组件可以直接在 JavaScript 中定义，并且支持更加灵活的组合方式。

模板示例：

```
<template>
  <div>
    <my-component :prop1="value1" :prop2="value2" />
  </div>
</template>


```

JSX 示例：

```
const MyComponentWrapper = () => {
  return (
    <div>
      <MyComponent prop1={value1} prop2={value2} />
    </div>
  );
};


```

从上面的对比可以看出，`JSX 允许在JavaScript中直接定义组件`，并且组件的传参更加直观。

### c. 类型检查与 IDE 支持

由于 Vue 模板使用了特定的指令和 HTML 语法，IDE 对于代码的支持可能相对有限。而 JSX 是 JavaScript 的扩展语法，因此可以与类型检查工具（如 TypeScript）完美结合，同时得到更好的 IDE 支持，例如自动补全、代码跳转等。

### d. 可扩展性

Vue 模板的语法是固定的，不能随意扩展。而 JSX 作为 JavaScript 的一部分，不需要额外的指令或语法，可以通过编写函数和组件来扩展其语法，也使得处理逻辑、计算和条件渲染变得更加灵活和方便。

### e. 生态系统的互通性

JSX 作为 React 的核心特性，拥有庞大的社区和丰富的生态系统，为开发者提供了海量的工具和库。

同时 JSX 在多个框架中得到了广泛支持，开发者们可以更轻松地在不同的项目和技术栈之间切换，而无需学习不同的模板语法。如文章上面 “如何配置 JSX？” 中对已有项目的配置，将 JSX 作为插件写到 Vue Plugin 即可配置完成。

模板语法和 JSX 的适用场景
---------------

Vue3 的模板语法和 JSX 各有优劣，因此它们在不同的场景下有不同的适用性。

### 模板语法的适用场景

*   快速原型开发：对于快速构建原型或小型项目，Vue3 的模板语法非常适合，可以快速上手和开发。
*   HTML/CSS 开发者：对于更擅长 HTML/CSS 的开发者，Vue3 的模板语法更加亲切。

### JSX 的适用场景

*   复杂交互：对于具有复杂交互逻辑的项目，使用 JSX 可以更灵活地处理各种条件和事件。
*   React 生态系统：如果项目需要使用丰富的 React 生态系统，或者团队已经熟悉了 React 和 JSX，那么继续使用 JSX 是合理的选择。

总结
--

无论是从开发体验、技术生态还是未来趋势来看，JSX 都有望成为 Vue3 前端开发的未来。它使得组件的模板结构更加清晰、声明性，提供了更强大的 JavaScript 表达能力，同时也增强了与其他技术栈的互通性。虽然传统的 Vue2 模板语法在一定程度上仍然适用，但通过引入 JSX，Vue3 在前端开发领域拥有更广阔的发展前景。开发者们可以更加便利地构建复杂的交互界面，同时又能享受到 Vue3 带来的性能优势。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5f247d9ab05f4986aa31acc92e3bb22d~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp?)

**JSX 是 Vue 前端开发的未来吗？** 请各位小伙伴做出自己的思考！欢迎在评论区一起讨论。