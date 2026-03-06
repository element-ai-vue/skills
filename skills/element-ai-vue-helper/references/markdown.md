# ElAMarkdown 组件

Markdown 渲染组件，基于 `unified` 和 `remark` 生态构建，支持代码高亮、数学公式、Mermaid 图表等。

## 组件导入

```typescript
import { ElAMarkdown } from 'element-ai-vue'
```

## Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| content | Markdown 内容 | `string` | `''` |
| theme | 主题模式 | `'light' \| 'dark'` | `'light'` |
| remarkPlugins | 自定义 remark 插件 | `MiddlewarePluginItem[]` | `[]` |
| codeHighlightProps | 传递给 CodeHighlight 组件的属性 | `CodeHighlightPropsType` | `{}` |
| codeMermaidProps | 传递给 CodeMermaid 组件的属性 | `CodeMermaidPropsType` | `{}` |

## Slots

| 插槽名 | 说明 | 参数 |
|--------|------|------|
| code | 自定义代码块渲染 | `{ content, language, theme, ...codeHighlightProps }` |
| mermaid | 自定义 Mermaid 代码块渲染 | `{ content, language, theme, ...codeMermaidProps }` |
| code-highlight-header | 自定义代码块头部 | 参见 CodeHighlight 组件 |
| code-mermaid-toolbar | 自定义 Mermaid 工具栏 | 参见 CodeMermaid 组件 |
| code-mermaid-fullscreen-toolbar | 自定义 Mermaid 全屏工具栏 | 参见 CodeMermaid 组件 |

## 功能特性

### 1. 基础 Markdown 支持

支持所有标准 Markdown 语法：

```vue
<template>
  <ElAMarkdown :content="content" />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAMarkdown } from 'element-ai-vue'

const content = ref(`
# 一级标题
## 二级标题
### 三级标题

**粗体文本** *斜体文本* ~~删除线~~

- 无序列表项 1
- 无序列表项 2
  - 子列表项 2.1

1. 有序列表项 1
2. 有序列表项 2

