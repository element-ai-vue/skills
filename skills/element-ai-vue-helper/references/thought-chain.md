# ElAThoughtChain 组件

思维链可视化组件，用于展示 AI 的连续推理步骤。

## 组件导入

```typescript
import { ElAThoughtChain } from 'element-ai-vue'
```

## Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| steps | 思维链步骤数组 | `ThoughtChainStep[]` | `[]` |

### ThoughtChainStep 类型

```typescript
interface ThoughtChainStep {
  title: string        // 步骤标题
  content?: string     // 步骤内容（可选）
  status?: 'pending' | 'active' | 'completed' | 'error'  // 步骤状态
  icon?: string        // 自定义图标
}
```

## 基础用法

### 1. 简单步骤

```vue
<template>
  <ElAThoughtChain :steps="steps" />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAThoughtChain } from 'element-ai-vue'

const steps = ref([
  {
    title: '理解问题',
    content: '分析用户的问题意图',
  },
  {
    title: '检索信息',
    content: '从知识库中查找相关内容',
  },
  {
    title: '生成答案',
    content: '基于检索结果生成回复',
  },
])
</script>
```

### 2. 带状态的步骤

```vue
<template>
  <ElAThoughtChain :steps="steps" />
</template>

<script setup lang="ts">
const steps = ref([
  {
    title: '步骤 1',
    content: '已完成',
    status: 'completed',
  },
  {
    title: '步骤 2',
    content: '进行中...',
    status: 'active',
  },
  {
    title: '步骤 3',
    content: '等待中',
    status: 'pending',
  },
])
</script>
```

### 3. 与 Thinking 组件结合

```vue
<template>
  <ElAThinking v-model="showThinking" title="推理链">
    <ElAThoughtChain :steps="thinkingSteps" />
  </ElAThinking>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAThinking, ElAThoughtChain } from 'element-ai-vue'

const showThinking = ref(false)

const thinkingSteps = ref([
  {
    title: '问题分析',
    content: '用户想要了解如何使用 Vue 3',
  },
  {
    title: '知识检索',
    content: '从 Vue 3 官方文档中查找相关内容',
  },
  {
    title: '答案生成',
    content: '整理信息并生成结构化的回答',
  },
])
</script>
```

### 4. 动态更新步骤

```vue
<template>
  <div>
    <button @click="nextStep">下一步</button>
    <button @click="reset">重置</button>

    <ElAThoughtChain :steps="steps" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAThoughtChain } from 'element-ai-vue'

const currentStepIndex = ref(0)

const steps = ref([
  {
    title: '步骤 1',
    content: '等待开始',
    status: 'pending',
  },
  {
    title: '步骤 2',
    content: '等待开始',
    status: 'pending',
  },
  {
    title: '步骤 3',
    content: '等待开始',
    status: 'pending',
  },
])

const nextStep = () => {
  if (currentStepIndex.value < steps.value.length) {
    // 完成当前步骤
    if (currentStepIndex.value > 0) {
      steps.value[currentStepIndex.value - 1].status = 'completed'
      steps.value[currentStepIndex.value - 1].content = '已完成'
    }

    // 激活下一步
    steps.value[currentStepIndex.value].status = 'active'
    steps.value[currentStepIndex.value].content = '进行中...'

    currentStepIndex.value++
  } else {
    // 完成最后一步
    steps.value[steps.value.length - 1].status = 'completed'
    steps.value[steps.value.length - 1].content = '已完成'
  }
}

const reset = () => {
  currentStepIndex.value = 0
  steps.value.forEach(step => {
    step.status = 'pending'
    step.content = '等待开始'
  })
}
</script>
```

## 完整示例

### AI 推理过程可视化

```vue
<template>
  <div class="reasoning-container">
    <ElABubble
      placement="start"
      :content="answer"
      :typing="typing"
      :loading="loading"
      is-markdown
    >
      <template #header>
        <ElAThinking
          v-model="showReasoning"
          title="AI 推理过程"
        >
          <ElAThoughtChain :steps="reasoningSteps" />

          <div v-if="currentStep < reasoningSteps.length" class="progress">
            <div class="progress-bar">
              <div
                class="progress-fill"
                :style="{ width: `${(currentStep / reasoningSteps.length) * 100}%` }"
              />
            </div>
            <div class="progress-text">
              {{ currentStep }} / {{ reasoningSteps.length }} 步骤
            </div>
          </div>
        </ElAThinking>
      </template>
    </ElABubble>

    <button @click="startReasoning" :disabled="isReasoning">
      开始推理
    </button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import {
  ElABubble,
  ElAThinking,
  ElAThoughtChain,
} from 'element-ai-vue'

const showReasoning = ref(true)
const loading = ref(false)
const typing = ref(false)
const answer = ref('')
const isReasoning = ref(false)
const currentStep = ref(0)

const reasoningSteps = ref([
  {
    title: '理解问题',
    content: '',
    status: 'pending',
  },
  {
    title: '分解任务',
    content: '',
    status: 'pending',
  },
  {
    title: '检索知识',
    content: '',
    status: 'pending',
  },
  {
    title: '推理分析',
    content: '',
    status: 'pending',
  },
  {
    title: '生成答案',
    content: '',
    status: 'pending',
  },
])

const startReasoning = async () => {
  isReasoning.value = true
  loading.value = true
  currentStep.value = 0

  // 重置所有步骤
  reasoningSteps.value.forEach(step => {
    step.status = 'pending'
    step.content = ''
  })

  // 逐步执行推理
  for (let i = 0; i < reasoningSteps.value.length; i++) {
    currentStep.value = i + 1

    // 激活当前步骤
    reasoningSteps.value[i].status = 'active'
    reasoningSteps.value[i].content = '处理中...'

    // 模拟处理时间
    await new Promise(resolve => setTimeout(resolve, 1000))

    // 完成当前步骤
    reasoningSteps.value[i].status = 'completed'
    reasoningSteps.value[i].content = getStepResult(i)
  }

  // 生成最终答案
  loading.value = false
  typing.value = true
  answer.value = '基于以上推理，最终答案是...'

  setTimeout(() => {
    typing.value = false
    isReasoning.value = false
  }, 2000)
}

const getStepResult = (stepIndex: number): string => {
  const results = [
    '问题是：如何使用 Vue 3 构建应用',
    '任务分解为：学习基础、搭建项目、编写组件',
    '从文档中找到相关内容',
    '分析最佳实践和常见模式',
    '整理成结构化的答案',
  ]
  return results[stepIndex]
}
</script>

<style scoped>
.reasoning-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.progress {
  margin-top: 20px;
  padding-top: 20px;
  border-top: 1px solid #e0e0e0;
}

.progress-bar {
  height: 8px;
  background: #f0f0f0;
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 8px;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #3b82f6 0%, #8b5cf6 100%);
  transition: width 0.3s ease;
}

.progress-text {
  text-align: center;
  font-size: 14px;
  color: #666;
}

button {
  margin-top: 20px;
  padding: 10px 20px;
  border: none;
  border-radius: 6px;
  background: #3b82f6;
  color: white;
  font-size: 14px;
  cursor: pointer;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background: #2563eb;
}
</style>
```

