# 打字机效果

element-ai-vue 提供打字机效果功能，用于实现文本逐字显示的动画效果。

## useTyperwriter Hook

组合式函数，用于控制打字机动画。

### 导入

```typescript
import { useTyperwriter } from 'element-ai-vue'
```

### 基础用法

```vue
<template>
  <div>
    <button @click="start">开始</button>
    <button @click="paused">暂停</button>
    <button @click="stop">停止</button>
    <button @click="done">完成</button>

    <div>{{ content }}</div>
    <div>状态: {{ status }}</div>
  </div>
</template>

<script setup lang="ts">
import { useTyperwriter } from 'element-ai-vue'

const {
  start,      // 开始打字机
  paused,     // 暂停打字机
  stop,       // 停止并重置
  done,       // 立即完成
  setText,    // 设置要显示的文本
  addText,    // 追加文本
  content,    // 当前显示的内容（ref）
  status,     // 当前状态（ref）
} = useTyperwriter({
  interval: 50,      // 每个字符间隔（毫秒）
  typingSpeed: 2,    // 每次显示几个字符
})

// 设置要显示的文本
setText('这是要逐字显示的文本内容')
</script>
```

### 配置选项

```typescript
interface TyperwriterOptions {
  interval?: number      // 每个字符的间隔时间（毫秒），默认 50
  typingSpeed?: number   // 每次显示几个字符，默认 2
}
```

### 返回值

```typescript
interface TyperwriterReturn {
  start: () => void           // 开始/恢复打字机动画
  paused: () => void          // 暂停打字机动画
  stop: () => void            // 停止并重置到初始状态
  done: () => void            // 立即完成，显示全部内容
  setText: (text: string) => void    // 设置要显示的文本
  addText: (text: string) => void    // 追加文本到末尾
  content: Ref<string>        // 当前显示的内容
  status: Ref<'running' | 'paused' | 'stopped'>  // 当前状态
}
```

## 使用示例

### 1. 基础文本打字机

```vue
<template>
  <div class="typewriter-demo">
    <div class="controls">
      <button @click="start">开始</button>
      <button @click="paused">暂停</button>
      <button @click="stop">停止</button>
      <button @click="done">跳过</button>
    </div>

    <div class="status">
      状态: {{ status }}
    </div>

    <div class="content">
      {{ content }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { useTyperwriter } from 'element-ai-vue'

const { start, paused, stop, done, setText, content, status } = useTyperwriter({
  interval: 50,
  typingSpeed: 1,
})

setText(`
欢迎使用 element-ai-vue！

这是一个专为 AI 交互设计的 Vue 组件库。
支持打字机效果、Markdown 渲染、代码高亮等功能。
`)
</script>

<style scoped>
.typewriter-demo {
  padding: 20px;
  max-width: 600px;
}

.controls {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.controls button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
}

.controls button:hover {
  background: #f5f5f5;
}

.status {
  margin-bottom: 10px;
  color: #666;
  font-size: 14px;
}

.content {
  padding: 16px;
  background: #f9f9f9;
  border-radius: 8px;
  min-height: 100px;
  white-space: pre-wrap;
  line-height: 1.6;
}
</style>
```

### 2. 与 Markdown 组件结合

```vue
<template>
  <div>
    <button @click="start">开始演示</button>
    <ElAMarkdown :content="content" />
  </div>
</template>

<script setup lang="ts">
import { ElAMarkdown, useTyperwriter } from 'element-ai-vue'

const { start, setText, content } = useTyperwriter({
  interval: 30,
})

setText(`
# AI 回复

这是一个**流式**输出的 Markdown 内容。

## 代码示例

\`\`\`javascript
function hello() {
  console.log('Hello World!')
}
\`\`\`

## 数学公式

$ E = mc^2 $
`)
</script>
```

### 3. 流式 API 响应

```vue
<template>
  <div>
    <button @click="fetchStream">获取流式响应</button>
    <div class="response">{{ content }}</div>
  </div>
</template>

<script setup lang="ts">
import { useTyperwriter } from 'element-ai-vue'

const { setText, addText, content, start } = useTyperwriter({
  interval: 30,
})

const fetchStream = async () => {
  // 清空之前的内容
  setText('')
  start()

  // 模拟流式 API 响应
  const response = await fetch('/api/chat/stream')
  const reader = response.body?.getReader()
  const decoder = new TextDecoder()

  while (true) {
    const { done, value } = await reader!.read()
    if (done) break

    const chunk = decoder.decode(value)
    addText(chunk)  // 追加新内容
  }
}
</script>
```

### 4. 多段落逐步显示

```vue
<template>
  <div>
    <button @click="showNext">显示下一段</button>
    <div class="content">{{ content }}</div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useTyperwriter } from 'element-ai-vue'

const { setText, addText, content, start } = useTyperwriter({
  interval: 40,
})

const paragraphs = [
  '第一段：欢迎来到 AI 对话系统。',
  '第二段：这里可以回答你的各种问题。',
  '第三段：让我们开始愉快的对话吧！',
]

