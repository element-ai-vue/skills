# 快速开始示例

这里提供几个常见场景的完整代码示例，可以直接复制使用。

## 1. 基础 AI 聊天界面

最简单的聊天界面，包含用户和 AI 消息。

```vue
<template>
  <div class="chat-app">
    <!-- 消息列表 -->
    <div class="messages">
      <ElABubble
        v-for="msg in messages"
        :key="msg.id"
        :placement="msg.role === 'user' ? 'end' : 'start'"
        :content="msg.content"
        :is-markdown="msg.role === 'assistant'"
      />
    </div>

    <!-- 输入框 -->
    <ElASender
      v-model="userInput"
      placeholder="输入你的消息..."
      @send="handleSend"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElABubble, ElASender } from 'element-ai-vue'

interface Message {
  id: string
  role: 'user' | 'assistant'
  content: string
}

const messages = ref<Message[]>([])
const userInput = ref('')

const handleSend = (content: string) => {
  // 添加用户消息
  messages.value.push({
    id: Date.now().toString(),
    role: 'user',
    content,
  })

  // 模拟 AI 回复
  setTimeout(() => {
    messages.value.push({
      id: (Date.now() + 1).toString(),
      role: 'assistant',
      content: '这是 AI 的回复',
    })
  }, 1000)

  // 清空输入
  userInput.value = ''
}
</script>

<style scoped>
.chat-app {
  display: flex;
  flex-direction: column;
  height: 100vh;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.messages {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-bottom: 20px;
}
</style>
```

## 2. 带打字机效果的聊天

AI 回复带有逐字显示的打字机效果。

```vue
<template>
  <div class="chat-app">
    <div class="messages">
      <ElABubble
        v-for="msg in messages"
        :key="msg.id"
        :placement="msg.role === 'user' ? 'end' : 'start'"
        :content="msg.content"
        :typing="msg.typing"
        :loading="msg.loading"
        :is-markdown="msg.role === 'assistant'"
      />
    </div>

    <ElASender
      v-model="userInput"
      @send="handleSend"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElABubble, ElASender } from 'element-ai-vue'

interface Message {
  id: string
  role: 'user' | 'assistant'
  content: string
  typing?: boolean
  loading?: boolean
}

const messages = ref<Message[]>([])
const userInput = ref('')

const handleSend = async (content: string) => {
  // 用户消息
  messages.value.push({
    id: Date.now().toString(),
    role: 'user',
    content,
  })

  // AI 消息（带加载状态）
  const aiMsg: Message = {
    id: (Date.now() + 1).toString(),
    role: 'assistant',
    content: '',
    loading: true,
    typing: true,
  }
  messages.value.push(aiMsg)

  userInput.value = ''

  // 模拟 API 调用
  await new Promise(resolve => setTimeout(resolve, 1000))

  // 开始打字机效果
  aiMsg.loading = false
  const fullText = '这是 AI 的回复，会逐字显示'

  for (let i = 0; i <= fullText.length; i++) {
    aiMsg.content = fullText.slice(0, i)
    await new Promise(resolve => setTimeout(resolve, 50))
  }

  aiMsg.typing = false
}
</script>
```

## 3. 带文件上传的聊天

支持拖拽、粘贴文件。

