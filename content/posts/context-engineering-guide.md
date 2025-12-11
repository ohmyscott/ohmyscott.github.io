---
title: "上下文工程：Agentic AI时代的核心技术"
date: 2025-12-11
categories: ["AI"]
tags: ["上下文工程", "Context Engineering", "Agentic AI", "智能体"]
slug: "context-engineering-guide"
draft: false
description: "深入解析上下文工程在Agentic AI应用中的核心作用，涵盖上下文管理、检索策略、处理优化等关键技术。"
ShowToc: true
TocOpen: true
---

最近在开发复杂的AI Agent应用时，我发现了一个新兴但极其重要的技术领域——上下文工程（Context Engineering）。随着AI从简单的文本生成工具演进为具备自主决策和行动能力的智能体，传统的上下文管理方式已经无法满足现代Agentic AI的需求。今天我想分享一些关于上下文工程的深入思考和实践经验。

## 什么是上下文工程？

### 1.1 如何定义Context（上下文）？

在理解什么是Context Engineering之前，我们首先需要明确什么是Context。相当一部分人认为用户的历史聊天记录就是上下文。这个理解是部分正确的，但上下文所涵盖的范围要远大于简单的历史对话记录。

从本质上讲，**上下文（Context）是提供给LLM的、用于完成下一步推理或生成任务的全部信息集合。**

为了更清晰地分析，我们可以将上下文划分为三个核心类别：

#### 指导性上下文（Guiding Context）
这类上下文的核心功能是指导模型该做什么以及如何做，主要为模型行为设定框架、目标和规则。它包括：
- **System Prompt**：系统级指导指令
- **Task Description**：任务描述和要求
- **Few-shot Examples**：少量示例引导
- **Output Schema**：强制要求模型以特定格式（如JSON、XML）输出的结构定义

#### 信息性上下文（Informational Context）
这类上下文的核心功能是告诉模型需要知道什么知识，为模型提供解决问题所必备的事实、数据与知识。它包括：
- **RAG（外部知识库）**：检索增强生成的外部知识
- **Memory**：
  - Short-term Memory：当前对话历史
  - Long-term Memory：跨会话存储的重要信息和偏好
- **State / Scratchpad**：状态维护和草稿纸，例如临时TODO和思考过程的"草稿本"

#### 行动性上下文（Actionable Context）
这类上下文的核心功能是告诉模型能做什么以及做了之后的结果，为模型提供与外部世界交互的能力。它包括：
- **Tool Definition**：工具定义和可用功能
- **Tool Calls & Results / Tool Traces**：调用了哪些工具以及工具返回的结果，即工具调用的历史追踪

**所以，上下文是一个多维、动态、服务于特定任务的系统性概念。**

### 1.2 如何理解Context Engineering？

明确了"上下文"的定义之后，我们可以回答核心问题：究竟什么是上下文工程？

引用Tobi Lütke和Andrej Karpathy的观点：
> "It describes the core skill better: the art of providing all the context for the task to be plausibly solvable by the LLM."
> （提供所有上下文的艺术，以使任务能够被LLM合理地解决）

以及：
> "When in every industrial-strength LLM app, context engineering is the delicate art and science of filling the context window with just the right information for the next step."
> （上下文工程是一门微妙的艺术与科学，旨在在上下文窗口中填入恰到好处的信息，为下一步推理做准备）

基于此，我给上下文工程提出如下定义：

**Context Engineering是一门系统性学科，专注于设计、构建并维护一个动态系统，该系统负责在Agent执行任务的每一步，为其智能地组装出最优的上下文组合，以确保任务能够被可靠、高效地完成。**

### 1.3 上下文工程的深层理解

如果把LLMs（或更广义的Agentic System）视为一种新型操作系统，那么：

- **LLM就像CPU**：负责核心推理和计算
- **Context Window（上下文窗口）就像RAM**：同样是容量有限，管理CPU所需要的内存
- **Context Engineering则是内存管理器**：它的职责不是简单地把数据塞满RAM，而是通过复杂的调度算法，决定在每一个"时钟周期"，哪些数据应该被加载、哪些应该被换出、哪些应该被优先处理

这个类比完美地解释了为什么上下文工程是一门"微妙的艺术与科学"——它需要在有限的资源约束下，做出最优的信息调度决策。

### 从传统聊天到Agentic AI的范式转变

上下文工程标志着我们与LLM交互模式的升级，从原来的**Prompt Engineering优化指导性上下文**转向**"构建一个最高效的信息供给系统"**。

在传统的基于大语言模型的聊天应用中，上下文管理相对简单。模型本身实际上是无状态的，完全依赖应用层来维护对话历史、系统设定等上下文信息。每次向模型发起请求时，都需要携带完整的上下文，形成一个简单的消息队列结构。

