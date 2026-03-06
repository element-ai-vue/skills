# ElAThinking 组件

可折叠的思考过程展示组件，用于显示 AI 的推理步骤或引用来源。

## 组件导入

```typescript
import { ElAThinking } from 'element-ai-vue'
```

## Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| v-model | 是否展开 | `boolean` | `false` |
| title | 标题文本 | `string` | - |

## Slots

| 插槽名 | 说明 |
|--------|------|
| default | 思考过程的内容 |
| title | 自定义标题内容 |
| action | 自定义操作区域内容 |

## 基础用法

### 1. 简单使用

```vue
<template>
  <ElAThinking v-model="isOpen" title="思考过程">
    <div>正在分析问题...</div>
    <div>查找相关信息...</div>
    <div>组织答案...</div>
  </ElAThinking>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAThinking } from 'element-ai-vue'

const isOpen = ref(false)
</script>
```

### 2. 与 Bubble 结合

```vue
<template>
  <ElABubble
    placement="start"
    :content="response"
    is-markdown
  >
    <template #header>
      <ElAThinking v-model="showThinking" title="推理过程">
        <div class="thinking-step">
          <strong>步骤 1:</strong> 理解问题
        </div>
        <div class="thinking-step">
          <strong>步骤 2:</strong> 搜索知识库
        </div>
        <div class="thinking-step">
          <strong>步骤 3:</strong> 形成答案
        </div>
      </ElAThinking>
    </template>
  </ElABubble>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElABubble, ElAThinking } from 'element-ai-vue'

const showThinking = ref(false)
const response = ref('这是最终的答案')
</script>

<style scoped>
.thinking-step {
  padding: 8px 0;
  line-height: 1.6;
}
</style>
```

### 3. 自定义标题

```vue
<template>
  <ElAThinking v-model="isOpen">
    <template #title>
      <div class="custom-title">
        <span class="icon">🤔</span>
        <span>AI 正在思考</span>
        <span class="badge">3 步骤</span>
      </div>
    </template>

    <div class="thinking-content">
      思考内容...
    </div>
  </ElAThinking>
</template>

<style scoped>
.custom-title {
  display: flex;
  align-items: center;
  gap: 8px;
}

.icon {
  font-size: 18px;
}

.badge {
  padding: 2px 8px;
  background: #f0f0f0;
  border-radius: 12px;
  font-size: 12px;
}
</style>
```

### 4. 自定义操作按钮

```vue
<template>
  <ElAThinking v-model="isOpen" title="思考过程">
    <template #action>
      <button @click="copyThinking" class="action-btn">
        复制
      </button>
      <button @click="shareThinking" class="action-btn">
        分享
      </button>
    </template>

    <div class="thinking-content">
      详细的思考过程...
    </div>
  </ElAThinking>
</template>

<script setup lang="ts">
const copyThinking = () => {
  // 复制逻辑
}

const shareThinking = () => {
  // 分享逻辑
}
</script>

<style scoped>
.action-btn {
  padding: 4px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 12px;
  margin-left: 8px;
}

.action-btn:hover {
  background: #f5f5f5;
}
</style>
```

## 与 ThoughtChain 结合

```vue
<template>
  <ElABubble placement="start" :content="answer" is-markdown>
    <template #header>
      <ElAThinking v-model="showThinking" title="推理链">
        <ElAThoughtChain :steps="thinkingSteps" />
      </ElAThinking>
    </template>
  </ElABubble>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElABubble, ElAThinking, ElAThoughtChain } from 'element-ai-vue'

const showThinking = ref(false)
const answer = ref('基于以上推理，答案是...')

const thinkingSteps = ref([
  {
    title: '理解问题',
    content: '用户想要了解...',
  },
  {
    title: '分析需求',
    content: '需要考虑以下几点...',
  },
  {
    title: '查找信息',
    content: '从知识库中找到...',
  },
  {
    title: '形成答案',
    content: '综合以上信息...',
  },
])
</script>
```

## 完整示例

