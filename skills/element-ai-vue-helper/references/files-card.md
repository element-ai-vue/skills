# ElAFilesCard 组件

文件预览卡片组件，用于显示文件列表、上传进度和文件信息。

## 组件导入

```typescript
import { ElAFilesCard } from 'element-ai-vue'
```

## Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| v-model | 文件列表 | `FilesUploadItem[]` | `[]` |

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

## 基础用法

### 1. 简单文件列表

```vue
<template>
  <div class="file-container">
    <ElAFilesCard v-model="fileList" />

    <ElAFilesUpload
      v-model="fileList"
      :on-upload="handleUpload"
    >
      <button class="add-btn">+ 添加文件</button>
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
.file-container {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.add-btn {
  padding: 10px 20px;
  border: 2px dashed #ddd;
  border-radius: 8px;
  background: white;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.3s;
}

.add-btn:hover {
  border-color: #667eea;
  background: #f5f5ff;
}
</style>
```

### 2. 带删除功能

```vue
<template>
  <div class="file-list">
    <div class="header">
      <h3>已上传文件 ({{ fileList.length }})</h3>
      <button @click="clearAll" class="clear-btn">清空全部</button>
    </div>

    <ElAFilesCard v-model="fileList" />

    <div v-if="!fileList.length" class="empty">
      暂无文件
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAFilesCard, type FilesUploadItem } from 'element-ai-vue'

const fileList = ref<FilesUploadItem[]>([
  {
    fileId: '1',
    fileName: 'document.pdf',
    fileSize: 1024 * 1024 * 2.5,
    fileType: 'application/pdf',
    fileUrl: 'https://example.com/doc.pdf',
    progress: 100,
    uploadingStatus: 'success',
  },
  {
    fileId: '2',
    fileName: 'image.png',
    fileSize: 1024 * 500,
    fileType: 'image/png',
    fileUrl: 'https://example.com/img.png',
    progress: 100,
    uploadingStatus: 'success',
  },
])

const clearAll = () => {
  fileList.value = []
}
</script>

<style scoped>
.file-list {
  padding: 20px;
  border: 1px solid #e0e0e0;
  border-radius: 12px;
  background: white;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 500;
  color: #333;
}

.clear-btn {
  padding: 6px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: white;
  cursor: pointer;
  font-size: 13px;
  transition: all 0.2s;
}

.clear-btn:hover {
  background: #f5f5f5;
}

.empty {
  padding: 40px;
  text-align: center;
  color: #999;
  font-size: 14px;
}
</style>
```

### 3. 在聊天场景中使用

```vue
<template>
  <div class="chat-container">
    <ElADragUpload
      v-model="fileList"
      :file-size-limit="10"
      :max-file-length="5"
      :accept="['.pdf', '.png', '.jpg', '.docx']"
      :on-upload="handleUpload"
      :on-error-message="handleError"
      class="upload-area"
    >
      <!-- 文件预览 -->
      <ElAFilesCard
        v-if="fileList.length"
        v-model="fileList"
        class="files-preview"
      />

      <!-- 输入框 -->
      <ElASender
        v-model="message"
        v-model:loading="sending"
        placeholder="输入消息..."
        @paste-file="handlePasteFile"
        @send="handleSend"
      >
        <template #prefix>
          <ElAFilesUpload
            v-model="fileList"
            :file-size-limit="10"
            :accept="['.pdf', '.png', '.jpg', '.docx']"
            :on-upload="handleUpload"
          >
            <button class="attach-btn">📎</button>
          </ElAFilesUpload>
        </template>
      </ElASender>
    </ElADragUpload>

    <!-- 文件统计 -->
    <div v-if="fileList.length" class="stats">
      <span>{{ fileList.length }} 个文件</span>
      <span>{{ totalSize }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import {
  ElAFilesCard,
  ElAFilesUpload,
  ElADragUpload,
  ElASender,
  useFileOperation,
  type FilesUploadItem,
} from 'element-ai-vue'

const fileList = ref<FilesUploadItem[]>([])
const message = ref('')
const sending = ref(false)

const totalSize = computed(() => {
  const bytes = fileList.value.reduce((sum, file) => sum + file.fileSize, 0)
  return formatFileSize(bytes)
})

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
  const messages = {
    size: '文件大小超过限制',
    count: '文件数量超过限制',
    type: '不支持的文件类型',
  }
  alert(`${messages[type]}: ${message}`)
}

const { handleFileUpload: handlePasteFile } = useFileOperation(
  {
    fileSizeLimit: 10,
    maxFileLength: 5,
    accept: ['.pdf', '.png', '.jpg', '.docx'],
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
    await new Promise(resolve => setTimeout(resolve, 1000))

    message.value = ''
    fileList.value = []
  } finally {
    sending.value = false
  }
}

const formatFileSize = (bytes: number): string => {
  if (bytes < 1024) return bytes + ' B'
  if (bytes < 1024 * 1024) return (bytes / 1024).toFixed(2) + ' KB'
  return (bytes / (1024 * 1024)).toFixed(2) + ' MB'
}
</script>

<style scoped>
.chat-container {
  max-width: 800px;
  margin: 0 auto;
}

.upload-area {
  min-height: 300px;
  border: 2px dashed #e0e0e0;
  border-radius: 12px;
  padding: 16px;
  background: white;
  transition: all 0.3s;
}

.upload-area:hover {
  border-color: #667eea;
  background: #fafafe;
}

.files-preview {
  margin-bottom: 16px;
}

.attach-btn {
  padding: 8px 12px;
  border: none;
  background: transparent;
  cursor: pointer;
  font-size: 18px;
  border-radius: 6px;
  transition: background 0.2s;
}

.attach-btn:hover {
  background: rgba(0, 0, 0, 0.05);
}

.stats {
  display: flex;
  gap: 16px;
  margin-top: 12px;
  padding: 12px;
  background: #f5f5f5;
  border-radius: 8px;
  font-size: 13px;
  color: #666;
}
</style>
```

