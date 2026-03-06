# ElADragUpload 组件

拖放上传区域组件，支持拖拽文件上传。

## 组件导入

```typescript
import { ElADragUpload } from 'element-ai-vue'
```

## Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| v-model | 文件列表 | `FilesUploadItem[]` | `[]` |
| fileSizeLimit | 文件大小限制（MB） | `number` | `10` |
| maxFileLength | 最大文件数量 | `number` | `5` |
| accept | 允许的文件类型 | `string[]` | `[]` |
| onUpload | 上传处理函数 | `(files: FilesUploadItem[]) => void \| Promise<void>` | - |
| onErrorMessage | 错误处理函数 | `(params: FilesUploadErrorParams) => void` | - |

## 基础用法

### 1. 简单拖放区域

```vue
<template>
  <ElADragUpload
    v-model="fileList"
    :on-upload="handleUpload"
    class="upload-area"
  >
    <div class="upload-prompt">
      <div class="icon">📁</div>
      <div class="text">拖拽文件到这里</div>
      <div class="hint">或点击选择文件</div>
    </div>
  </ElADragUpload>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElADragUpload, type FilesUploadItem } from 'element-ai-vue'

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
</script>

<style scoped>
.upload-area {
  width: 100%;
  height: 200px;
  border: 2px dashed #ddd;
  border-radius: 12px;
  background: #fafafa;
  cursor: pointer;
  transition: all 0.3s;
}

.upload-area:hover {
  border-color: #667eea;
  background: #f5f5ff;
}

.upload-prompt {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
}

.icon {
  font-size: 48px;
  margin-bottom: 16px;
}

.text {
  font-size: 16px;
  font-weight: 500;
  color: #333;
  margin-bottom: 8px;
}

.hint {
  font-size: 14px;
  color: #999;
}
</style>
```

### 2. 带文件预览

```vue
<template>
  <ElADragUpload
    v-model="fileList"
    :file-size-limit="10"
    :max-file-length="5"
    :accept="['.pdf', '.png', '.jpg']"
    :on-upload="handleUpload"
    :on-error-message="handleError"
    class="upload-container"
  >
    <!-- 文件预览 -->
    <ElAFilesCard
      v-if="fileList.length"
      v-model="fileList"
      class="files-preview"
    />

    <!-- 上传提示 -->
    <div v-else class="empty-state">
      <div class="icon">📂</div>
      <div class="text">拖拽文件到这里上传</div>
      <div class="types">支持: PDF, PNG, JPG</div>
    </div>
  </ElADragUpload>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import {
  ElADragUpload,
  ElAFilesCard,
  type FilesUploadItem,
} from 'element-ai-vue'

const fileList = ref<FilesUploadItem[]>([])

const handleUpload = async (files: FilesUploadItem[]) => {
  // 上传逻辑
}

const handleError = ({ message, type }) => {
  alert(`${type}: ${message}`)
}
</script>

<style scoped>
.upload-container {
  min-height: 300px;
  border: 2px dashed #e0e0e0;
  border-radius: 12px;
  padding: 20px;
  background: white;
  transition: all 0.3s;
}

.upload-container:hover {
  border-color: #667eea;
  background: #fafafe;
}

.files-preview {
  margin-bottom: 16px;
}

.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 260px;
}

.empty-state .icon {
  font-size: 64px;
  margin-bottom: 20px;
  opacity: 0.5;
}

.empty-state .text {
  font-size: 18px;
  font-weight: 500;
  color: #666;
  margin-bottom: 8px;
}

.empty-state .types {
  font-size: 14px;
  color: #999;
}
</style>
```

### 3. 聊天场景中使用

