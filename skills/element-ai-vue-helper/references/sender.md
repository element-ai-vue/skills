# ElASender 组件

基于 Tiptap 的富文本输入框组件，专为聊天场景设计，支持文件粘贴、自定义扩展、加载状态等。

## 组件导入

```typescript
import { ElASender } from 'element-ai-vue'
```

## Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| v-model | 输入框的 HTML 内容 | `string` | `''` |
| v-model:loading | 发送中状态，可通过 slot 自定义样式 | `boolean` | `false` |
| v-model:show-input-tag-prefix | 是否显示输入框前置标签 | `boolean` | `false` |
| theme | 主题 | `'light' \| 'dark'` | `'light'` |
| placeholder | 占位文本 | `string` | `''` |
| disabled | 是否禁用 | `boolean` | `false` |
| variant | 布局变体 | `'default' \| 'updown'` | `'default'` |
| enterBreak | 回车是否换行，为 false 时回车触发 enterPressed 事件 | `boolean` | `false` |
| inputTagPrefixValue | 输入框前置标签内容 | `string` | `''` |
| extensions | Tiptap 扩展配置 | `Array<Extensions>` | `[]` |
| onHandleKeyDown | 自定义键盘事件处理 | `(view: EditorView, event: KeyboardEvent) => void` | - |

## Slots

| 插槽名 | 说明 | 作用域参数 |
|--------|------|-----------|
| prefix | 前置内容插槽（左侧） | - |
| input-tag-prefix | 输入框前置标签自定义内容 | - |
| action-list | 操作栏列表插槽（右侧） | - |
| send-btn | 发送按钮插槽 | `{ disabled: boolean }` |
| send-btn-loading | loading 为 true 时显示的按钮 | - |
| select-slot-content | select-slot 点击弹窗插槽 | `{ options: SenderSelectOption[] }` |

## Events

| 事件名 | 说明 | 回调参数 |
|--------|------|----------|
| enterPressed | 回车键按下时触发（当 enterBreak 为 false） | - |
| paste | 粘贴时触发 | `(event: ClipboardEvent)` |
| pasteFile | 粘贴文件时触发 | `(files: File[])` |
| blur | 失去焦点时触发 | - |
| focus | 获得焦点时触发 | - |
| send | 点击发送按钮或回车发送时触发 | `(content: string)` |

## Exposes

| 名称 | 说明 | 类型 |
|------|------|------|
| editor | Tiptap editor 实例 | `() => Editor` |
| focus | 自动聚焦 | `() => void` |

## 基础用法

### 1. 简单输入框

```vue
<template>
  <ElASender
    v-model="content"
    placeholder="输入你的消息..."
    @send="handleSend"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElASender } from 'element-ai-vue'

const content = ref('')

const handleSend = (text: string) => {
  console.log('发送:', text)
  content.value = '' // 清空输入
}
</script>
```

### 2. 带加载状态

```vue
<template>
  <ElASender
    v-model="content"
    v-model:loading="loading"
    placeholder="输入消息..."
    @send="handleSend"
  />
</template>

<script setup lang="ts">
import { ref } from 'vue'

const content = ref('')
const loading = ref(false)

const handleSend = async (text: string) => {
  loading.value = true

  try {
    await sendToAPI(text)
  } finally {
    loading.value = false
    content.value = ''
  }
}
</script>
```

### 3. 回车发送 vs 回车换行

```vue
<template>
  <div>
    <!-- 回车发送（默认） -->
    <ElASender
      v-model="content1"
      :enter-break="false"
      placeholder="按 Enter 发送，Shift+Enter 换行"
      @send="handleSend"
    />

    <!-- 回车换行 -->
    <ElASender
      v-model="content2"
      :enter-break="true"
      placeholder="按 Enter 换行"
      @send="handleSend"
    />
  </div>
</template>
```

## 布局变体

### default（水平布局）

```vue
<template>
  <ElASender
    v-model="content"
    variant="default"
    placeholder="水平布局，适合桌面端"
  />
</template>
```

### updown（垂直布局）