```vue
<template>
  <div class="chat-app">
    <!-- 消息列表 -->
    <div class="messages">
      <ElABubble
        v-for="msg in messages"
        :key="msg.id"
        :placement="msg.role === 'user' ? 'end' : 'start'"
        :content="msg.content"
      />
    </div>

    <!-- 拖放上传区域 -->
    <ElADragUpload
      v-model="fileList"
      :file-size-limit="10"
      :on-upload="handleUpload"
      class="upload-area"
    >
      <!-- 文件预览 -->
      <ElAFilesCard
        v-if="fileList.length"
        v-model="fileList"
      />

      <!-- 输入框 -->
      <ElASender
        v-model="userInput"
        @paste-file="handlePasteFile"
        @send="handleSend"
      >
        <template #prefix>
          <ElAFilesUpload
            v-model="fileList"
            :on-upload="handleUpload"
          >
            <button class="upload-btn">📎</button>
          </ElAFilesUpload>
        </template>
      </ElASender>
    </ElADragUpload>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import {
  ElABubble,
  ElASender,
  ElAFilesCard,
  ElAFilesUpload,
  ElADragUpload,
  useFileOperation,
  type FilesUploadItem,
} from 'element-ai-vue'

const messages = ref<any[]>([])
const userInput = ref('')
const fileList = ref<FilesUploadItem[]>([])

const handleUpload = async (files: FilesUploadItem[]) => {
  for (const file of files) {
    // 模拟上传
    let progress = 0
    const interval = setInterval(() => {
      progress += 10
      file.progress = progress
      if (progress >= 100) {
        file.uploadingStatus = 'success'
        clearInterval(interval)
      }
    }, 200)
  }
}

const { handleFileUpload: handlePasteFile } = useFileOperation(
  {
    fileSizeLimit: 10,
    onUpload: handleUpload,
    onErrorMessage: ({ message }) => alert(message),
  },
  fileList
)

const handleSend = (content: string) => {
  messages.value.push({
    id: Date.now().toString(),
    role: 'user',
    content: `${content}\n附件: ${fileList.value.length} 个`,
  })

  userInput.value = ''
  fileList.value = []
}
</script>

<style scoped>
.upload-area {
  min-height: 200px;
  border: 2px dashed #ddd;
  border-radius: 8px;
  padding: 16px;
}

.upload-btn {
  padding: 8px;
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 18px;
}
</style>
```

## 4. Markdown 文档展示

展示 Markdown 内容，支持代码高亮和数学公式。

```vue
<template>
  <div class="doc-viewer">
    <ElAMarkdown :content="content" :theme="theme" />

    <button @click="toggleTheme">
      切换主题
    </button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAMarkdown } from 'element-ai-vue'

const theme = ref<'light' | 'dark'>('light')

const content = ref(`
# 文档标题

这是一个 **Markdown** 文档示例。

## 代码示例

\`\`\`javascript
function hello() {
  console.log('Hello World!')
}
\`\`\`

## 数学公式

$ E = mc^2 $

## 表格

| 列1 | 列2 |
|-----|-----|
| A   | B   |
`)

const toggleTheme = () => {
  theme.value = theme.value === 'light' ? 'dark' : 'light'
}
</script>

<style scoped>
.doc-viewer {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}
</style>
```

## 5. 流式 Markdown 输出

模拟 AI 流式输出 Markdown 内容。

```vue
<template>
  <div>
    <button @click="start">开始流式输出</button>
    <button @click="paused">暂停</button>
    <button @click="stop">停止</button>

    <ElAMarkdown :content="content" />
  </div>
</template>

<script setup lang="ts">
import { ElAMarkdown, useTyperwriter } from 'element-ai-vue'

const { start, paused, stop, setText, content } = useTyperwriter({
  interval: 30,
})

setText(`
# AI 分析报告

## 摘要

这是一份详细的分析报告...

## 代码实现

\`\`\`python
def analyze_data(data):
    return data.mean()
\`\`\`

## 结论

综上所述...
`)
</script>
```

## 6. 完整的聊天应用

包含所有功能的完整示例。

```vue
<template>
  <div class="chat-app">
    <!-- 头部 -->
    <header class="header">
      <h1>AI 助手</h1>
      <button @click="clearChat">清空对话</button>
    </header>

    <!-- 消息列表 -->
    <div class="messages" ref="messagesRef">
      <ElABubble
        v-for="msg in messages"
        :key="msg.id"
        :placement="msg.role === 'user' ? 'end' : 'start'"
        :content="msg.content"
        :typing="msg.typing"
        :loading="msg.loading"
        :is-markdown="msg.role === 'assistant'"
        :variant="msg.role === 'assistant' ? 'borderless' : 'filled'"
      >
        <!-- AI 消息的操作按钮 -->
        <template #footer v-if="msg.role === 'assistant'">
          <div class="actions">
            <button @click="copyMessage(msg.content)">复制</button>
            <button @click="regenerate(msg.id)">重新生成</button>
          </div>
        </template>
      </ElABubble>
    </div>

    <!-- 输入区域 -->
    <div class="input-area">
      <ElADragUpload
        v-model="fileList"
        :file-size-limit="10"
        :on-upload="handleUpload"
      >
        <ElAFilesCard
          v-if="fileList.length"
          v-model="fileList"
        />

        <ElASender
          v-model="userInput"
          v-model:loading="loading"
          placeholder="输入消息..."
          @paste-file="handlePasteFile"
          @send="handleSend"
        >
          <template #prefix>
            <ElAFilesUpload
              v-model="fileList"
              :on-upload="handleUpload"
            >
              <button class="icon-btn">📎</button>
            </ElAFilesUpload>
          </template>

          <template #send-btn-loading>
            <button class="stop-btn" @click="stopGeneration">
              停止
            </button>
          </template>
        </ElASender>
      </ElADragUpload>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, nextTick } from 'vue'
