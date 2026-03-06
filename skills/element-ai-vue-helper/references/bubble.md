# ElABubble 组件

对话气泡组件，用于展示聊天消息，支持打字机效果、Markdown 渲染、加载状态等。

## 组件导入

```typescript
import { ElABubble } from 'element-ai-vue'
```

## Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| placement | 气泡位置，start 表示左侧（AI），end 表示右侧（用户） | `'start' \| 'end'` | `'start'` |
| content | 气泡内容 | `string` | `''` |
| typing | 是否启用打字机效果 | `boolean` | `false` |
| typingOver | 内容输出完成标识，置为 true 时打字机会自动完成 | `boolean` | `true` |
| loading | 是否显示加载状态（三个跳动的点） | `boolean` | `false` |
| isMarkdown | 是否将内容渲染为 Markdown | `boolean` | `false` |
| variant | 气泡变体样式 | `'filled' \| 'outlined' \| 'shadow' \| 'borderless'` | `'filled'` |
| shape | 气泡形状 | `'default' \| 'round' \| 'corner'` | `'default'` |

## Slots

| 插槽名 | 说明 | 作用域参数 |
|--------|------|-----------|
| default | 自定义气泡内容，覆盖 content 属性 | `content: string` |
| avatar | 自定义头像区域 | - |
| header | 自定义头部区域（气泡上方） | - |
| footer | 自定义底部区域（气泡下方） | - |

## Exposes

| 方法名 | 说明 | 类型 |
|--------|------|------|
| overTyperwriter | 立即完成打字机效果 | `() => void` |

## 基础用法

### 1. 用户消息气泡（右侧）

```vue
<template>
  <ElABubble
    placement="end"
    content="你好，我是用户"
  />
</template>

<script setup lang="ts">
import { ElABubble } from 'element-ai-vue'
</script>
```

### 2. AI 消息气泡（左侧）

```vue
<template>
  <ElABubble
    placement="start"
    content="你好，我是 AI 助手"
  />
</template>
```

### 3. 带加载状态

```vue
<template>
  <ElABubble
    placement="start"
    content=""
    :loading="true"
  />
</template>
```

### 4. 打字机效果

```vue
<template>
  <ElABubble
    placement="start"
    :content="content"
    :typing="true"
    :loading="false"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue'

const content = ref('')

// 模拟逐字输出
const simulateTyping = async () => {
  const text = '这是一段逐字显示的文本'
  for (let i = 0; i <= text.length; i++) {
    content.value = text.slice(0, i)
    await new Promise(resolve => setTimeout(resolve, 50))
  }
}

simulateTyping()
</script>
```

### 5. Markdown 渲染

```vue
<template>
  <ElABubble
    placement="start"
    :content="markdownContent"
    :is-markdown="true"
  />
</template>

<script setup lang="ts">
const markdownContent = `
# 标题

这是 **粗体** 和 *斜体* 文本。

\`\`\`javascript
console.log('代码块')
\`\`\`

数学公式：$ E = mc^2 $
`
</script>
```

## 样式变体

### variant 属性

```vue
<template>
  <div>
    <!-- 填充样式（默认） -->
    <ElABubble variant="filled" content="填充样式" />

    <!-- 边框样式 -->
    <ElABubble variant="outlined" content="边框样式" />

    <!-- 阴影样式 -->
    <ElABubble variant="shadow" content="阴影样式" />

    <!-- 无边框样式 -->
    <ElABubble variant="borderless" content="无边框样式" />
  </div>
</template>
```

### shape 属性

```vue
<template>
  <div>
    <!-- 默认形状 -->
    <ElABubble shape="default" content="默认形状" />

    <!-- 圆角形状 -->
    <ElABubble shape="round" content="圆角形状" />

    <!-- 直角形状 -->
    <ElABubble shape="corner" content="直角形状" />
  </div>
</template>
```

## 自定义插槽

### 1. 自定义头像

```vue
<template>
  <ElABubble placement="start" content="消息内容">
    <template #avatar>
      <img src="/avatar-ai.png" alt="AI" class="avatar" />
    </template>
  </ElABubble>

  <ElABubble placement="end" content="消息内容">
    <template #avatar>
      <div class="user-avatar">U</div>
    </template>
  </ElABubble>
</template>

<style scoped>
.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
}

.user-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: bold;
}
</style>
```

### 2. 自定义头部（思考过程）

```vue
<template>
  <ElABubble
    placement="start"
    :content="response"
    is-markdown
  >
    <template #header>
      <ElAThinking v-model="showThinking" title="思考过程">
        <div class="thinking-content">
          正在分析问题...
        </div>
      </ElAThinking>
    </template>
  </ElABubble>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElABubble, ElAThinking } from 'element-ai-vue'

const showThinking = ref(false)
const response = ref('这是 AI 的回复')
</script>
```

### 3. 自定义底部（操作按钮）

```vue
<template>
  <ElABubble placement="start" :content="content">
    <template #footer>
      <div class="actions">
        <button @click="copyContent">
          <span class="icon">📋</span>
          复制
        </button>
        <button @click="regenerate">
          <span class="icon">🔄</span>
          重新生成
        </button>
        <button @click="deleteMessage">
          <span class="icon">🗑️</span>
          删除
        </button>
      </div>
    </template>
  </ElABubble>