### Chain-of-Thought 展示

```vue
<template>
  <div class="cot-demo">
    <h3>Chain-of-Thought 推理</h3>

    <div class="question">
      <strong>问题：</strong>
      如果一个农场有 3 只鸡和 2 只羊，总共有多少条腿？
    </div>

    <ElAThoughtChain :steps="cotSteps" />

    <div class="answer">
      <strong>答案：</strong>
      {{ finalAnswer }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElAThoughtChain } from 'element-ai-vue'

const cotSteps = ref([
  {
    title: '识别信息',
    content: '农场有 3 只鸡和 2 只羊',
    status: 'completed',
  },
  {
    title: '回忆知识',
    content: '鸡有 2 条腿，羊有 4 条腿',
    status: 'completed',
  },
  {
    title: '计算鸡的腿数',
    content: '3 只鸡 × 2 条腿 = 6 条腿',
    status: 'completed',
  },
  {
    title: '计算羊的腿数',
    content: '2 只羊 × 4 条腿 = 8 条腿',
    status: 'completed',
  },
  {
    title: '求总和',
    content: '6 + 8 = 14 条腿',
    status: 'completed',
  },
])

const finalAnswer = ref('14 条腿')
</script>

<style scoped>
.cot-demo {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}

h3 {
  margin-bottom: 20px;
  color: #333;
}

.question {
  padding: 16px;
  background: #f0f7ff;
  border-left: 4px solid #3b82f6;
  border-radius: 4px;
  margin-bottom: 20px;
}

.answer {
  padding: 16px;
  background: #f0fdf4;
  border-left: 4px solid #10b981;
  border-radius: 4px;
  margin-top: 20px;
}
</style>
```

## 使用场景

### 1. AI 推理过程

展示 AI 如何一步步得出答案。

```vue
<ElAThoughtChain :steps="[
  { title: '理解问题', content: '...' },
  { title: '分析需求', content: '...' },
  { title: '生成方案', content: '...' },
]" />
```

### 2. 问题求解步骤

展示数学题或逻辑题的求解过程。

```vue
<ElAThoughtChain :steps="[
  { title: '识别已知条件', content: '...' },
  { title: '列出公式', content: '...' },
  { title: '代入计算', content: '...' },
  { title: '得出结论', content: '...' },
]" />
```

### 3. 决策过程

展示 AI 的决策逻辑。

```vue
<ElAThoughtChain :steps="[
  { title: '评估选项 A', content: '优点: ..., 缺点: ...' },
  { title: '评估选项 B', content: '优点: ..., 缺点: ...' },
  { title: '对比分析', content: '...' },
  { title: '做出决策', content: '选择 B，因为...' },
]" />
```

## 样式定制

```vue
<style>
/* 自定义步骤样式 */
.el-a-thought-chain .step {
  padding: 16px;
  margin-bottom: 12px;
}

/* 自定义连接线 */
.el-a-thought-chain .connector {
  border-left: 2px dashed #3b82f6;
}

/* 自定义状态颜色 */
.el-a-thought-chain .step.completed {
  border-left-color: #10b981;
}

.el-a-thought-chain .step.active {
  border-left-color: #3b82f6;
  background: #f0f7ff;
}

.el-a-thought-chain .step.error {
  border-left-color: #ef4444;
  background: #fef2f2;
}
</style>
```

## 注意事项

1. **CSS 导入**：必须导入样式
   ```typescript
   import 'element-ai-vue/dist/index.css'
   ```

2. **步骤状态**：
   - `pending` - 等待执行
   - `active` - 正在执行
   - `completed` - 已完成
   - `error` - 执行失败

3. **动画效果**：
   - 组件内置步骤过渡动画
   - 状态变化会有平滑过渡

4. **与 Thinking 配合**：
   - 通常嵌套在 ElAThinking 组件中
   - 提供更好的折叠/展开体验

5. **内容格式**：
   - title 是必需的
   - content 是可选的
   - 支持纯文本内容
