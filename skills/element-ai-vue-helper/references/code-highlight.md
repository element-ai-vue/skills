# ElACodeHighlight 组件

独立的代码高亮组件，使用 Shiki 实现语法高亮。

## 组件导入

```typescript
import { ElACodeHighlight } from 'element-ai-vue'
```

## Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| content | 代码内容 | `string` | `''` |
| language | 编程语言 | `string` | `'javascript'` |
| theme | 主题 | `'light' \| 'dark'` | `'light'` |
| showLineNumbers | 是否显示行号 | `boolean` | `true` |
| highlightLines | 高亮指定行（如 "1,3-5,7"） | `string` | `''` |
| fileName | 文件名 | `string` | `''` |

## Slots

| 插槽名 | 说明 | 参数 |
|--------|------|------|
| header | 自定义头部工具栏 | `{ language, content, fileName }` |

## 基础用法

### 1. 简单代码高亮

```vue
<template>
  <ElACodeHighlight
    :content="code"
    language="javascript"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElACodeHighlight } from 'element-ai-vue'

const code = ref(`
function hello() {
  console.log('Hello World!')
}
`)
</script>
```

### 2. 显示行号

```vue
<template>
  <ElACodeHighlight
    :content="code"
    language="python"
    :show-line-numbers="true"
  />
</template>

<script setup lang="ts">
const code = ref(`
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
`)
</script>
```

### 3. 高亮指定行

```vue
<template>
  <ElACodeHighlight
    :content="code"
    language="typescript"
    highlight-lines="2,4-6"
  />
</template>

<script setup lang="ts">
const code = ref(`
interface User {
  name: string  // 高亮
  age: number
  email: string  // 高亮
  phone: string  // 高亮
  address: string  // 高亮
}
`)
</script>
```

### 4. 深色主题

```vue
<template>
  <div style="background: #1e1e1e; padding: 20px;">
    <ElACodeHighlight
      :content="code"
      language="vue"
      theme="dark"
    />
  </div>
</template>
```

### 5. 显示文件名

```vue
<template>
  <ElACodeHighlight
    :content="code"
    language="javascript"
    file-name="hello.js"
  />
</template>

<script setup lang="ts">
const code = ref(`
export function hello(name) {
  return \`Hello, \${name}!\`
}
`)
</script>
```

## 自定义头部工具栏

### 1. 添加复制按钮

```vue
<template>
  <ElACodeHighlight
    :content="code"
    language="javascript"
  >
    <template #header="{ content, language }">
      <div class="code-header">
        <span class="language">{{ language }}</span>
        <button @click="copyCode(content)" class="copy-btn">
          {{ copied ? '已复制' : '复制代码' }}
        </button>
      </div>
    </template>
  </ElACodeHighlight>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const code = ref(`console.log('Hello')`)
const copied = ref(false)

const copyCode = (content: string) => {
  navigator.clipboard.writeText(content)
  copied.value = true
  setTimeout(() => {
    copied.value = false
  }, 2000)
}
</script>

<style scoped>
.code-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 16px;
  background: #f5f5f5;
  border-bottom: 1px solid #e0e0e0;
}

.language {
  font-size: 12px;
  color: #666;
  text-transform: uppercase;
  font-weight: 500;
}

.copy-btn {
  padding: 4px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 12px;
  transition: all 0.2s;
}

.copy-btn:hover {
  background: #f0f0f0;
}
</style>
```

### 2. 添加运行按钮

```vue
<template>
  <ElACodeHighlight
    :content="code"
    language="javascript"
  >
    <template #header="{ content }">
      <div class="code-header">
        <span class="file-name">script.js</span>
        <div class="actions">
          <button @click="copyCode(content)">复制</button>
          <button @click="runCode(content)">运行</button>
        </div>
      </div>
    </template>
  </ElACodeHighlight>

  <div v-if="output" class="output">
    <strong>输出：</strong>
    <pre>{{ output }}</pre>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const code = ref(`console.log('Hello World!')`)
const output = ref('')

const copyCode = (content: string) => {
  navigator.clipboard.writeText(content)
}

const runCode = (content: string) => {
  try {
    // 捕获 console.log
    const logs: string[] = []
    const originalLog = console.log
    console.log = (...args) => {
      logs.push(args.join(' '))
    }

    // 执行代码
    eval(content)

    // 恢复 console.log
    console.log = originalLog

    output.value = logs.join('\n')
  } catch (error) {
    output.value = `错误: ${error.message}`
  }
}
</script>

<style scoped>
.code-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 16px;
  background: #2d2d2d;
  color: white;
}

.file-name {
  font-size: 13px;
  font-family: monospace;
}

.actions {
  display: flex;
  gap: 8px;
}

.actions button {
  padding: 4px 12px;
  border: 1px solid #555;
  border-radius: 4px;
  background: #3d3d3d;
  color: white;
  cursor: pointer;
  font-size: 12px;
}

.actions button:hover {
  background: #4d4d4d;
}

.output {
  margin-top: 16px;
  padding: 12px;
  background: #f9f9f9;
  border: 1px solid #e0e0e0;
  border-radius: 4px;
}