```vue
<template>
  <ElASender
    v-model="content"
    variant="updown"
    placeholder="垂直布局，适合移动端"
  />
</template>
```

## 主题

### 深色主题

```vue
<template>
  <div style="background: #000; padding: 20px;">
    <ElASender
      v-model="content"
      theme="dark"
      placeholder="深色主题输入框"
    />
  </div>
</template>
```

## 自定义插槽

### 1. 前置内容（上传按钮）

```vue
<template>
  <ElASender v-model="content">
    <template #prefix>
      <button class="upload-btn" @click="handleUpload">
        📎 上传文件
      </button>
    </template>
  </ElASender>
</template>

<script setup lang="ts">
const handleUpload = () => {
  // 打开文件选择器
}
</script>

<style scoped>
.upload-btn {
  padding: 8px 12px;
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 14px;
}

.upload-btn:hover {
  background: rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}
</style>
```

### 2. 操作栏（模型选择器）

```vue
<template>
  <ElASender v-model="content">
    <template #action-list>
      <select v-model="selectedModel" class="model-select">
        <option value="gpt-4">GPT-4</option>
        <option value="gpt-3.5">GPT-3.5</option>
        <option value="claude">Claude</option>
      </select>
      <button class="voice-btn">🎤</button>
    </template>
  </ElASender>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const selectedModel = ref('gpt-4')
</script>

<style scoped>
.model-select {
  padding: 6px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
}

.voice-btn {
  padding: 6px 12px;
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 18px;
}
</style>
```

### 3. 自定义发送按钮

```vue
<template>
  <ElASender v-model="content">
    <template #send-btn="{ disabled }">
      <button
        class="custom-send-btn"
        :disabled="disabled"
        @click="handleSend"
      >
        <span v-if="!disabled">✈️ 发送</span>
        <span v-else>⏸️ 等待</span>
      </button>
    </template>
  </ElASender>
</template>

<style scoped>
.custom-send-btn {
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  font-weight: bold;
  cursor: pointer;
}

.custom-send-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
```

### 4. 自定义加载按钮

```vue
<template>
  <ElASender
    v-model="content"
    v-model:loading="loading"
  >
    <template #send-btn-loading>
      <button class="stop-btn" @click="stopGenerating">
        ⏹️ 停止生成
      </button>
    </template>
  </ElASender>
</template>

<script setup lang="ts">
const stopGenerating = () => {
  loading.value = false
  // 停止 API 请求
}
</script>
```

## 文件粘贴功能

### 基础文件粘贴

```vue
<template>
  <ElASender
    v-model="content"
    @paste-file="handlePasteFile"
  />
</template>

<script setup lang="ts">
const handlePasteFile = (files: File[]) => {
  console.log('粘贴的文件:', files)
  files.forEach(file => {
    console.log(`文件名: ${file.name}, 大小: ${file.size}`)
  })
}
</script>
```

### 与文件上传组件集成

```vue
<template>
  <div class="chat-input-container">
    <!-- 文件预览 -->
    <ElAFilesCard
      v-if="fileList.length"
      v-model="fileList"
    />

    <!-- 输入框 -->
    <ElASender
      v-model="content"
      @paste-file="handleFileUpload"
      @send="handleSend"
    >
      <template #prefix>
        <ElAFilesUpload
          v-model="fileList"
          :file-size-limit="10"
          :accept="['.pdf', '.png', '.jpg']"
          :on-upload="handleUpload"
        >
          <button class="upload-btn">📎</button>
        </ElAFilesUpload>
      </template>
    </ElASender>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import {
  ElASender,
  ElAFilesCard,
  ElAFilesUpload,
  FilesUploadItem,
  useFileOperation,
} from 'element-ai-vue'

const content = ref('')
const fileList = ref<FilesUploadItem[]>([])

const handleUpload = async (files: FilesUploadItem[]) => {
  for (const file of files) {
    // 模拟上传进度
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

const { handleFileUpload } = useFileOperation(
  {
    fileSizeLimit: 10,
    maxFileLength: 5,
    accept: ['.pdf', '.png', '.jpg'],
    onUpload: handleUpload,
    onErrorMessage: ({ message }) => alert(message),
  },
  fileList
)

const handleSend = (text: string) => {
  console.log('发送消息:', text)
  console.log('附件:', fileList.value)

  // 发送后清空
  content.value = ''
  fileList.value = []
}
</script>

<style scoped>
.chat-input-container {
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding: 16px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
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

## 标签前缀功能

### 显示技能标签

```vue
<template>
  <div>
    <button @click="showTag = true">启用翻译技能</button>
    <button @click="showTag = false">关闭技能</button>

    <ElASender
      v-model="content"
      v-model:show-input-tag-prefix="showTag"
      input-tag-prefix-value="技能：翻译"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const showTag = ref(false)
