# ElABubbleList 组件

消息列表容器组件，提供自动滚动功能，专为聊天消息设计。

## 组件导入

```typescript
import { ElABubbleList } from 'element-ai-vue'
```

## Exposes

| 方法名 | 说明 | 类型 |
|--------|------|------|
| scrollToBottom | 滚动到底部 | `(smooth?: boolean) => void` |

## 基础用法

```vue
<template>
  <ElABubbleList ref="bubbleListRef" class="message-list">
    <ElABubble
      v-for="msg in messages"
      :key="msg.id"
      :placement="msg.role === 'user' ? 'end' : 'start'"
      :content="msg.content"
    />
  </ElABubbleList>
</template>

<script setup lang="ts">
import { ref, nextTick } from 'vue'
import { ElABubbleList, ElABubble } from 'element-ai-vue'

const bubbleListRef = ref()
const messages = ref([])

// 添加消息后滚动到底部
const addMessage = async (message) => {
  messages.value.push(message)
  await nextTick()
  bubbleListRef.value?.scrollToBottom(true)
}
</script>

<style scoped>
.message-list {
  height: 600px;
  overflow-y: auto;
}
</style>
```

## 自动滚动

```vue
<script setup lang="ts">
import { watch } from 'vue'

// 监听消息变化，自动滚动
watch(
  () => messages.value.length,
  async () => {
    await nextTick()
    bubbleListRef.value?.scrollToBottom(true)
  }
)
</script>
```

## 完整示例

```vue
<template>
  <div class="chat-container">
    <ElABubbleList ref="bubbleListRef" class="messages">
      <ElABubble
        v-for="msg in messages"
        :key="msg.id"
        :placement="msg.role === 'user' ? 'end' : 'start'"
        :content="msg.content"
        :typing="msg.typing"
        :loading="msg.loading"
      />
    </ElABubbleList>

    <ElASender
      v-model="input"
      @send="handleSend"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, nextTick } from 'vue'
import {
  ElABubbleList,
  ElABubble,
  ElASender,
} from 'element-ai-vue'

const bubbleListRef = ref()
const messages = ref([])
const input = ref('')

const handleSend = async (content: string) => {
  messages.value.push({
    id: Date.now().toString(),
    role: 'user',
    content,
  })

  await nextTick()
  bubbleListRef.value?.scrollToBottom(true)

  input.value = ''
}
</script>

<style scoped>
.chat-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

.messages {
  flex: 1;
  overflow-y: auto;
  padding: 20px;
}
</style>
```

## 注意事项

1. 需要设置固定高度和 `overflow-y: auto`
2. 使用 `scrollToBottom(true)` 实现平滑滚动
3. 添加消息后记得调用 `nextTick` 再滚动
