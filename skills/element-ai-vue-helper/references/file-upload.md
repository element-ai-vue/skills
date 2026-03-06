# 文件上传组件

element-ai-vue 提供一套完整的文件上传解决方案，包括文件选择、拖放上传、文件预览和进度显示。

## 组件概览

- **ElAFilesUpload** - 基础文件选择器
- **ElADragUpload** - 拖放上传区域
- **ElAFilesCard** - 文件预览卡片（带进度）
- **useFileOperation** - 文件操作组合式函数

## ElAFilesUpload

基础文件上传组件，点击选择文件。

### 导入

```typescript
import { ElAFilesUpload, type FilesUploadItem } from 'element-ai-vue'
```

### Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| v-model | 文件列表 | `FilesUploadItem[]` | `[]` |
| fileSizeLimit | 文件大小限制（MB） | `number` | `10` |
| maxFileLength | 最大文件数量 | `number` | `5` |
| accept | 允许的文件类型 | `string[]` | `[]` |
| onUpload | 上传处理函数 | `(files: FilesUploadItem[]) => void \| Promise<void>` | - |
| onErrorMessage | 错误处理函数 | `(params: FilesUploadErrorParams) => void` | - |

### 基础用法

```vue
<template>
  <ElAFilesUpload
    v-model="fileList"
    :file-size-limit="10"
    :max-file-length="5"
    :accept="['.pdf', '.docx', '.png', '.jpg']"
    :on-upload="handleUpload"
    :on-error-message="handleError"
  >
    <button class="upload-btn">
      📎 选择文件
    </button>
  </ElAFilesUpload>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAFilesUpload, type FilesUploadItem } from 'element-ai-vue'

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
        // 可以在这里更新 fileUrl 为 CDN 地址
        // file.fileUrl = 'https://cdn.example.com/xxx'
        clearInterval(interval)
      }
    }, 200)
  }
}

const handleError = ({ message, type }) => {
  alert(`${type}: ${message}`)
}
</script>

<style scoped>
.upload-btn {
  padding: 10px 20px;
  border: 2px dashed #ddd;
  border-radius: 8px;
  background: white;
  cursor: pointer;
  font-size: 14px;
}

.upload-btn:hover {
  border-color: #667eea;
  background: #f5f5ff;
}
</style>
```

## ElADragUpload

拖放上传区域，支持拖拽文件。

### 导入

```typescript
import { ElADragUpload } from 'element-ai-vue'
```

### Props

与 ElAFilesUpload 相同。

### 基础用法

```vue
<template>
  <ElADragUpload
    v-model="fileList"
    :file-size-limit="10"
    :max-file-length="5"
    :accept="['.pdf', '.png', '.jpg']"
    :on-upload="handleUpload"
    :on-error-message="handleError"
    class="drag-area"
  >
    <div class="drag-content">
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
  // 上传逻辑
}

const handleError = ({ message, type }) => {
  console.error(type, message)
}
</script>

<style scoped>
.drag-area {
  width: 100%;
  height: 200px;
  border: 2px dashed #ddd;
  border-radius: 12px;
  background: #fafafa;
  cursor: pointer;
  transition: all 0.3s;
}

.drag-area:hover {
  border-color: #667eea;
  background: #f5f5ff;
}

.drag-content {
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

## ElAFilesCard

文件预览卡片，显示文件信息和上传进度。

### 导入

```typescript
import { ElAFilesCard } from 'element-ai-vue'
```

### Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| v-model | 文件列表 | `FilesUploadItem[]` | `[]` |

### 基础用法

```vue
<template>
  <div>
    <!-- 文件预览 -->
    <ElAFilesCard v-model="fileList" />

    <!-- 上传按钮 -->
    <ElAFilesUpload
      v-model="fileList"
      :on-upload="handleUpload"
    >
      <button>添加文件</button>
    </ElAFilesUpload>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import {
  ElAFilesCard,
  ElAFilesUpload,
  type FilesUploadItem,
} from 'element-ai-vue'

const fileList = ref<FilesUploadItem[]>([])

const handleUpload = async (files: FilesUploadItem[]) => {
  // 上传逻辑
}
</script>
```

## useFileOperation

文件操作组合式函数，用于处理粘贴文件等操作。

### 导入

```typescript
import { useFileOperation } from 'element-ai-vue'
```

### 基础用法

```vue
<template>
  <ElASender
    v-model="content"
    @paste-file="handleFileUpload"
  />

  <ElAFilesCard v-model="fileList" />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import {
  ElASender,
  ElAFilesCard,
  useFileOperation,
  type FilesUploadItem,
} from 'element-ai-vue'

const content = ref('')
const fileList = ref<FilesUploadItem[]>([])