.output pre {
  margin: 8px 0 0;
  font-family: monospace;
  font-size: 13px;
}
</style>
```

## 支持的语言

Shiki 支持 100+ 编程语言，包括但不限于：

### 常用语言
- `javascript`, `typescript`, `jsx`, `tsx`
- `vue`, `react`, `svelte`
- `html`, `css`, `scss`, `less`
- `json`, `yaml`, `toml`
- `markdown`

### 后端语言
- `python`, `java`, `c`, `cpp`, `csharp`
- `go`, `rust`, `php`, `ruby`
- `kotlin`, `swift`, `dart`

### 脚本语言
- `bash`, `shell`, `powershell`
- `lua`, `perl`

### 数据库
- `sql`, `mysql`, `postgresql`
- `mongodb`

### 其他
- `dockerfile`, `nginx`
- `graphql`, `proto`
- `latex`, `r`

## 完整示例

```vue
<template>
  <div class="code-viewer">
    <!-- 语言选择 -->
    <div class="controls">
      <select v-model="selectedLanguage">
        <option value="javascript">JavaScript</option>
        <option value="typescript">TypeScript</option>
        <option value="python">Python</option>
        <option value="vue">Vue</option>
      </select>

      <label>
        <input type="checkbox" v-model="showLineNumbers" />
        显示行号
      </label>

      <label>
        <input type="checkbox" v-model="isDark" />
        深色主题
      </label>
    </div>

    <!-- 代码展示 -->
    <ElACodeHighlight
      :content="codeExamples[selectedLanguage]"
      :language="selectedLanguage"
      :theme="isDark ? 'dark' : 'light'"
      :show-line-numbers="showLineNumbers"
      :file-name="fileNames[selectedLanguage]"
    >
      <template #header="{ content, language }">
        <div class="header">
          <div class="left">
            <span class="dot red"></span>
            <span class="dot yellow"></span>
            <span class="dot green"></span>
            <span class="file-name">
              {{ fileNames[language] }}
            </span>
          </div>
          <div class="right">
            <button @click="copyCode(content)" class="icon-btn">
              📋 {{ copied ? '已复制' : '复制' }}
            </button>
            <button @click="downloadCode(content, language)" class="icon-btn">
              💾 下载
            </button>
          </div>
        </div>
      </template>
    </ElACodeHighlight>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElACodeHighlight } from 'element-ai-vue'

const selectedLanguage = ref('javascript')
const showLineNumbers = ref(true)
const isDark = ref(false)
const copied = ref(false)

const codeExamples = {
  javascript: `function fibonacci(n) {
  if (n <= 1) return n
  return fibonacci(n - 1) + fibonacci(n - 2)
}

console.log(fibonacci(10))`,

  typescript: `interface User {
  id: number
  name: string
  email: string
}

function greet(user: User): string {
  return \`Hello, \${user.name}!\`
}`,

  python: `def quicksort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quicksort(left) + middle + quicksort(right)`,

  vue: `<template>
  <div class="hello">
    <h1>{{ message }}</h1>
    <button @click="count++">
      Count: {{ count }}
    </button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const message = ref('Hello Vue!')
const count = ref(0)
</script>`,
}

const fileNames = {
  javascript: 'fibonacci.js',
  typescript: 'user.ts',
  python: 'quicksort.py',
  vue: 'HelloWorld.vue',
}

const copyCode = (content: string) => {
  navigator.clipboard.writeText(content)
  copied.value = true
  setTimeout(() => {
    copied.value = false
  }, 2000)
}

const downloadCode = (content: string, language: string) => {
  const blob = new Blob([content], { type: 'text/plain' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = fileNames[language]
  a.click()
  URL.revokeObjectURL(url)
}
</script>

<style scoped>
.code-viewer {
  max-width: 900px;
  margin: 0 auto;
}

.controls {
  display: flex;
  gap: 16px;
  align-items: center;
  margin-bottom: 16px;
  padding: 12px;
  background: #f5f5f5;
  border-radius: 8px;
}

.controls select {
  padding: 6px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
}

.controls label {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 14px;
  cursor: pointer;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 16px;
  background: #2d2d2d;
  color: white;
}

.left {
  display: flex;
  align-items: center;
  gap: 8px;
}

.dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
}

.dot.red { background: #ff5f56; }
.dot.yellow { background: #ffbd2e; }
.dot.green { background: #27c93f; }

.file-name {
  margin-left: 8px;
  font-size: 13px;
  font-family: monospace;
}

.right {
  display: flex;
  gap: 8px;
}

.icon-btn {
  padding: 4px 12px;
  border: 1px solid #555;
  border-radius: 4px;
  background: #3d3d3d;
  color: white;
  cursor: pointer;
  font-size: 12px;
  transition: background 0.2s;
}

.icon-btn:hover {
  background: #4d4d4d;
}
</style>
```

## 使用场景

### 1. 文档中的代码示例

```vue
<ElACodeHighlight
  :content="exampleCode"
  language="javascript"
  file-name="example.js"
/>
```

### 2. 代码对比

```vue
<div class="code-comparison">
  <div class="before">
    <h4>修改前</h4>
    <ElACodeHighlight :content="beforeCode" language="javascript" />
  </div>
  <div class="after">
    <h4>修改后</h4>
    <ElACodeHighlight :content="afterCode" language="javascript" />
  </div>
</div>
```

### 3. 代码片段展示

```vue
<ElACodeHighlight
  :content="snippet"
  language="vue"
  :show-line-numbers="false"
/>
```

## 注意事项

1. **CSS 导入**：必须导入样式
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

2. **语言识别**：
   - 语言名称不区分大小写
   - 支持常见别名（如 `js` 等同于 `javascript`）

3. **性能考虑**：
   - Shiki 在首次加载时会加载语法定义
   - 大量代码块建议使用虚拟滚动

4. **行号显示**：
   - 默认显示行号
   - 可以通过 `showLineNumbers` 控制

5. **主题**：
   - 支持 `light` 和 `dark` 两种主题
   - 主题会影响代码高亮颜色