```vue
<template>
  <div class="chat-container">
    <ElABubble
      placement="start"
      :content="aiResponse"
      is-markdown
      :typing="typing"
      :loading="loading"
    >
      <template #header>
        <ElAThinking
          v-model="showThinking"
          title="思考过程"
        >
          <template #action>
            <button
              @click="toggleAutoExpand"
              class="auto-expand-btn"
            >
              {{ autoExpand ? '自动展开' : '手动展开' }}
            </button>
          </template>

          <div class="thinking-steps">
            <div
              v-for="(step, index) in thinkingSteps"
              :key="index"
              class="step"
              :class="{ active: currentStep === index }"
            >
              <div class="step-header">
                <span class="step-number">{{ index + 1 }}</span>
                <span class="step-title">{{ step.title }}</span>
                <span v-if="currentStep === index" class="step-status">
                  进行中...
                </span>
              </div>
              <div v-if="step.content" class="step-content">
                {{ step.content }}
              </div>
            </div>
          </div>
        </ElAThinking>
      </template>

      <template #footer>
        <div class="message-actions">
          <button @click="regenerate">重新生成</button>
          <button @click="copyResponse">复制</button>
        </div>
      </template>
    </ElABubble>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElABubble, ElAThinking } from 'element-ai-vue'

const showThinking = ref(false)
const autoExpand = ref(true)
const loading = ref(false)
const typing = ref(false)
const aiResponse = ref('')
const currentStep = ref(0)

interface ThinkingStep {
  title: string
  content?: string
}

const thinkingSteps = ref<ThinkingStep[]>([
  { title: '理解问题' },
  { title: '分析需求' },
  { title: '查找信息' },
  { title: '形成答案' },
])

const toggleAutoExpand = () => {
  autoExpand.value = !autoExpand.value
}

const regenerate = () => {
  // 重新生成逻辑
}

const copyResponse = () => {
  navigator.clipboard.writeText(aiResponse.value)
}
</script>

<style scoped>
.chat-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.thinking-steps {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.step {
  padding: 12px;
  background: #f9f9f9;
  border-radius: 8px;
  border-left: 3px solid #e0e0e0;
  transition: all 0.3s;
}

.step.active {
  background: #f0f7ff;
  border-left-color: #3b82f6;
}

.step-header {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 8px;
}

.step-number {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 24px;
  height: 24px;
  background: #e0e0e0;
  border-radius: 50%;
  font-size: 12px;
  font-weight: bold;
}

.step.active .step-number {
  background: #3b82f6;
  color: white;
}

.step-title {
  font-weight: 500;
  color: #333;
}

.step-status {
  margin-left: auto;
  font-size: 12px;
  color: #3b82f6;
}

.step-content {
  padding-left: 32px;
  color: #666;
  font-size: 14px;
  line-height: 1.6;
}

.auto-expand-btn {
  padding: 4px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 12px;
}

.message-actions {
  display: flex;
  gap: 8px;
  margin-top: 12px;
}

.message-actions button {
  padding: 6px 12px;
  border: 1px solid #e0e0e0;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 13px;
}

.message-actions button:hover {
  background: #f5f5f5;
}
</style>
```

## 使用场景

### 1. 显示 AI 推理过程

```vue
<ElAThinking title="推理过程">
  <div>1. 分析用户问题</div>
  <div>2. 检索相关知识</div>
  <div>3. 生成候选答案</div>
  <div>4. 评估答案质量</div>
</ElAThinking>
```

### 2. 显示引用来源

```vue
<ElAThinking title="引用来源">
  <div class="source">
    <strong>文档 1:</strong> Vue 3 官方文档
  </div>
  <div class="source">
    <strong>文档 2:</strong> TypeScript 手册
  </div>
</ElAThinking>
```

### 3. 显示计算步骤

```vue
<ElAThinking title="计算过程">
  <div>原价: ¥100</div>
  <div>折扣: 8折</div>
  <div>优惠券: -¥10</div>
  <div>最终价格: ¥70</div>
</ElAThinking>
```

## 注意事项

1. **CSS 导入**：必须导入样式
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

2. **展开状态**：
   - 使用 `v-model` 控制展开/收起
   - 默认是收起状态

3. **平滑动画**：
   - 组件内置高度过渡动画
   - 无需额外配置

4. **与 Bubble 配合**：
   - 通常放在 Bubble 的 header 插槽中
   - 适合显示 AI 的思考过程

5. **内容格式**：
   - 支持任意 HTML 内容
   - 可以嵌套其他组件（如 ThoughtChain）