```vue
<template>
  <div class="chat-upload">
    <ElADragUpload
      v-model="fileList"
      :file-size-limit="10"
      :max-file-length="5"
      :accept="acceptTypes"
      :on-upload="handleUpload"
      :on-error-message="handleError"
      class="drag-area"
    >
      <div class="chat-content">
        <!-- 文件预览 -->
        <ElAFilesCard
          v-if="fileList.length"
          v-model="fileList"
        />

        <!-- 输入框 -->
        <ElASender
          v-model="message"
          v-model:loading="sending"
          placeholder="输入消息或拖拽文件..."
          @paste-file="handlePasteFile"
          @send="handleSend"
        >
          <template #prefix>
            <ElAFilesUpload
              v-model="fileList"
              :file-size-limit="10"
              :accept="acceptTypes"
              :on-upload="handleUpload"
            >
              <button class="upload-btn">📎</button>
            </ElAFilesUpload>
          </template>
        </ElASender>
      </div>
    </ElADragUpload>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import {
  ElADragUpload,
  ElAFilesCard,
  ElAFilesUpload,
  ElASender,
  useFileOperation,
  type FilesUploadItem,
} from 'element-ai-vue'

const fileList = ref<FilesUploadItem[]>([])
const message = ref('')
const sending = ref(false)

const acceptTypes = ['.pdf', '.docx', '.png', '.jpg']

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

const handleError = ({ message, type }) => {
  console.error(type, message)
}

const { handleFileUpload: handlePasteFile } = useFileOperation(
  {
    fileSizeLimit: 10,
    maxFileLength: 5,
    accept: acceptTypes,
    onUpload: handleUpload,
    onErrorMessage: handleError,
  },
  fileList
)

const handleSend = async (text: string) => {
  if (!text.trim() && fileList.value.length === 0) {
    return
  }

  sending.value = true

  try {
    console.log('发送:', text, '文件:', fileList.value)
    // 发送到服务器
    await new Promise(resolve => setTimeout(resolve, 1000))

    message.value = ''
    fileList.value = []
  } finally {
    sending.value = false
  }
}
</script>

<style scoped>
.chat-upload {
  max-width: 800px;
  margin: 0 auto;
}

.drag-area {
  min-height: 300px;
  border: 2px dashed #e0e0e0;
  border-radius: 12px;
  padding: 16px;
  background: white;
  transition: all 0.3s;
}

.drag-area:hover {
  border-color: #667eea;
  background: #fafafe;
}

.chat-content {
  display: flex;
  flex-direction: column;
  gap: 16px;
  min-height: 268px;
}

.upload-btn {
  padding: 8px 12px;
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 18px;
  border-radius: 6px;
  transition: background 0.2s;
}

.upload-btn:hover {
  background: rgba(0, 0, 0, 0.05);
}
</style>
```

## 拖拽状态样式

### 1. 拖拽进入时高亮

```vue
<template>
  <ElADragUpload
    v-model="fileList"
    :on-upload="handleUpload"
    class="drag-zone"
    :class="{ 'drag-over': isDragOver }"
    @dragenter="isDragOver = true"
    @dragleave="isDragOver = false"
    @drop="isDragOver = false"
  >
    <div class="content">
      {{ isDragOver ? '松开鼠标上传文件' : '拖拽文件到这里' }}
    </div>
  </ElADragUpload>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const isDragOver = ref(false)
const fileList = ref([])

const handleUpload = async (files) => {
  // 上传逻辑
}
</script>

<style scoped>
.drag-zone {
  width: 100%;
  height: 200px;
  border: 2px dashed #ddd;
  border-radius: 12px;
  background: #fafafa;
  transition: all 0.3s;
}

.drag-zone.drag-over {
  border-color: #667eea;
  background: #f0f7ff;
  transform: scale(1.02);
}

.content {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  font-size: 16px;
  font-weight: 500;
  color: #666;
}

.drag-zone.drag-over .content {
  color: #667eea;
}
</style>
```

### 2. 动画效果

```vue
<style scoped>
.drag-zone {
  position: relative;
  overflow: hidden;
}

.drag-zone::before {
  content: '';
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  background: linear-gradient(
    45deg,
    transparent 30%,
    rgba(102, 126, 234, 0.1) 50%,
    transparent 70%
  );
  background-size: 200% 200%;
  opacity: 0;
  transition: opacity 0.3s;
  pointer-events: none;
}

.drag-zone.drag-over::before {
  opacity: 1;
  animation: shimmer 2s infinite;
}

@keyframes shimmer {
  0% {
    background-position: 200% 200%;
  }
  100% {
    background-position: -200% -200%;
  }
}
</style>
```