</template>

<script setup lang="ts">
const copyContent = () => {
  navigator.clipboard.writeText(content.value)
}

const regenerate = () => {
  // 重新生成逻辑
}

const deleteMessage = () => {
  // 删除消息逻辑
}
</script>

<style scoped>
.actions {
  display: flex;
  gap: 8px;
  margin-top: 8px;
}

.actions button {
  padding: 4px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 12px;
  display: flex;
  align-items: center;
  gap: 4px;
}

.actions button:hover {
  background: #f5f5f5;
}

.icon {
  font-size: 14px;
}
</style>
```

## 完整聊天示例

```vue
<template>
  <div class="chat-container">
    <div class="messages">
      <ElABubble
        v-for="msg in messages"
        :key="msg.id"
        :placement="msg.role === 'user' ? 'end' : 'start'"
        :content="msg.content"
        :is-markdown="msg.role === 'assistant'"
        :typing="msg.typing"
        :loading="msg.loading"
        :variant="msg.role === 'assistant' ? 'borderless' : 'filled'"
        :shape="msg.role === 'user' ? 'round' : 'default'"
      >
        <template #avatar>
          <div
            class="avatar"
            :class="msg.role === 'user' ? 'user-avatar' : 'ai-avatar'"
          >
            {{ msg.role === 'user' ? 'U' : 'AI' }}
          </div>
        </template>

        <template #footer v-if="msg.role === 'assistant'">
          <div class="message-actions">
            <button @click="copyMessage(msg.content)">复制</button>
            <button @click="regenerateMessage(msg.id)">重新生成</button>
          </div>
        </template>
      </ElABubble>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElABubble } from 'element-ai-vue'

interface Message {
  id: string
  role: 'user' | 'assistant'
  content: string
  typing?: boolean
  loading?: boolean
}

const messages = ref<Message[]>([
  {
    id: '1',
    role: 'user',
    content: '你好，请介绍一下你自己',
  },
  {
    id: '2',
    role: 'assistant',
    content: '你好！我是 AI 助手，可以回答你的问题。',
    typing: false,
    loading: false,
  },
])

const copyMessage = (content: string) => {
  navigator.clipboard.writeText(content)
  alert('已复制到剪贴板')
}

const regenerateMessage = (id: string) => {
  const msg = messages.value.find(m => m.id === id)
  if (msg) {
    msg.loading = true
    msg.content = ''
    // 模拟重新生成
    setTimeout(() => {
      msg.loading = false
      msg.typing = true
      msg.content = '这是重新生成的内容'
    }, 1000)
  }
}
</script>

<style scoped>
.chat-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.messages {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: bold;
  font-size: 14px;
}

.user-avatar {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.ai-avatar {
  background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
}

.message-actions {
  display: flex;
  gap: 8px;
  margin-top: 8px;
}

.message-actions button {
  padding: 4px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 12px;
}

.message-actions button:hover {
  background: #f5f5f5;
}
</style>
```

## 打字机效果最佳实践

### 方式 1：手动控制内容更新

```vue
<script setup lang="ts">
import { ref } from 'vue'

const content = ref('')
const typing = ref(true)
const loading = ref(false)

const streamContent = async (fullText: string) => {
  loading.value = true
  await new Promise(resolve => setTimeout(resolve, 500))

  loading.value = false
  typing.value = true

  for (let i = 0; i <= fullText.length; i++) {
    content.value = fullText.slice(0, i)
    await new Promise(resolve => setTimeout(resolve, 30))
  }

  typing.value = false
}
</script>
```

### 方式 2：使用 useTyperwriter

```vue
<template>
  <ElABubble
    placement="start"
    :content="content"
    :typing="status === 'running'"
    :loading="false"
  />
</template>

<script setup lang="ts">
import { useTyperwriter } from 'element-ai-vue'

const { content, status, setText, start } = useTyperwriter({
  interval: 30,
})

setText('要显示的完整文本')
start()
</script>
```

## 使用技巧

1. **区分用户和 AI**：
   - 用户消息：`placement="end"`, `variant="filled"`, `shape="round"`
   - AI 消息：`placement="start"`, `variant="borderless"`, `isMarkdown="true"`

2. **加载流程**：
   ```
   loading: true  →  显示三个跳动的点
   loading: false, typing: true  →  开始打字机效果
   typing: false  →  显示完整内容
   ```

3. **立即完成打字机**：
   ```vue
   <template>
     <ElABubble ref="bubbleRef" :typing="true" :content="content" />
     <button @click="bubbleRef?.overTyperwriter()">跳过动画</button>
   </template>
   ```

4. **Markdown 内容**：
   - 设置 `isMarkdown="true"`
   - 支持代码高亮、数学公式、表格等
   - 可以与打字机效果结合使用

## 注意事项

1. **CSS 导入**：必须在 main.ts 中导入：
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

2. **打字机效果**：
   - `typing` 为 `true` 时才会启用
   - 内容必须持续更新才能看到效果
   - 可以通过 `overTyperwriter()` 方法立即完成

3. **性能优化**：
   - 大量消息时使用虚拟滚动
   - 避免同时启用多个打字机效果

4. **响应式设计**：
   - 组件会自动适应容器宽度
   - 在移动端测试不同屏幕尺寸