## 显示特性

### 1. 文件信息展示

ElAFilesCard 自动显示以下信息：
- 文件图标（根据文件类型）
- 文件名
- 文件大小
- 上传进度条
- 上传状态（上传中/成功/失败）
- 删除按钮

### 2. 上传状态

```typescript
// 上传中
file.uploadingStatus = 'uploading'
file.progress = 50  // 0-100

// 上传成功
file.uploadingStatus = 'success'
file.progress = 100

// 上传失败
file.uploadingStatus = 'error'
```

### 3. 删除文件

用户可以点击文件卡片上的删除按钮来移除文件。删除操作会自动更新 v-model 绑定的文件列表。

## 与其他组件配合

### 1. 与 ElAFilesUpload 配合

```vue
<template>
  <!-- 文件预览 -->
  <ElAFilesCard v-model="fileList" />

  <!-- 上传按钮 -->
  <ElAFilesUpload
    v-model="fileList"
    :on-upload="handleUpload"
  >
    <button>添加文件</button>
  </ElAFilesUpload>
</template>
```

### 2. 与 ElADragUpload 配合

```vue
<template>
  <ElADragUpload
    v-model="fileList"
    :on-upload="handleUpload"
  >
    <!-- 文件预览 -->
    <ElAFilesCard v-if="fileList.length" v-model="fileList" />

    <!-- 空状态提示 -->
    <div v-else class="empty-hint">
      拖拽文件到这里
    </div>
  </ElADragUpload>
</template>
```

### 3. 与 ElASender 配合

```vue
<template>
  <div class="chat-input">
    <!-- 文件预览 -->
    <ElAFilesCard
      v-if="fileList.length"
      v-model="fileList"
    />

    <!-- 输入框 -->
    <ElASender
      v-model="message"
      @paste-file="handlePasteFile"
      @send="handleSend"
    >
      <template #prefix>
        <ElAFilesUpload v-model="fileList" :on-upload="handleUpload">
          <button>📎</button>
        </ElAFilesUpload>
      </template>
    </ElASender>
  </div>
</template>
```

## 完整示例

### 文件管理器