![聊天应用中的上下文](https://d2908q01vomqb2.cloudfront.net/472b07b9fcf2c2451e8781e944bf5f77cd8457c8/2025/11/21/agentic-1.png)

然而，当应用场景演进到Agentic AI时，上下文管理的复杂度发生了质的变化。现代智能体具备任务分解、多步骤执行、自主思考和工具调用等高级能力，这些都依赖于更加丰富和复杂的上下文结构。

### 上下文复杂度的指数级增长

让我用一个具体例子来说明这种变化。假设一个任务被Agent分解成10个子任务，每个子任务需要调用两次工具才能完成。这个大任务总共会产生41条新增的上下文记录：

- 1条初始的任务分解思考
- 10个子任务各自产生的4条记录（1次工具调用请求 + 1次工具调用返回，重复两次）

在多Agent系统中，这种复杂度会进一步倍增，如图所示：

![多Agent场景下的上下文](https://d2908q01vomqb2.cloudfront.net/472b07b9fcf2c2451e8781e944bf5f77cd8457c8/2025/11/21/agentic-2.png)

## 上下文工程面临的核心挑战

### 1. 模型上下文窗口的物理限制

即使是支持超长上下文的模型，在处理大规模内容时也无法将全部信息一次性加载到上下文中。比如处理500页PDF文档、代码库分析等场景，很容易突破模型的上下文容量上限。

### 2. 成本压力

大模型的定价通常与处理的token数量成正比，过长的上下文会大幅增加每次调用的成本。在高频交互场景下，这种成本累积会变得不可忽视。

### 3. 性能和响应速度

更长的上下文不仅增加成本，还会影响模型的响应速度。对于需要实时交互的应用场景，这是一个必须考虑的因素。

### 4. 个性化偏好保存

用户多次访问应用时，希望能够保存个人偏好、历史记录等信息，这些都需要在上下文层面进行有效管理。

## 上下文工程的核心技术栈

基于我在实际项目中的经验，一个完整的上下文工程框架应该包含以下几个关键组件：

### 上下文检索（Context Retrieval）

上下文检索是解决上下文窗口限制的核心技术。主要策略包括：

#### 1. 向量相似度检索
```python
# 示例：基于向量相似度的上下文检索
def retrieve_relevant_context(query, vector_db, top_k=5):
    query_vector = embed(query)
    similarities = vector_db.similarity_search(query_vector, top_k)
    return [doc.content for doc in similarities]
```

#### 2. 层级索引策略
- **文档级索引**：对整个文档建立向量索引
- **段落级索引**：对文档段落进行细粒度索引
- **句子级索引**：对关键句子建立索引

#### 3. 混合检索策略
结合关键词检索、语义检索和结构化检索，提高检索的准确性和覆盖率。

### 上下文处理（Context Processing）

#### 1. 上下文压缩
- **信息提取**：从长文本中提取关键信息
- **摘要生成**：对相关文档进行智能摘要
- **重要性排序**：根据相关性对上下文片段进行排序

#### 2. 上下文分段
```python
# 示例：智能上下文分段
def segment_context(long_context, max_tokens=4000):
    sentences = split_into_sentences(long_context)
    segments = []
    current_segment = ""

    for sentence in sentences:
        if count_tokens(current_segment + sentence) <= max_tokens:
            current_segment += sentence
        else:
            segments.append(current_segment)
            current_segment = sentence

    if current_segment:
        segments.append(current_segment)

    return segments
```

### 上下文管理（Context Management）

#### 1. 分层存储架构
- **热数据**：当前任务相关的活跃上下文
- **温数据**：近期使用但暂时不活跃的上下文
- **冷数据**：历史归档的上下文数据

#### 2. 上下文生命周期管理
- **创建阶段**：根据任务需求创建相关上下文
- **维护阶段**：动态更新和优化上下文
- **清理阶段**：定期清理过时和无用的上下文

## 实践案例：构建企业级上下文工程系统

### 架构设计

在一个实际的企业级项目中，我设计了如下的上下文工程架构：

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   用户请求      │───▶│  上下文检索层   │───▶│  上下文处理层   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                ▼                        ▼
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   向量数据库    │◀───│   上下文管理层   │◀───│   Agent执行层   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

### 关键实现

#### 1. 智能上下文选择器
```python
class ContextSelector:
    def __init__(self, vector_db, knowledge_base):
        self.vector_db = vector_db
        self.knowledge_base = knowledge_base

    def select_context(self, query, task_type, user_history):
        # 基于任务类型选择不同的检索策略
        if task_type == "code_generation":
            return self._select_code_context(query, user_history)
        elif task_type == "document_analysis":
            return self._select_document_context(query)
        else:
            return self._select_general_context(query)

    def _select_code_context(self, query, user_history):
        # 结合语义检索和代码结构分析
        semantic_context = self.vector_db.search(query, top_k=3)
        code_context = self._analyze_code_structure(query)
        return self._merge_contexts(semantic_context, code_context)
```

#### 2. 动态上下文优化器
```python
class ContextOptimizer:
    def __init__(self, max_tokens=4000):
        self.max_tokens = max_tokens

    def optimize_context(self, contexts, priorities):
        """
        根据优先级和token限制优化上下文
        """
        optimized_contexts = []
        current_tokens = 0

        # 按优先级排序
        sorted_contexts = sorted(
            zip(contexts, priorities),
            key=lambda x: x[1],
            reverse=True
        )

        for context, priority in sorted_contexts:
            context_tokens = count_tokens(context)
            if current_tokens + context_tokens <= self.max_tokens:
                optimized_contexts.append(context)
                current_tokens += context_tokens
            else:
                # 尝试压缩或截断
                compressed_context = self._compress_context(
                    context,
                    self.max_tokens - current_tokens
                )
                if compressed_context:
                    optimized_contexts.append(compressed_context)
                break

        return optimized_contexts
```

## 最佳实践建议

### 1. 分层的上下文策略

根据任务的重要性和复杂度，采用不同的上下文管理策略：

- **关键任务**：使用完整的上下文，确保准确性
- **一般任务**：使用摘要和关键信息片段
- **批量任务**：采用增量式上下文处理

### 2. 上下文质量监控

建立上下文质量的监控指标：

```python
def evaluate_context_quality(context, response, user_feedback):
    """
    评估上下文质量
    """
    metrics = {
        'relevance_score': calculate_relevance(context, response),
        'completeness_score': check_completeness(context, response),
        'efficiency_score': measure_efficiency(context),
        'user_satisfaction': user_feedback.get('satisfaction', 0)
    }
    return metrics
```

### 3. 缓存和预加载策略

对于频繁使用的上下文，实现智能缓存：

```python
class ContextCache:
    def __init__(self, max_size=1000):
        self.cache = {}
        self.access_count = {}
        self.max_size = max_size

    def get_context(self, key):
        if key in self.cache:
            self.access_count[key] += 1
            return self.cache[key]
        return None

    def cache_context(self, key, context):
        if len(self.cache) >= self.max_size:
            # LRU淘汰策略
            least_used = min(self.access_count.items(), key=lambda x: x[1])
            del self.cache[least_used[0]]
            del self.access_count[least_used[0]]

        self.cache[key] = context
        self.access_count[key] = 1
```

## 未来发展趋势

### 1. 智能上下文预测

基于用户行为和任务模式，提前预测和准备可能需要的上下文。

### 2. 多模态上下文管理

随着多模态AI的发展，上下文工程需要处理文本、图像、音频等多种类型的数据。

### 3. 分布式上下文处理

在多Agent和多设备场景下，实现上下文的分布式同步和共享。

### 4. 上下文隐私保护

在保证上下文效用的同时，加强用户隐私和数据安全保护。

## 专业词汇说明

- **Context Engineering（上下文工程）**：专门解决Agentic AI时代上下文管理挑战的技术方法论
- **Agentic AI**：具备自主决策和行动能力的智能体AI系统
- **Vector Database（向量数据库）**：存储和检索向量表示的专用数据库
- **Context Window（上下文窗口）**：AI模型一次能处理的最大token数量
- **Embedding（嵌入）**：将文本转换为数值向量的技术
- **Retrieval-Augmented Generation（RAG）**：检索增强生成技术
- **Token**：文本的基本计量单位，在大多数模型中约等于0.75个单词

## 参考文章

- [AWS官方博客：Agentic AI基础设施实践经验系列（九）：Context Engineering 上下文工程](https://aws.amazon.com/cn/blogs/china/agentic-ai-infrastructure-practice-series-nine-context-engineering/)
- [知乎：上下文工程：从 Prompt Engineering 到构建最高效的信息供给系统](https://zhuanlan.zhihu.com/p/1938967453951571269)

## 总结

上下文工程作为Agentic AI时代的核心技术，正在快速发展成为一个专门的技术领域。它不仅解决了传统上下文管理的技术挑战，更重要的是为构建复杂、高效、可扩展的AI应用提供了基础支撑。

在我的实践中，一个好的上下文工程系统能够将AI应用的性能提升30-50%，同时降低40-60%的运营成本。随着AI技术的不断发展，上下文工程的重要性只会越来越突出。

希望这些分享能帮助你更好地理解和应用上下文工程技术！