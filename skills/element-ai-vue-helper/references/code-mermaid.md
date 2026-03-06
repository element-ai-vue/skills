# ElACodeMermaid 组件

独立的 Mermaid 图表渲染组件，支持流程图、时序图等多种图表类型。

## 组件导入

```typescript
import { ElACodeMermaid } from 'element-ai-vue'
```

## Props

| 属性名 | 说明 | 类型 | 默认值 |
|--------|------|------|--------|
| content | Mermaid 代码 | `string` | `''` |
| theme | 主题 | `'light' \| 'dark'` | `'light'` |

## Slots

| 插槽名 | 说明 |
|--------|------|
| toolbar | 自定义工具栏 |
| fullscreen-toolbar | 自定义全屏工具栏 |

## 基础用法

### 1. 流程图

```vue
<template>
  <ElACodeMermaid :content="flowchart" />
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElACodeMermaid } from 'element-ai-vue'

const flowchart = ref(`
flowchart TD
    A[开始] --> B{判断条件}
    B -->|是| C[执行操作1]
    B -->|否| D[执行操作2]
    C --> E[结束]
    D --> E
`)
</script>
```

### 2. 时序图

```vue
<template>
  <ElACodeMermaid :content="sequence" />
</template>

<script setup lang="ts">
const sequence = ref(`
sequenceDiagram
    participant 用户
    participant 前端
    participant 后端
    participant 数据库

    用户->>前端: 发送请求
    前端->>后端: API 调用
    后端->>数据库: 查询数据
    数据库-->>后端: 返回数据
    后端-->>前端: 返回结果
    前端-->>用户: 显示结果
`)
</script>
```

### 3. 类图

```vue
<template>
  <ElACodeMermaid :content="classDiagram" />
</template>

<script setup lang="ts">
const classDiagram = ref(`
classDiagram
    class Animal {
        +String name
        +int age
        +makeSound()
    }

    class Dog {
        +String breed
        +bark()
    }

    class Cat {
        +String color
        +meow()
    }

    Animal <|-- Dog
    Animal <|-- Cat
`)
</script>
```

### 4. 状态图

```vue
<template>
  <ElACodeMermaid :content="stateDiagram" />
</template>

<script setup lang="ts">
const stateDiagram = ref(`
stateDiagram-v2
    [*] --> 待处理
    待处理 --> 处理中: 开始处理
    处理中 --> 已完成: 处理成功
    处理中 --> 失败: 处理失败
    失败 --> 待处理: 重试
    已完成 --> [*]
`)
</script>
```

### 5. 甘特图

```vue
<template>
  <ElACodeMermaid :content="gantt" />
</template>

<script setup lang="ts">
const gantt = ref(`
gantt
    title 项目开发计划
    dateFormat  YYYY-MM-DD

    section 设计阶段
    需求分析           :a1, 2024-01-01, 7d
    UI设计            :a2, after a1, 5d

    section 开发阶段
    前端开发          :b1, after a2, 14d
    后端开发          :b2, after a2, 14d

    section 测试阶段
    功能测试          :c1, after b1, 7d
    上线部署          :c2, after c1, 2d
`)
</script>
```

### 6. ER 图

```vue
<template>
  <ElACodeMermaid :content="erDiagram" />
</template>

<script setup lang="ts">
const erDiagram = ref(`
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ ORDER_ITEM : contains
    PRODUCT ||--o{ ORDER_ITEM : "ordered in"

    USER {
        int id PK
        string name
        string email
    }

    ORDER {
        int id PK
        int user_id FK
        datetime created_at
    }

    ORDER_ITEM {
        int id PK
        int order_id FK
        int product_id FK
        int quantity
    }

    PRODUCT {
        int id PK
        string name
        decimal price
    }
`)
</script>
```

## 自定义工具栏

```vue
<template>
  <ElACodeMermaid :content="diagram">
    <template #toolbar>
      <div class="custom-toolbar">
        <button @click="zoomIn">放大</button>
        <button @click="zoomOut">缩小</button>
        <button @click="download">下载</button>
        <button @click="fullscreen">全屏</button>
      </div>
    </template>
  </ElACodeMermaid>
</template>

<script setup lang="ts">
const zoomIn = () => {
  // 放大逻辑
}

const zoomOut = () => {
  // 缩小逻辑
}

const download = () => {
  // 下载逻辑
}

const fullscreen = () => {
  // 全屏逻辑
}
</script>

<style scoped>
.custom-toolbar {
  display: flex;
  gap: 8px;
  padding: 8px;
  background: #f5f5f5;
  border-bottom: 1px solid #e0e0e0;
}

.custom-toolbar button {
  padding: 4px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background: white;
  cursor: pointer;
  font-size: 12px;
}
</style>
```

## 支持的图表类型

- **flowchart** - 流程图
- **sequenceDiagram** - 时序图
- **classDiagram** - 类图
- **stateDiagram** - 状态图
- **erDiagram** - ER 图（实体关系图）
- **gantt** - 甘特图
- **pie** - 饼图
- **gitGraph** - Git 图

## 注意事项

1. **CSS 导入**：必须导入样式
2. **语法正确性**：Mermaid 语法必须正确，否则不会渲染
3. **性能**：复杂图表可能需要较长渲染时间
4. **主题**：支持 light 和 dark 主题
