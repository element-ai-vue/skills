# 安装和配置

## 安装

### 使用包管理器

```bash
# 使用 pnpm（推荐）
pnpm install element-ai-vue

# 使用 npm
npm install element-ai-vue

# 使用 yarn
yarn add element-ai-vue
```

### 版本要求

- **Vue**: ^3.4.0 或更高
- **Node.js**: >= 20

## 基础配置

### 1. 导入样式（必需！）

在你的主入口文件（通常是 `main.ts` 或 `main.js`）中导入 CSS：

```typescript
// main.ts
import { createApp } from 'vue'
import App from './App.vue'

// ⚠️ 重要：必须导入样式文件
import 'element-ai-vue/dist/index.css'

const app = createApp(App)
app.mount('#app')
```

### 2. 按需导入组件

推荐按需导入组件以实现更好的 tree-shaking：

```vue
<script setup lang="ts">
// 导入需要的组件
import { ElAMarkdown, ElABubble, ElASender } from 'element-ai-vue'
</script>

<template>
  <ElAMarkdown :content="content" />
  <ElABubble :content="message" />
  <ElASender v-model="input" />
</template>
```

## 全局配置

### 主题配置

使用 `ElAConfigProvider` 设置全局主题：

```vue
<!-- App.vue -->
<template>
  <ElAConfigProvider theme="dark">
    <router-view />
  </ElAConfigProvider>
</template>

<script setup lang="ts">
import { ElAConfigProvider } from 'element-ai-vue'
</script>
```

### 国际化配置

```vue
<!-- App.vue -->
<template>
  <ElAConfigProvider :locale="locale">
    <router-view />
  </ElAConfigProvider>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAConfigProvider } from 'element-ai-vue'
import zhCn from 'element-ai-vue/es/locale/lang/zh-cn'
import en from 'element-ai-vue/es/locale/lang/en'

const locale = ref(zhCn)  // 或 en

// 切换语言
const switchLanguage = () => {
  locale.value = locale.value === zhCn ? en : zhCn
}
</script>
```

## TypeScript 支持

element-ai-vue 完全使用 TypeScript 编写，提供完整的类型定义。

### 导入类型

```typescript
import type {
  FilesUploadItem,
  FilesUploadErrorParams,
  SenderSelectOption,
} from 'element-ai-vue'

// 使用类型
const fileList = ref<FilesUploadItem[]>([])
```

### 组件类型

```vue
<script setup lang="ts">
import { ref } from 'vue'
import { ElABubble } from 'element-ai-vue'

// 组件实例类型
const bubbleRef = ref<InstanceType<typeof ElABubble>>()

// 调用组件方法
bubbleRef.value?.overTyperwriter()
</script>
```

## Vite 配置

如果使用 Vite，通常不需要额外配置。但如果遇到问题，可以添加：

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  optimizeDeps: {
    include: ['element-ai-vue'],
  },
})
```

## Webpack 配置

如果使用 Webpack，确保正确处理 CSS：

```javascript
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
}
```

## Nuxt 3 配置

在 Nuxt 3 中使用：

```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  css: ['element-ai-vue/dist/index.css'],
  build: {
    transpile: ['element-ai-vue'],
  },
})
```

```vue
<!-- 使用客户端组件 -->
<template>
  <ClientOnly>
    <ElAMarkdown :content="content" />
  </ClientOnly>
</template>

<script setup lang="ts">
import { ElAMarkdown } from 'element-ai-vue'
</script>
```

## 常见问题

### 样式不生效

**问题**：组件显示异常，没有样式。

**解决**：确保在 `main.ts` 中导入了 CSS 文件：
```typescript
import 'element-ai-vue/dist/index.css'
```

### TypeScript 类型错误

**问题**：TypeScript 报告找不到模块。

**解决**：确保安装了 `@types/node` 并在 `tsconfig.json` 中配置：
```json
{
  "compilerOptions": {
    "types": ["node"],
    "moduleResolution": "bundler"
  }
}
```

### Vite 开发服务器慢

**问题**：首次启动或热更新很慢。

**解决**：在 `vite.config.ts` 中添加预构建：
```typescript
export default defineConfig({
  optimizeDeps: {
    include: ['element-ai-vue'],
  },
})
```

### SSR/SSG 问题

**问题**：在 Nuxt 或其他 SSR 框架中报错。

**解决**：使用 `ClientOnly` 包裹组件：
```vue
<template>
  <ClientOnly>
    <ElAMarkdown :content="content" />
  </ClientOnly>
</template>
```

## 浏览器兼容性

- Chrome/Edge 90+
- Firefox 88+
- Safari 14+
- iOS Safari 14+
- Android Chrome 90+

**不支持 IE11**

## CDN 使用

也可以通过 CDN 使用（不推荐用于生产环境）：

```html
<!DOCTYPE html>
<html>
<head>
  <!-- Vue 3 -->
  <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

  <!-- element-ai-vue CSS -->
  <link rel="stylesheet" href="https://unpkg.com/element-ai-vue/dist/index.css">

  <!-- element-ai-vue JS -->
  <script src="https://unpkg.com/element-ai-vue"></script>
</head>
<body>
  <div id="app">
    <el-a-markdown :content="content"></el-a-markdown>
  </div>

  <script>
    const { createApp } = Vue
    const { ElAMarkdown } = ElementAiVue

    createApp({
      components: { ElAMarkdown },
      data() {
        return {
          content: '# Hello World'
        }
      }
    }).mount('#app')
  </script>
</body>
</html>
```

## 下一步

安装完成后，查看以下文档开始使用：

- [ElAMarkdown 组件](./components/markdown.md) - Markdown 渲染
- [ElABubble 组件](./components/bubble.md) - 对话气泡
- [ElASender 组件](./components/sender.md) - 输入框
- [打字机效果](./components/typewriter.md) - 逐字显示动画
- [文件上传](./components/file-upload.md) - 文件上传组件

或查看 [快速开始示例](./examples.md) 了解常见用法。