```vue
<template>
  <div class="file-manager">
    <div class="header">
      <h2>文件管理器</h2>
      <div class="actions">
        <ElAFilesUpload
          v-model="fileList"
          :file-size-limit="20"
          :max-file-length="10"
          :accept="acceptTypes"
          :on-upload="handleUpload"
          :on-error-message="handleError"
        >
          <button class="upload-btn">
            <span class="icon">📤</span>
            上传文件
          </button>
        </ElAFilesUpload>

        <button
          v-if="fileList.length"
          @click="downloadAll"
          class="download-btn"
        >
          <span class="icon">📥</span>
          下载全部
        </button>
      </div>
    </div>

    <div class="filters">
      <select v-model="filterType" class="filter-select">
        <option value="all">所有文件</option>
        <option value="image">图片</option>
        <option value="document">文档</option>
        <option value="uploading">上传中</option>
        <option value="success">已完成</option>
        <option value="error">失败</option>
      </select>

      <input
        v-model="searchText"
        type="text"
        placeholder="搜索文件名..."
        class="search-input"
      />
    </div>

    <div class="stats-bar">
      <div class="stat">
        <span class="label">总文件:</span>
        <span class="value">{{ fileList.length }}</span>
      </div>
      <div class="stat">
        <span class="label">总大小:</span>
        <span class="value">{{ totalSize }}</span>
      </div>
      <div class="stat">
        <span class="label">上传中:</span>
        <span class="value">{{ uploadingCount }}</span>
      </div>
      <div class="stat">
        <span class="label">已完成:</span>
        <span class="value">{{ successCount }}</span>
      </div>
    </div>

    <div class="file-list-container">
      <ElAFilesCard
        v-if="filteredFiles.length"
        v-model="filteredFiles"
      />

      <div v-else class="empty-state">
        <div class="icon">📂</div>
        <div class="text">暂无文件</div>
        <div class="hint">点击上方按钮上传文件</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import {
  ElAFilesCard,
  ElAFilesUpload,
  type FilesUploadItem,
} from 'element-ai-vue'

const fileList = ref<FilesUploadItem[]>([])
const filterType = ref('all')
const searchText = ref('')

const acceptTypes = [
  '.pdf', '.doc', '.docx', '.xls', '.xlsx',
  '.png', '.jpg', '.jpeg', '.gif', '.webp',
  '.zip', '.rar', '.txt',
]

// 过滤后的文件列表
const filteredFiles = computed(() => {
  let files = fileList.value

  // 按类型过滤
  if (filterType.value !== 'all') {
    if (filterType.value === 'image') {
      files = files.filter(f => f.fileType.startsWith('image/'))
    } else if (filterType.value === 'document') {
      files = files.filter(f =>
        f.fileType.includes('pdf') ||
        f.fileType.includes('document') ||
        f.fileType.includes('sheet')
      )
    } else {
      files = files.filter(f => f.uploadingStatus === filterType.value)
    }
  }

  // 按文件名搜索
  if (searchText.value) {
    files = files.filter(f =>
      f.fileName.toLowerCase().includes(searchText.value.toLowerCase())
    )
  }

  return files
})

// 统计信息
const totalSize = computed(() => {
  const bytes = fileList.value.reduce((sum, file) => sum + file.fileSize, 0)
  return formatFileSize(bytes)
})

const uploadingCount = computed(() =>
  fileList.value.filter(f => f.uploadingStatus === 'uploading').length
)

const successCount = computed(() =>
  fileList.value.filter(f => f.uploadingStatus === 'success').length
)

// 上传处理
const handleUpload = async (files: FilesUploadItem[]) => {
  for (const file of files) {
    try {
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
      }, 300)
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

// 下载全部
const downloadAll = () => {
  fileList.value
    .filter(f => f.uploadingStatus === 'success')
    .forEach(file => {
      const a = document.createElement('a')
      a.href = file.fileUrl
      a.download = file.fileName
      a.click()
    })
}

// 格式化文件大小
const formatFileSize = (bytes: number): string => {
  if (bytes < 1024) return bytes + ' B'
  if (bytes < 1024 * 1024) return (bytes / 1024).toFixed(2) + ' KB'
  return (bytes / (1024 * 1024)).toFixed(2) + ' MB'
}
</script>

<style scoped>
.file-manager {
  max-width: 1000px;
  margin: 0 auto;
  padding: 24px;
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
}

.header h2 {
  margin: 0;
  font-size: 24px;
  font-weight: 600;
  color: #333;
}

.actions {
  display: flex;
  gap: 12px;
}

.upload-btn,
.download-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s;
}

.upload-btn {
  background: #667eea;
  color: white;
}

.upload-btn:hover {
  background: #5568d3;
}

.download-btn {
  background: #f5f5f5;
  color: #333;
}

.download-btn:hover {
  background: #e0e0e0;
}

.icon {
  font-size: 16px;
}

.filters {
  display: flex;
  gap: 12px;
  margin-bottom: 20px;
}

.filter-select,
.search-input {
  padding: 10px 16px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  font-size: 14px;
}

.filter-select {
  width: 150px;
  cursor: pointer;
}

.search-input {
  flex: 1;
}

.stats-bar {
  display: flex;
  gap: 24px;
  padding: 16px;
  background: #f9f9f9;
  border-radius: 8px;
  margin-bottom: 20px;
}

.stat {
  display: flex;
  gap: 8px;
  font-size: 14px;
}

.stat .label {
  color: #666;
}

.stat .value {
  font-weight: 600;
  color: #333;
}

.file-list-container {
  min-height: 300px;
}

.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 300px;
  padding: 40px;
}

.empty-state .icon {
  font-size: 64px;
  margin-bottom: 16px;
  opacity: 0.3;
}

.empty-state .text {
  font-size: 18px;
  font-weight: 500;
  color: #666;
  margin-bottom: 8px;
}

.empty-state .hint {
  font-size: 14px;
  color: #999;
}
</style>
```

## 注意事项

1. **CSS 导入**：必须导入样式
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

2. **双向绑定**：
   - 使用 `v-model` 绑定文件列表
   - 删除文件会自动更新列表

3. **进度显示**：
   - 组件自动显示上传进度条
   - 需要手动更新 `file.progress` 和 `file.uploadingStatus`

4. **状态图标**：
   - `uploading` - 显示进度条和百分比
   - `success` - 显示成功图标
   - `error` - 显示错误图标

5. **文件删除**：
   - 用户点击删除按钮会从列表中移除文件
   - 可以监听列表变化来执行额外操作