</script>
```

### 自定义标签内容

```vue
<template>
  <ElASender
    v-model="content"
    v-model:show-input-tag-prefix="showTag"
  >
    <template #input-tag-prefix>
      <div class="custom-tag">
        <span class="tag-icon">✨</span>
        <span class="tag-text">AI 助手</span>
        <button class="tag-close" @click="showTag = false">×</button>
      </div>
    </template>
  </ElASender>
</template>

<style scoped>
.custom-tag {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 4px 8px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border-radius: 4px;
  font-size: 12px;
}

.tag-close {
  padding: 0 4px;
  border: none;
  background: transparent;
  color: white;
  cursor: pointer;
  font-size: 16px;
}
</style>
```

## 高级功能

### 1. 限制输入高度并添加滚动

```vue
<template>
  <ElASender
    v-model="content"
    class="scrollable-sender"
  />
</template>

<style>
.scrollable-sender .el-ai-sender__content {
  max-height: 200px;
  overflow-y: auto;
}

.scrollable-sender .el-ai-sender__content::-webkit-scrollbar {
  width: 6px;
}

.scrollable-sender .el-ai-sender__content::-webkit-scrollbar-thumb {
  border-radius: 6px;
  background-color: rgba(144, 147, 153, 0.3);
}

.scrollable-sender .el-ai-sender__content::-webkit-scrollbar-thumb:hover {
  background-color: rgba(144, 147, 153, 0.5);
}
</style>
```

### 2. 自动聚焦

```vue
<template>
  <ElASender
    ref="senderRef"
    v-model="content"
  />
  <button @click="focusInput">聚焦输入框</button>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const senderRef = ref()

const focusInput = () => {
  senderRef.value?.focus()
}
</script>
```

### 3. 获取纯文本内容

```vue
<template>
  <ElASender
    ref="senderRef"
    v-model="content"
  />
  <button @click="getTextContent">获取纯文本</button>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const senderRef = ref()

const getTextContent = () => {
  const editor = senderRef.value?.editor()
  const text = editor?.getText()
  console.log('纯文本:', text)
}
</script>
```

### 4. 自定义键盘事件

```vue
<template>
  <ElASender
    v-model="content"
    :on-handle-key-down="handleKeyDown"
  />
</template>

<script setup lang="ts">
import type { EditorView } from '@tiptap/pm/view'

