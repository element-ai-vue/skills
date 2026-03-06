# ElAOverflowToolbar 组件

溢出工具栏组件，当工具栏项目过多时自动收起到更多菜单中。

## 组件导入

```typescript
import { ElAOverflowToolbar } from 'element-ai-vue'
```

## 基础用法

```vue
<template>
  <ElAOverflowToolbar>
    <button>操作1</button>
    <button>操作2</button>
    <button>操作3</button>
    <button>操作4</button>
    <button>操作5</button>
  </ElAOverflowToolbar>
</template>

<script setup lang="ts">
import { ElAOverflowToolbar } from 'element-ai-vue'
</script>
```

## 使用场景

### 1. 消息操作栏

```vue
<template>
  <ElABubble :content="message">
    <template #footer>
      <ElAOverflowToolbar>
        <button @click="copy">复制</button>
        <button @click="regenerate">重新生成</button>
        <button @click="share">分享</button>
        <button @click="edit">编辑</button>
        <button @click="delete">删除</button>
      </ElAOverflowToolbar>
    </template>
  </ElABubble>
</template>
```

### 2. 编辑器工具栏

```vue
<template>
  <div class="editor">
    <ElAOverflowToolbar class="toolbar">
      <button>加粗</button>
      <button>斜体</button>
      <button>下划线</button>
      <button>删除线</button>
      <button>插入链接</button>
      <button>插入图片</button>
      <button>插入代码</button>
    </ElAOverflowToolbar>

    <textarea v-model="content"></textarea>
  </div>
</template>
```

## 注意事项

1. 组件会自动检测容器宽度
2. 超出部分自动收起到"更多"菜单
3. 响应式设计，适配不同屏幕尺寸
