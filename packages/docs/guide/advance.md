# 进阶配置

## 指定目录

如果你的 demo 文件和你的 `.md` 文件不是在一个目录下，引用 demo 的相对路径可能比较长，你可以通过 `demoDir` 属性指定 demo 所在的目录以简化引入路径。

例如以下的目录结构:

```
docs
├─ .vitepress
│  └─ config.ts
├─ guide
│  └─ start.md
└─ demos
   ├─ base
   |  └─ demo.html
   ├─ demo.tsx
   └─ demo.vue
```

在 `config.ts` 中添加如下代码：

```ts
import { defineConfig } from 'vitepress';
import { vitepressDemoPlugin } from 'vitepress-demo-plugin';
import path from 'path';

export default defineConfig({
  // other configs...
  markdown: {
    config(md) {
      md.use(vitepressDemoPlugin, {
        demoDir: path.resolve(__dirname, '../demos'), // [!code ++]
      });
    },
  },
});
```

### 配置指定目录前

配置指定目录之前，在 `start.md` 中，我们是通过相对路径引入 demo 的：

```html
<demo
  vue="../demos/demo.vue"
  react="../demos/demo.tsx"
  html="../demos/base/demo.html"
/>
```

### 配置指定目录后

配置指定目录之后，在 `start.md` 中，我们可以简化引入路径为：

```html
<demo vue="demo.vue" react="demo.tsx" html="base/demo.html" />
```

## 展示顺序和默认选中

### 局部配置

当在一个 `<demo />` 组件中同时展示多种语法时，默认的展示顺序为 `vue,react,html` 且默认选中展示顺序中的第一种语法。

你可以通过 `order` 属性来指定 demo 的展示顺序，以及通过 `select` 属性来指定默认选中的 demo。

```html
<demo
  vue="../demos/demo.vue"
  react="../demos/demo.tsx"
  html="../demos/demo.html"
  order="html,react,vue"
  select="react"
/>
```

其对应的渲染效果如下：

<demo
  vue="demo.vue"
  react="demo.tsx"
  html="demo.html"
  order="html,react,vue"
  select="react"
/>

### 全局配置

如果你想对全局的 `<demo />` 组件生效，在 `.vitepress/config.ts` 中添加 `tabs` 配置，例如：

```ts
import { defineConfig } from 'vitepress';
import { vitepressDemoPlugin } from 'vitepress-demo-plugin';
import path from 'path';

export default defineConfig({
  // other configs...
  markdown: {
    config(md) {
      md.use(vitepressDemoPlugin, {
        demoDir: path.resolve(__dirname, '../demos'),
        tabs: { // [!code ++]
          order: 'html,react,vue', // [!code ++]
          select: 'react', // [!code ++]
        }, // [!code ++]
      });
    },
  },
});
```

## 展示多文件代码

### 数组方式

如果你的 demo 中包含多个文件，你可以通过 `vueFiles/reactFiles/htmlFiles` 属性来指定需要展示对应类型 demo 的文件及代码。例如：

```html
<demo
  vue="../demos/multiple.vue"
  :vueFiles="['../demos/multiple.vue', '../demos/constant/students.ts']"
/>
```

其对应的渲染效果如下：

<demo
  vue="../demos/multiple.vue"
  :vueFiles="['../demos/multiple.vue', '../demos/constant/students.ts']"
/>

### 对象方式

默认情况下，`vitepress-demo-plugin` 多文件展示时会取文件路径的 `basename` 作为文件名，你也可以通过 `Object` 类型的 `vueFiles/reactFiles/htmlFiles` 属性来指定文件名。例如，你想通过 `constant/students.ts` 来体现出文件所在的目录层级，你可以通过如下方式：

```html
<demo
  vue="../demos/multiple.vue"
  :vueFiles="{
    'multiple.vue': '../demos/multiple.vue',
    'constant/students.ts': '../demos/constant/students.ts',
  }"
/>
```

其对应的渲染效果如下：

<demo
  vue="../demos/multiple.vue"
  :vueFiles="{
    'multiple.vue': '../demos/multiple.vue',
    'constant/students.ts': '../demos/constant/students.ts',
  }"
/>

### 自定义展示

你也可以通过 `vueFiles/reactFiles/htmlFiles` 属性去完全自定义要展示的文件及代码，例如你要展示一份代码的 `typescript` 和 `javascript` 的两种格式，你可以通过如下方式：

```html
<demo
  react="../demos/demo.tsx"
  :reactFiles="{
    'Typescript': '../demos/demo.tsx',
    'Javascript': '../demos/demo.jsx',
  }"
/>
```

其对应的渲染效果如下：

<demo
  react="../demos/demo.tsx"
  :reactFiles="{
    'Typescript': '../demos/demo.tsx',
    'Javascript': '../demos/demo.jsx',
  }"
/>

### 🚨 注意事项

`vitepress-demo-plugin` 内部会将 `vueFiles/reactFiles/htmlFiles` 作为一个类型为 `string[] | Record<string, string>` 的字符串来处理，所以你只能直接声明  `vueFiles/reactFiles/htmlFiles` 的值，不能通过引用变量的方式来代替。

- ❌ 错误示例

  ```html
  const vueFiles = ['../demos/multiple.vue', '../demos/constant/students.ts'];

  <demo
    vue="../demos/multiple.vue"
    :vueFiles="vueFiles"
  />
  ```

- ✅ 正确示例

  ```html
  <demo
    vue="../demos/multiple.vue"
    :vueFiles="['../demos/multiple.vue', '../demos/constant/students.ts']"
  />
  ```