const handleKeyDown = (view: EditorView, event: KeyboardEvent) => {
  // Ctrl/Cmd + K 触发特殊功能
  if ((event.ctrlKey || event.metaKey) && event.key === 'k') {
    event.preventDefault()
    console.log('触发快捷键')
    return true
  }
  return false
}
</script>
```

## 完整示例

```vue
<template>
  <div class="chat-input-wrapper">
    <!-- 文件预览区 -->
    <ElAFilesCard
      v-if="fileList.length"
      v-model="fileList"
      class="file-preview"
    />

    <!-- 输入框容器 -->
    <div
      class="sender-container"
      :class="{ focused: isFocused }"
    >
      <ElASender
        ref="senderRef"
        v-model="content"
        v-model:loading="loading"
        v-model:show-input-tag-prefix="showSkill"
        placeholder="输入消息... (Enter 发送，Shift+Enter 换行)"
        :enter-break="false"
        input-tag-prefix-value="技能：翻译"
        @focus="isFocused = true"
        @blur="isFocused = false"
        @paste-file="handleFileUpload"
        @send="handleSend"
      >
        <!-- 左侧：上传按钮 -->
        <template #prefix>
          <ElAFilesUpload
            v-model="fileList"
            :file-size-limit="10"
            :max-file-length="5"
            :accept="['.pdf', '.docx', '.png', '.jpg']"
            :on-upload="handleUpload"
            :on-error-message="handleError"
          >
            <button class="icon-btn" title="上传文件">
              📎
            </button>
          </ElAFilesUpload>
        </template>

        <!-- 右侧：模型选择和语音 -->
        <template #action-list>
          <select v-model="model" class="model-select">
            <option value="gpt-4">GPT-4</option>
            <option value="claude">Claude</option>
          </select>
          <button class="icon-btn" title="语音输入">
            🎤
          </button>
        </template>

        <!-- 自定义加载按钮 -->
        <template #send-btn-loading>
          <button class="stop-btn" @click="stopGeneration">
            ⏹️ 停止
          </button>
        </template>
      </ElASender>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import {
  ElASender,
  ElAFilesCard,
  ElAFilesUpload,
  FilesUploadItem,
  useFileOperation,
} from 'element-ai-vue'

const senderRef = ref()
const content = ref('')
const loading = ref(false)
const isFocused = ref(false)
const showSkill = ref(false)
const model = ref('gpt-4')
const fileList = ref<FilesUploadItem[]>([])

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

const handleError = ({ message, type }) => {
  alert(`${type}: ${message}`)
}

const { handleFileUpload } = useFileOperation(
  {
    fileSizeLimit: 10,
    maxFileLength: 5,
    accept: ['.pdf', '.docx', '.png', '.jpg'],
    onUpload: handleUpload,
    onErrorMessage: handleError,
  },
  fileList
)

const handleSend = async (text: string) => {
  if (!text.trim() && fileList.value.length === 0) {
    return
  }

  loading.value = true

  try {
    // 发送到 API
    console.log('发送消息:', text)
    console.log('附件:', fileList.value)
    console.log('模型:', model.value)

    // 模拟 API 调用
    await new Promise(resolve => setTimeout(resolve, 2000))

    // 清空输入
    content.value = ''
    fileList.value = []
  } finally {
    loading.value = false
  }
}

const stopGeneration = () => {
  loading.value = false
  // 中断 API 请求
}
</script>

<style scoped>
.chat-input-wrapper {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.file-preview {
  padding: 8px;
  background: #f9f9f9;
  border-radius: 8px;
}

.sender-container {
  padding: 12px;
  border: 2px solid #e0e0e0;
  border-radius: 12px;
  transition: border-color 0.2s;
}

.sender-container.focused {
  border-color: #667eea;
}

.icon-btn {
  padding: 8px 12px;
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 16px;
  border-radius: 6px;
  transition: background 0.2s;
}

.icon-btn:hover {
  background: rgba(0, 0, 0, 0.05);
}

.model-select {
  padding: 6px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  font-size: 13px;
  cursor: pointer;
  background: white;
}

.stop-btn {
  padding: 8px 16px;
  border: none;
  border-radius: 6px;
  background: #ff4444;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

.stop-btn:hover {
  background: #cc0000;
}
</style>
```

## 注意事项

1. **CSS 导入**：必须导入样式文件
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

2. **Enter 键行为**：
   - `enterBreak: false`（默认）：Enter 发送，Shift+Enter 换行
   - `enterBreak: true`：Enter 换行

3. **v-model 内容**：
   - 返回的是 HTML 字符串
   - 使用 `editor().getText()` 获取纯文本
   - 使用 `editor().getJSON()` 获取 JSON 格式

4. **文件粘贴**：
   - 只能粘贴图片和文件
   - 纯文本粘贴不会触发 `pasteFile` 事件

5. **响应式设计**：
   - 桌面端使用 `variant="default"`
   - 移动端使用 `variant="updown"`