const handleUpload = async (files: FilesUploadItem[]) => {
  for (const file of files) {
    // 上传逻辑
    file.progress = 100
    file.uploadingStatus = 'success'
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
</script>
```

## FilesUploadItem 类型

```typescript
interface FilesUploadItem {
  fileId: string                              // 文件唯一 ID
  fileName: string                            // 文件名
  fileSize: number                            // 文件大小（字节）
  fileType: string                            // 文件类型（MIME）
  fileUrl: string                             // 文件 URL（本地或上传后的 CDN）
  progress: number                            // 上传进度 0-100
  uploadingStatus: 'uploading' | 'success' | 'error'  // 上传状态
  file?: File                                 // 原始 File 对象
}
```

## FilesUploadErrorParams 类型

```typescript
interface FilesUploadErrorParams {
  message: string                             // 错误消息
  type: 'size' | 'count' | 'type'            // 错误类型
}
```

## 完整示例

### 聊天文件上传

```vue
<template>
  <div class="chat-upload-container">
    <!-- 拖放区域 -->
    <ElADragUpload
      v-model="fileList"
      :file-size-limit="10"
      :max-file-length="5"
      :accept="acceptTypes"
      :on-upload="handleUpload"
      :on-error-message="handleError"
      class="drag-area"
    >
      <div class="upload-content">
        <!-- 文件预览 -->
        <ElAFilesCard
          v-if="fileList.length"
          v-model="fileList"
          class="file-preview"
        />

        <!-- 输入框 -->
        <div class="input-wrapper">
          <ElASender
            v-model="message"
            v-model:loading="sending"
            placeholder="输入消息或拖拽文件..."
            @paste-file="handlePasteFile"
            @send="handleSend"
          >
            <!-- 上传按钮 -->
            <template #prefix>
              <ElAFilesUpload
                v-model="fileList"
                :file-size-limit="10"
                :max-file-length="5"
                :accept="acceptTypes"
                :on-upload="handleUpload"
                :on-error-message="handleError"
              >
                <button class="icon-btn" title="上传文件">
                  📎
                </button>
              </ElAFilesUpload>
            </template>

            <!-- 文件类型选择 -->
            <template #action-list>
              <select v-model="uploadType" class="type-select">
                <option value="all">所有文件</option>
                <option value="image">仅图片</option>
                <option value="document">仅文档</option>
              </select>
            </template>
          </ElASender>
        </div>
      </div>
    </ElADragUpload>

    <!-- 上传统计 -->
    <div v-if="fileList.length" class="upload-stats">
      <span>已选择 {{ fileList.length }} 个文件</span>
      <span>总大小: {{ totalSize }}</span>
      <button @click="clearAll" class="clear-btn">清空</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import {
  ElASender,
  ElAFilesCard,
  ElAFilesUpload,
  ElADragUpload,
  useFileOperation,
  type FilesUploadItem,
} from 'element-ai-vue'

const message = ref('')
const fileList = ref<FilesUploadItem[]>([])
const sending = ref(false)
const uploadType = ref('all')

// 根据类型确定可接受的文件
const acceptTypes = computed(() => {
  switch (uploadType.value) {
    case 'image':
      return ['.png', '.jpg', '.jpeg', '.gif', '.webp']
    case 'document':
      return ['.pdf', '.doc', '.docx', '.xls', '.xlsx', '.txt']
    default:
      return ['.pdf', '.doc', '.docx', '.png', '.jpg', '.jpeg', '.txt']
  }
})

// 计算总大小
const totalSize = computed(() => {
  const bytes = fileList.value.reduce((sum, file) => sum + file.fileSize, 0)
  return formatFileSize(bytes)
})

// 上传处理
const handleUpload = async (files: FilesUploadItem[]) => {
  for (const file of files) {
    try {
      // 创建 FormData
      const formData = new FormData()
      formData.append('file', file.file!)

      // 上传到服务器
      const xhr = new XMLHttpRequest()

      // 监听进度
      xhr.upload.addEventListener('progress', (e) => {
        if (e.lengthComputable) {
          file.progress = Math.round((e.loaded / e.total) * 100)
        }
      })

      // 监听完成
      xhr.addEventListener('load', () => {
        if (xhr.status === 200) {
          const response = JSON.parse(xhr.responseText)
          file.fileUrl = response.url  // 更新为 CDN 地址
          file.uploadingStatus = 'success'
        } else {
          file.uploadingStatus = 'error'
        }
      })

      // 监听错误
      xhr.addEventListener('error', () => {
        file.uploadingStatus = 'error'
      })

      // 发送请求
      xhr.open('POST', '/api/upload')
      xhr.send(formData)
    } catch (error) {
      file.uploadingStatus = 'error'
      console.error('上传失败:', error)
    }
  }
}

// 错误处理
const handleError = ({ message, type }) => {
  const messages = {
    size: '文件大小超过限制',
    count: '文件数量超过限制',
    type: '不支持的文件类型',
  }
  alert(`${messages[type]}: ${message}`)
}

// 粘贴文件处理
const { handleFileUpload: handlePasteFile } = useFileOperation(
  {
    fileSizeLimit: 10,
    maxFileLength: 5,
    accept: acceptTypes.value,
    onUpload: handleUpload,
    onErrorMessage: handleError,
  },
  fileList
)

// 发送消息
const handleSend = async (text: string) => {
  if (!text.trim() && fileList.value.length === 0) {
    return
  }

  sending.value = true

  try {
    // 发送到服务器
    await fetch('/api/chat', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        message: text,
        files: fileList.value.map(f => ({
          url: f.fileUrl,
          name: f.fileName,
          type: f.fileType,
        })),
      }),
    })

    // 清空
    message.value = ''
    fileList.value = []
  } finally {
    sending.value = false
  }
}