import {
  ElABubble,
  ElASender,
  ElAFilesCard,
  ElAFilesUpload,
  ElADragUpload,
  useFileOperation,
  type FilesUploadItem,
} from 'element-ai-vue'

interface Message {
  id: string
  role: 'user' | 'assistant'
  content: string
  typing?: boolean
  loading?: boolean
}

const messages = ref<Message[]>([])
const userInput = ref('')
const loading = ref(false)
const fileList = ref<FilesUploadItem[]>([])
const messagesRef = ref<HTMLElement>()

const handleUpload = async (files: FilesUploadItem[]) => {
  for (const file of files) {
    let progress = 0
    const interval = setInterval(() => {
      progress += 10
      file.progress = progress
      if (progress >= 100) {
        file.uploadingStatus = 'success'
        clearInterval(interval)
      }
    }, 200)
  }
}

const { handleFileUpload: handlePasteFile } = useFileOperation(
  {
    fileSizeLimit: 10,
    onUpload: handleUpload,
    onErrorMessage: ({ message }) => alert(message),
  },
  fileList
)

const handleSend = async (content: string) => {
  // 用户消息
  messages.value.push({
    id: Date.now().toString(),
    role: 'user',
    content,
  })

  // AI 消息
  const aiMsg: Message = {
    id: (Date.now() + 1).toString(),
    role: 'assistant',
    content: '',
    loading: true,
    typing: true,
  }
  messages.value.push(aiMsg)

  userInput.value = ''
  fileList.value = []
  loading.value = true

  // 滚动到底部
  await nextTick()
  scrollToBottom()

  // 模拟 API 调用
  await new Promise(resolve => setTimeout(resolve, 1000))

  // 打字机效果
  aiMsg.loading = false
  const fullText = '这是 AI 的回复内容'

  for (let i = 0; i <= fullText.length; i++) {
    aiMsg.content = fullText.slice(0, i)
    await new Promise(resolve => setTimeout(resolve, 30))
  }

  aiMsg.typing = false
  loading.value = false
}

const stopGeneration = () => {
  loading.value = false
}

const copyMessage = (content: string) => {
  navigator.clipboard.writeText(content)
  alert('已复制')
}

const regenerate = (id: string) => {
  const msg = messages.value.find(m => m.id === id)
  if (msg) {
    msg.loading = true
    msg.content = ''
    // 重新生成逻辑
  }
}

const clearChat = () => {
  if (confirm('确定要清空对话吗？')) {
    messages.value = []
  }
}

const scrollToBottom = () => {
  if (messagesRef.value) {
    messagesRef.value.scrollTop = messagesRef.value.scrollHeight
  }
}
</script>

<style scoped>
.chat-app {
  display: flex;
  flex-direction: column;
  height: 100vh;
  max-width: 900px;
  margin: 0 auto;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 20px;
  border-bottom: 1px solid #e0e0e0;
}

.header h1 {
  margin: 0;
  font-size: 20px;
}

.header button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: white;
  cursor: pointer;
}

.messages {
  flex: 1;
  overflow-y: auto;
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

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
}

.input-area {
  padding: 20px;
  border-top: 1px solid #e0e0e0;
}

.icon-btn {
  padding: 8px;
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 18px;
}

.stop-btn {
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  background: #ff4444;
  color: white;
  cursor: pointer;
}
</style>
```

## 使用步骤

1. **安装依赖**
   ```bash
   pnpm install element-ai-vue
   ```

2. **导入样式**（在 main.ts）
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

3. **复制示例代码**到你的 Vue 组件中

4. **根据需求调整**样式和功能

## 下一步

- 查看 [组件文档](./references/) 了解更多 API
- 查看 [安装配置](./installation.md) 了解高级配置
- 访问 [官方文档](https://element-ai-vue.com) 查看更多示例