[链接文本](https://example.com)

![图片](https://example.com/image.png)

> 引用文本
> 可以多行

| 表头1 | 表头2 |
|-------|-------|
| 单元格1 | 单元格2 |
`)
</script>
```

### 2. 代码高亮

使用 Shiki 实现代码高亮，支持 100+ 编程语言：

```vue
<template>
  <ElAMarkdown :content="codeContent" />
</template>

<script setup lang="ts">
const codeContent = ref(`
\`\`\`javascript
// JavaScript 代码
function hello() {
  console.log('Hello World!')
}
\`\`\`

\`\`\`python
# Python 代码
def hello():
    print("Hello World!")
\`\`\`

\`\`\`typescript
// TypeScript 代码
interface User {
  name: string
  age: number
}
\`\`\`
`)
</script>
```

### 3. 数学公式（KaTeX）

支持行内和块级数学公式：

```vue
<template>
  <ElAMarkdown :content="mathContent" />
</template>

<script setup lang="ts">
const mathContent = ref(`
## 行内公式

勾股定理：$ a^2 + b^2 = c^2 $

欧拉公式：$ e^{i\\pi} + 1 = 0 $

## 块级公式

$$
\\int_a^b f(x)dx = F(b) - F(a)
$$

$$
\\frac{-b \\pm \\sqrt{b^2-4ac}}{2a}
$$

$$
\\begin{pmatrix}
a & b \\\\
c & d
\\end{pmatrix}
$$
`)
</script>
```

### 4. Mermaid 图表

支持流程图、时序图等：

```vue
<template>
  <ElAMarkdown :content="mermaidContent" />
</template>

<script setup lang="ts">
const mermaidContent = ref(`
\`\`\`mermaid
flowchart TD
    A[开始] --> B{判断条件}
    B -->|是| C[执行操作1]
    B -->|否| D[执行操作2]
    C --> E[结束]
    D --> E
\`\`\`

\`\`\`mermaid
sequenceDiagram
    participant 用户
    participant 服务器
    用户->>服务器: 发送请求
    服务器->>用户: 返回响应
\`\`\`
`)
</script>
```

### 5. 自定义代码块工具栏

```vue
<template>
  <ElAMarkdown :content="content">
    <template #code-highlight-header="{ language, content }">
      <div class="custom-toolbar">
        <span>{{ language }}</span>
        <button @click="copyCode(content)">复制</button>
      </div>
    </template>
  </ElAMarkdown>
</template>

<script setup lang="ts">
const copyCode = (code: string) => {
  navigator.clipboard.writeText(code)
}
</script>
```

### 6. 自定义代码块渲染

使用 ECharts 等第三方库：

```vue
<template>
  <ElAMarkdown :content="content">
    <template #code="{ content, language }">
      <div v-if="language === 'echarts'">
        <EchartsComponent :option="JSON.parse(content)" />
      </div>
      <ElACodeHighlight
        v-else
        :content="content"
        :language="language"
      />
    </template>
  </ElAMarkdown>
</template>
```

## 与打字机效果结合

```vue
<template>
  <div>
    <button @click="start">开始</button>
    <button @click="paused">暂停</button>
    <button @click="stop">停止</button>

    <ElAMarkdown :content="content" />
  </div>
</template>

<script setup lang="ts">
import { ElAMarkdown, useTyperwriter } from 'element-ai-vue'

const { start, paused, stop, setText, content } = useTyperwriter({
  interval: 50, // 每个字符的毫秒数
})

const fullText = `
# AI 回复

这是一个**流式**输出的 Markdown 内容。

\`\`\`javascript
console.log('代码也会逐字显示')
\`\`\`

数学公式：$ E = mc^2 $
`

setText(fullText)
</script>
```

## 主题切换

```vue
<template>
  <div>
    <button @click="theme = 'light'">浅色</button>
    <button @click="theme = 'dark'">深色</button>

    <ElAMarkdown :content="content" :theme="theme" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const theme = ref<'light' | 'dark'>('light')
</script>
```

## 常见用例

### 1. AI 对话回复展示
```vue
<ElAMarkdown
  :content="aiResponse"
  :theme="isDark ? 'dark' : 'light'"
/>
```

### 2. 文档渲染
```vue
<ElAMarkdown :content="documentation" />
```

### 3. 帮助文本
```vue
<ElAMarkdown :content="helpText" />
```

## 注意事项

1. **CSS 导入**：必须在 main.ts 中导入样式：
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

2. **代码高亮语言**：支持的语言包括但不限于：
   - JavaScript, TypeScript, Vue, React
   - Python, Java, C++, C#, Go, Rust
   - HTML, CSS, SCSS, JSON, YAML
   - SQL, Shell, Bash
   - 等 100+ 种语言

3. **数学公式语法**：
   - 行内公式：`$ formula $` 或 `\\( formula \\)`
   - 块级公式：`$$ formula $$` 或 `\\[ formula \\]`

4. **Mermaid 图表类型**：
   - flowchart（流程图）
   - sequenceDiagram（时序图）
   - classDiagram（类图）
   - stateDiagram（状态图）
   - erDiagram（ER 图）
   - gantt（甘特图）
   - pie（饼图）

## 完整示例

```vue
<template>
  <div class="markdown-container">
    <ElAMarkdown
      :content="content"
      :theme="theme"
    >
      <template #code-highlight-header="{ language }">
        <div class="code-header">
          <span class="language">{{ language }}</span>
          <button class="copy-btn" @click="handleCopy">
            复制代码
          </button>
        </div>
      </template>
    </ElAMarkdown>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAMarkdown } from 'element-ai-vue'

const theme = ref<'light' | 'dark'>('light')

const content = ref(`
# 完整示例

这是一个包含多种元素的 Markdown 文档。

## 代码示例

\`\`\`javascript
function fibonacci(n) {
  if (n <= 1) return n
  return fibonacci(n - 1) + fibonacci(n - 2)
}
\`\`\`

## 数学公式

斐波那契数列的通项公式：

$$
F_n = \\frac{1}{\\sqrt{5}} \\left[ \\left(\\frac{1+\\sqrt{5}}{2}\\right)^n - \\left(\\frac{1-\\sqrt{5}}{2}\\right)^n \\right]
$$

## 流程图

\`\`\`mermaid
flowchart LR
    A[输入n] --> B{n <= 1?}
    B -->|是| C[返回n]
    B -->|否| D[递归计算]
    D --> E[返回结果]
\`\`\`

## 表格

| n | F(n) |
|---|------|
| 0 | 0    |
| 1 | 1    |
| 2 | 1    |
| 3 | 2    |
| 4 | 3    |
| 5 | 5    |
`)

const handleCopy = () => {
  console.log('复制代码')
}
</script>

<style scoped>
.markdown-container {
  padding: 20px;
  max-width: 800px;
  margin: 0 auto;
}

.code-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 12px;
  background: #f5f5f5;
  border-bottom: 1px solid #e0e0e0;
}

.language {
  font-size: 12px;
  color: #666;
  text-transform: uppercase;
}

.copy-btn {
  padding: 4px 12px;
  font-size: 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
}

.copy-btn:hover {
  background: #f0f0f0;
}
</style>
```