let currentIndex = 0

const showNext = () => {
  if (currentIndex === 0) {
    setText(paragraphs[0])
  } else {
    addText('\n\n' + paragraphs[currentIndex])
  }

  start()
  currentIndex++

  if (currentIndex >= paragraphs.length) {
    currentIndex = 0
  }
}
</script>
```

### 5. 可控速度

```vue
<template>
  <div>
    <div class="speed-control">
      <label>速度：</label>
      <input
        v-model.number="speed"
        type="range"
        min="10"
        max="200"
        @input="updateSpeed"
      />
      <span>{{ speed }}ms</span>
    </div>

    <button @click="restart">重新开始</button>

    <div class="content">{{ content }}</div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { useTyperwriter } from 'element-ai-vue'

const speed = ref(50)

const { setText, content, start, stop } = useTyperwriter({
  interval: speed.value,
})

const text = '这是一段可以调节速度的打字机效果演示文本。'

const updateSpeed = () => {
  restart()
}

const restart = () => {
  stop()
  // 需要重新创建 useTyperwriter 实例来更新 interval
  setText(text)
  start()
}

setText(text)
</script>

<style scoped>
.speed-control {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 20px;
}

.speed-control input {
  width: 200px;
}
</style>
```

### 6. 在聊天消息中使用

```vue
<template>
  <div class="chat">
    <ElABubble
      v-for="msg in messages"
      :key="msg.id"
      :placement="msg.role === 'user' ? 'end' : 'start'"
      :content="msg.content"
      :typing="msg.typing"
      :loading="msg.loading"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElABubble, useTyperwriter } from 'element-ai-vue'

interface Message {
  id: string
  role: 'user' | 'assistant'
  content: string
  typing?: boolean
  loading?: boolean
}

const messages = ref<Message[]>([])

const addUserMessage = (text: string) => {
  messages.value.push({
    id: Date.now().toString(),
    role: 'user',
    content: text,
  })

  // 添加 AI 回复（带打字机效果）
  addAIResponse()
}

const addAIResponse = async () => {
  const aiMsg: Message = {
    id: (Date.now() + 1).toString(),
    role: 'assistant',
    content: '',
    loading: true,
    typing: true,
  }

  messages.value.push(aiMsg)

  // 模拟 API 调用
  await new Promise(resolve => setTimeout(resolve, 1000))

  // 开始打字机效果
  aiMsg.loading = false

  const fullText = '这是 AI 的回复内容，会逐字显示出来。'

  // 手动实现打字机效果
  for (let i = 0; i <= fullText.length; i++) {
    aiMsg.content = fullText.slice(0, i)
    await new Promise(resolve => setTimeout(resolve, 50))
  }

  aiMsg.typing = false
}
</script>
```

## 最佳实践

### 1. 速度选择

- **快速**（20-30ms）：适合代码、数据等内容
- **中速**（40-60ms）：适合一般文本、对话
- **慢速**（80-100ms）：适合强调、教学内容

### 2. 配合加载状态

```vue
<script setup lang="ts">
// 1. 显示加载动画
message.loading = true

// 2. 等待 API 响应
const response = await fetchAPI()

// 3. 关闭加载，开启打字机
message.loading = false
message.typing = true

// 4. 逐字显示内容
setText(response)
start()

// 5. 完成后关闭打字机标识
// typing 会在内容显示完后自动变为 false
</script>
```

### 3. 提供跳过功能

```vue
<template>
  <div>
    <ElABubble
      ref="bubbleRef"
      :content="content"
      :typing="status === 'running'"
    />

    <button
      v-if="status === 'running'"
      @click="skipAnimation"
    >
      跳过动画
    </button>
  </div>
</template>

<script setup lang="ts">
const { status, done } = useTyperwriter()

const skipAnimation = () => {
  done()  // 立即显示全部内容
  // 或者调用 bubbleRef.value?.overTyperwriter()
}
</script>
```

### 4. 性能优化

```vue
<script setup lang="ts">
// 避免同时运行多个打字机效果
const runningTypers = ref<Set<string>>(new Set())

const startTyper = (id: string) => {
  if (runningTypers.value.size >= 2) {
    // 最多同时运行 2 个
    return
  }

  runningTypers.value.add(id)

  // 打字机完成后移除
  onComplete(() => {
    runningTypers.value.delete(id)
  })
}
</script>
```

## 注意事项

1. **内存管理**：
   - 组件卸载时打字机会自动停止
   - 不需要手动清理

2. **状态同步**：
   - `content` 是响应式的，会自动更新 UI
   - `status` 可用于显示当前状态

3. **中文字符**：
   - 完全支持中文、emoji 等 Unicode 字符
   - 每个字符作为一个单位显示

4. **性能考虑**：
   - 避免同时运行过多打字机效果
   - 长文本建议使用较大的 `typingSpeed` 值

5. **与 ElABubble 配合**：
   - ElABubble 内置打字机支持
   - 设置 `typing={true}` 并更新 `content` 即可
   - 无需手动使用 `useTyperwriter`