// 清空文件
const clearAll = () => {
  fileList.value = []
}

// 格式化文件大小
const formatFileSize = (bytes: number): string => {
  if (bytes < 1024) return bytes + ' B'
  if (bytes < 1024 * 1024) return (bytes / 1024).toFixed(2) + ' KB'
  return (bytes / (1024 * 1024)).toFixed(2) + ' MB'
}
</script>

<style scoped>
.chat-upload-container {
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

.upload-content {
  display: flex;
  flex-direction: column;
  gap: 16px;
  height: 100%;
}

.file-preview {
  flex-shrink: 0;
}

.input-wrapper {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
}

.icon-btn {
  padding: 8px 12px;
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 18px;
  border-radius: 6px;
  transition: background 0.2s;
}

.icon-btn:hover {
  background: rgba(0, 0, 0, 0.05);
}

.type-select {
  padding: 6px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 6px;
  font-size: 13px;
  cursor: pointer;
  background: white;
}

.upload-stats {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-top: 12px;
  padding: 12px;
  background: #f5f5f5;
  border-radius: 8px;
  font-size: 14px;
  color: #666;
}

.clear-btn {
  margin-left: auto;
  padding: 6px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 12px;
}

.clear-btn:hover {
  background: #f5f5f5;
}
</style>
```

## 使用技巧

### 1. 文件类型限制

```typescript
// 图片
accept: ['.png', '.jpg', '.jpeg', '.gif', '.webp']

// 文档
accept: ['.pdf', '.doc', '.docx', '.xls', '.xlsx', '.ppt', '.pptx']

// 压缩包
accept: ['.zip', '.rar', '.7z']

// 所有类型
accept: []
```

### 2. 文件大小格式化

```typescript
const formatFileSize = (bytes: number): string => {
  const units = ['B', 'KB', 'MB', 'GB']
  let size = bytes
  let unitIndex = 0

  while (size >= 1024 && unitIndex < units.length - 1) {
    size /= 1024
    unitIndex++
  }

  return `${size.toFixed(2)} ${units[unitIndex]}`
}
```

### 3. 上传进度模拟

```typescript
const mockUpload = (file: FilesUploadItem) => {
  return new Promise<void>((resolve) => {
    let progress = 0
    const interval = setInterval(() => {
      progress += Math.random() * 15
      if (progress >= 100) {
        progress = 100
        clearInterval(interval)
        resolve()
      }
      file.progress = Math.min(progress, 100)
    }, 200)
  })
}
```

### 4. 真实上传（XMLHttpRequest）

```typescript
const uploadFile = (file: FilesUploadItem): Promise<string> => {
  return new Promise((resolve, reject) => {
    const formData = new FormData()
    formData.append('file', file.file!)

    const xhr = new XMLHttpRequest()

    xhr.upload.onprogress = (e) => {
      if (e.lengthComputable) {
        file.progress = (e.loaded / e.total) * 100
      }
    }

    xhr.onload = () => {
      if (xhr.status === 200) {
        const response = JSON.parse(xhr.responseText)
        file.uploadingStatus = 'success'
        resolve(response.url)
      } else {
        file.uploadingStatus = 'error'
        reject(new Error('Upload failed'))
      }
    }

    xhr.onerror = () => {
      file.uploadingStatus = 'error'
      reject(new Error('Network error'))
    }

    xhr.open('POST', '/api/upload')
    xhr.send(formData)
  })
}
```

### 5. 真实上传（Fetch + ReadableStream）

```typescript
const uploadFile = async (file: FilesUploadItem): Promise<string> => {
  const formData = new FormData()
  formData.append('file', file.file!)

  const response = await fetch('/api/upload', {
    method: 'POST',
    body: formData,
  })

  if (!response.ok) {
    file.uploadingStatus = 'error'
    throw new Error('Upload failed')
  }

  const data = await response.json()
  file.uploadingStatus = 'success'
  return data.url
}
```

## 注意事项

1. **CSS 导入**：必须导入样式
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

2. **进度更新**：
   - 必须手动更新 `file.progress` 和 `file.uploadingStatus`
   - 组件不会自动上传文件

3. **文件 URL**：
   - 初始 `fileUrl` 是本地 blob URL
   - 上传成功后应更新为 CDN 地址

4. **错误处理**：
   - 文件大小超限
   - 文件数量超限
   - 文件类型不支持
   - 上传失败

5. **性能优化**：
   - 大文件考虑分片上传
   - 多文件考虑并发控制
   - 添加重试机制