## 完整示例

```vue
<template>
  <div class="upload-demo">
    <h3>文件上传演示</h3>

    <!-- 配置选项 -->
    <div class="controls">
      <label>
        文件大小限制:
        <input
          v-model.number="sizeLimit"
          type="number"
          min="1"
          max="100"
        />
        MB
      </label>

      <label>
        最大文件数:
        <input
          v-model.number="maxFiles"
          type="number"
          min="1"
          max="20"
        />
      </label>

      <label>
        允许类型:
        <select v-model="fileType">
          <option value="all">所有文件</option>
          <option value="image">仅图片</option>
          <option value="document">仅文档</option>
        </select>
      </label>
    </div>

    <!-- 拖放区域 -->
    <ElADragUpload
      v-model="fileList"
      :file-size-limit="sizeLimit"
      :max-file-length="maxFiles"
      :accept="acceptTypes"
      :on-upload="handleUpload"
      :on-error-message="handleError"
      class="upload-area"
      :class="{ 'has-files': fileList.length > 0 }"
    >
      <!-- 文件列表 -->
      <div v-if="fileList.length" class="files-section">
        <div class="section-header">
          <span>已选择 {{ fileList.length }} 个文件</span>
          <button @click="clearAll" class="clear-btn">
            清空
          </button>
        </div>

        <ElAFilesCard v-model="fileList" />
      </div>

      <!-- 空状态 -->
      <div v-else class="empty-section">
        <div class="icon">📤</div>
        <div class="title">拖拽文件到这里上传</div>
        <div class="subtitle">或点击选择文件</div>
        <div class="info">
          <div>大小限制: {{ sizeLimit }}MB</div>
          <div>数量限制: {{ maxFiles }} 个</div>
          <div>支持类型: {{ fileTypeText }}</div>
        </div>
      </div>
    </ElADragUpload>

    <!-- 上传统计 -->
    <div v-if="fileList.length" class="stats">
      <div class="stat-item">
        <span class="label">总大小:</span>
        <span class="value">{{ totalSize }}</span>
      </div>
      <div class="stat-item">
        <span class="label">上传进度:</span>
        <span class="value">{{ uploadProgress }}%</span>
      </div>
      <div class="stat-item">
        <span class="label">状态:</span>
        <span class="value" :class="statusClass">{{ uploadStatus }}</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import {
  ElADragUpload,
  ElAFilesCard,
  type FilesUploadItem,
} from 'element-ai-vue'

const fileList = ref<FilesUploadItem[]>([])
const sizeLimit = ref(10)
const maxFiles = ref(5)
const fileType = ref('all')

const acceptTypes = computed(() => {
  switch (fileType.value) {
    case 'image':
      return ['.png', '.jpg', '.jpeg', '.gif', '.webp']
    case 'document':
      return ['.pdf', '.doc', '.docx', '.xls', '.xlsx', '.txt']
    default:
      return []
  }
})

const fileTypeText = computed(() => {
  switch (fileType.value) {
    case 'image':
      return '图片文件'
    case 'document':
      return '文档文件'
    default:
      return '所有类型'
  }
})

const totalSize = computed(() => {
  const bytes = fileList.value.reduce((sum, file) => sum + file.fileSize, 0)
  return formatFileSize(bytes)
})

const uploadProgress = computed(() => {
  if (fileList.value.length === 0) return 0
  const total = fileList.value.reduce((sum, file) => sum + file.progress, 0)
  return Math.round(total / fileList.value.length)
})

const uploadStatus = computed(() => {
  const allSuccess = fileList.value.every(f => f.uploadingStatus === 'success')
  const hasError = fileList.value.some(f => f.uploadingStatus === 'error')
  const uploading = fileList.value.some(f => f.uploadingStatus === 'uploading')

  if (hasError) return '失败'
  if (uploading) return '上传中'
  if (allSuccess && fileList.value.length > 0) return '完成'
  return '待上传'
})

const statusClass = computed(() => {
  switch (uploadStatus.value) {
    case '完成':
      return 'success'
    case '失败':
      return 'error'
    case '上传中':
      return 'uploading'
    default:
      return ''
  }
})

const handleUpload = async (files: FilesUploadItem[]) => {
  for (const file of files) {
    // 模拟上传
    let progress = 0
    const interval = setInterval(() => {
      progress += Math.random() * 15
      if (progress >= 100) {
        progress = 100
        file.uploadingStatus = 'success'
        clearInterval(interval)
      }
      file.progress = Math.min(progress, 100)
    }, 200)
  }
}

const handleError = ({ message, type }) => {
  const messages = {
    size: '文件大小超过限制',
    count: '文件数量超过限制',
    type: '不支持的文件类型',
  }
  alert(`${messages[type]}: ${message}`)
}

const clearAll = () => {
  fileList.value = []
}

const formatFileSize = (bytes: number): string => {
  if (bytes < 1024) return bytes + ' B'
  if (bytes < 1024 * 1024) return (bytes / 1024).toFixed(2) + ' KB'
  return (bytes / (1024 * 1024)).toFixed(2) + ' MB'
}
</script>

<style scoped>
.upload-demo {
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
}

h3 {
  margin-bottom: 24px;
  color: #333;
}

.controls {
  display: flex;
  gap: 20px;
  margin-bottom: 24px;
  padding: 16px;
  background: #f5f5f5;
  border-radius: 8px;
}

.controls label {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
}

.controls input,
.controls select {
  padding: 4px 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 14px;
}

.upload-area {
  min-height: 400px;
  border: 2px dashed #e0e0e0;
  border-radius: 12px;
  padding: 24px;
  background: white;
  transition: all 0.3s;
}

.upload-area:hover {
  border-color: #667eea;
}

.upload-area.has-files {
  border-style: solid;
  border-color: #667eea;
  background: #fafafe;
}

.files-section {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-bottom: 12px;
  border-bottom: 1px solid #e0e0e0;
}

.section-header span {
  font-size: 14px;
  font-weight: 500;
  color: #666;
}

.clear-btn {
  padding: 6px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 12px;
  transition: all 0.2s;
}

.clear-btn:hover {
  background: #f5f5f5;
}

.empty-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 352px;
}

.empty-section .icon {
  font-size: 72px;
  margin-bottom: 24px;
  opacity: 0.5;
}

.empty-section .title {
  font-size: 20px;
  font-weight: 500;
  color: #333;
  margin-bottom: 8px;
}

.empty-section .subtitle {
  font-size: 14px;
  color: #999;
  margin-bottom: 24px;
}

.empty-section .info {
  display: flex;
  flex-direction: column;
  gap: 6px;
  padding: 16px 24px;
  background: #f9f9f9;
  border-radius: 8px;
  font-size: 13px;
  color: #666;
}

.stats {
  display: flex;
  gap: 24px;
  margin-top: 20px;
  padding: 16px;
  background: #f9f9f9;
  border-radius: 8px;
}

.stat-item {
  display: flex;
  gap: 8px;
  font-size: 14px;
}

.stat-item .label {
  color: #666;
}

.stat-item .value {
  font-weight: 500;
  color: #333;
}

.stat-item .value.success {
  color: #10b981;
}

.stat-item .value.error {
  color: #ef4444;
}

.stat-item .value.uploading {
  color: #3b82f6;
}
</style>
```

## 注意事项

1. **CSS 导入**：必须导入样式
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

2. **拖拽区域**：
   - 组件会自动处理拖拽事件
   - 可以通过 CSS 自定义拖拽时的样式

3. **文件验证**：
   - 自动验证文件大小、类型、数量
   - 验证失败会触发 `onErrorMessage`

4. **上传处理**：
   - 必须在 `onUpload` 中手动更新进度
   - 设置 `uploadingStatus` 为 'success' 或 'error'

5. **与其他组件配合**：
   - 通常与 `ElAFilesCard` 一起使用显示文件
   - 可以包裹 `ElASender` 实现聊天场景
