# AI智能体开发实习 - 完整实战手册

> **岗位**: 智能体开发工程师（实习）  
> **版本**: 2026年2月完整版  
> **涵盖**: LangChain | LlamaIndex | RAG | 向量数据库 | FastAPI | Prompt Engineering

---

# 目录

1. [术语速查表](#一术语速查表)
2. [LangChain Agent开发](#二langchain-agent开发)
3. [LlamaIndex RAG实现](#三llamaindex-rag实现)
4. [向量数据库实战](#四向量数据库实战)
5. [FastAPI AI后端](#五fastapi-ai后端)
6. [Prompt Engineering](#六prompt-engineering)
7. [多Agent协同架构](#七多agent协同架构)
8. [LLM API调用与错误处理](#八llm-api调用与错误处理)
9. [RAG优化与评估](#九rag优化与评估)
10. [常见问题排查](#十常见问题排查)
11. [实习第一天检查清单](#十一实习第一天检查清单)

---

# 一、术语速查表

## 1.1 LLM基础术语

| 术语 | 英文 | 解释 |
|------|------|------|
| Token | Token | 文本最小单位，中文约1.5字/token，英文约4字符/token |
| Context Window | 上下文窗口 | 模型一次能处理的最大token数（GPT-4: 128k, Claude: 200k） |
| Temperature | 温度 | 控制输出随机性，0=确定性强，1=创意性高 |
| Top-p | 核采样 | 概率阈值采样，和Temperature配合控制输出质量 |
| Few-shot | 少样本学习 | 给几个例子让模型模仿 |
| Zero-shot | 零样本学习 | 不给例子，直接问 |
| CoT | Chain of Thought | 思维链，让模型"一步步思考" |
| Prompt Engineering | 提示工程 | 设计prompt的技巧 |
| Hallucination | 幻觉 | 模型一本正经胡说八道 |

## 1.2 Agent开发术语

| 术语 | 英文 | 解释 |
|------|------|------|
| Agent | 智能体 | 能自主决策、调用工具的LLM应用 |
| Tool / Function | 工具/函数 | Agent可以调用的外部能力（搜索、计算、API） |
| Function Calling | 函数调用 | 模型输出结构化函数调用请求 |
| Tool Calling | 工具调用 | Function Calling的LLM统一叫法 |
| ReAct | 推理+行动 | Reasoning + Acting，先思考再行动的Agent模式 |
| Planning | 规划 | Agent拆解复杂任务的能力 |
| Memory | 记忆 | Agent的短期/长期记忆存储 |
| Sub-agent | 子智能体 | 被主Agent调用的专门Agent |
| Handoff | 转交 | Agent之间传递对话的控制权 |
| MRKL | MRKL | 模块化推理+知识+语言，Agent早期架构思想 |

## 1.3 RAG相关术语

| 术语 | 英文 | 解释 |
|------|------|------|
| RAG | 检索增强生成 | Retrieval-Augmented Generation |
| Embedding | 向量化 | 把文本转成高维向量表示 |
| Vector Store | 向量数据库 | 存储和检索向量的数据库 |
| Chunk | 文本块 | 切分后的文档片段 |
| Chunking Strategy | 切分策略 | 如何切割文档（按字数、段落、语义） |
| Retrieval | 检索 | 从向量库找相关内容 |
| Reranking | 重排序 | 对检索结果二次排序提高准确率 |
| Hybrid Search | 混合检索 | 向量检索 + 关键词检索结合 |
| Knowledge Base | 知识库 | RAG的数据源 |

## 1.4 工程术语

| 术语 | 解释 |
|------|------|
| Fine-tuning | 微调，用自己的数据训练模型 |
| RLHF | 人类反馈强化学习，GPT的训练方法 |
| DPO | 直接偏好优化，更高效的微调方法 |
| SSE | Server-Sent Events，流式响应技术 |
| Rate Limiting | 限流，控制API调用频率 |

---

# 二、LangChain Agent开发

## 2.1 核心架构

### Chain vs Agent vs Tool

```
┌─────────────────────────────────────────────────────┐
│  CHAIN (链)                                         │
│  - 固定流程，预先定义的步骤序列                       │
│  - 适合：确定性的任务，流程固定                       │
│  - 例：LLMChain, RetrievalQA                        │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│  AGENT (智能体)                                     │
│  - 动态决策，根据输入自主选择工具和步骤               │
│  - 适合：需要判断力的任务，流程不确定                 │
│  - 例：ReActAgent, OpenAIFunctionsAgent             │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│  TOOL (工具)                                        │
│  - 单一功能封装，Agent可调用的原子能力               │
│  - 例：搜索、计算器、数据库查询                      │
└─────────────────────────────────────────────────────┘
```

## 2.2 LLM初始化

### OpenAI模型

```python
# 方法1: 使用init_chat_model (推荐 - 统一接口)
from langchain.chat_models import init_chat_model
import os

os.environ["OPENAI_API_KEY"] = "sk-..."

model = init_chat_model(
    model="gpt-4",
    temperature=0.5,
    timeout=10,
    max_tokens=1000
)

# 方法2: 直接实例化
from langchain_openai import ChatOpenAI

model = ChatOpenAI(
    model="gpt-4",
    temperature=0.1,
    max_tokens=1000,
    timeout=30
)
```

### Anthropic模型

```python
from langchain.chat_models import init_chat_model
import os

os.environ["ANTHROPIC_API_KEY"] = "sk-..."

model = init_chat_model("claude-sonnet-4-5-20250929")

# 或直接实例化
from langchain_anthropic import ChatAnthropic

model = ChatAnthropic(
    model="claude-sonnet-4-5-20250929",
    temperature=0.7,
    max_tokens=1000
)
```

### 本地模型 (Ollama)

```python
from langchain_community.chat_models import ChatOllama

model = ChatOllama(
    model="llama2",
    base_url="http://localhost:11434",
    temperature=0.7
)
```

## 2.3 PromptTemplate和ChatPromptTemplate

### PromptTemplate (纯文本模板)

```python
from langchain.prompts import PromptTemplate

# 基础用法
prompt = PromptTemplate.from_template(
    "请用{language}写一个关于{topic}的简介"
)

formatted_prompt = prompt.format(language="中文", topic="人工智能")
# 输出: "请用中文写一个关于人工智能的简介"
```

### ChatPromptTemplate (对话模板)

```python
from langchain.prompts import ChatPromptTemplate, MessagesPlaceholder

# 方法1: 从模板字符串创建
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个{role}助手。"),
    ("human", "{user_input}"),
])

# 方法2: MessagesPlaceholder (用于Memory等动态内容)
chat_prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个有用的助手。"),
    MessagesPlaceholder(variable_name="chat_history", optional=True),
    ("human", "{input}"),
])
```

## 2.4 Tool开发完整指南

### @tool装饰器详解

```python
from langchain.tools import tool

@tool
def search_database(query: str, limit: int = 10) -> str:
    """搜索客户数据库，返回匹配查询的记录。

    Args:
        query: 要搜索的关键词
        limit: 返回结果的最大数量，默认10条
    """
    # 实际业务逻辑
    return f"Found {limit} results for '{query}'"

# 获取工具信息
print(search_database.name)        # search_database
print(search_database.description) # 函数的docstring
```

### StructuredTool用法

```python
from langchain.tools import StructuredTool
from pydantic import BaseModel, Field

class SearchInput(BaseModel):
    query: str = Field(description="搜索关键词")
    limit: int = Field(default=10, description="结果数量限制")

def search_func(query: str, limit: int = 10) -> str:
    """搜索功能的实现"""
    return f"Found {limit} results for {query}"

search_tool = StructuredTool(
    name="search",
    description="Search for information in the database",
    args_schema=SearchInput,
    func=search_func,
)
```

### 异步工具实现

```python
import asyncio
from langchain.tools import tool

@tool
async def async_search(query: str) -> str:
    """异步搜索工具"""
    await asyncio.sleep(1)
    return f"Async results for: {query}"

@tool  
async def fetch_url(url: str) -> str:
    """异步获取URL内容"""
    import aiohttp
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.text()
```

### 错误处理最佳实践

```python
from langchain.tools import tool
from typing import Optional

@tool
def safe_division(numerator: float, denominator: float) -> str:
    """安全地执行除法运算。
    
    Args:
        numerator: 被除数
        denominator: 除数，不能为零
    """
    try:
        if denominator == 0:
            return "错误: 除数不能为零"
        result = numerator / denominator
        return f"结果: {result}"
    except Exception as e:
        return f"计算错误: {str(e)}"
```

## 2.5 Agent类型与选择

### create_agent (推荐 - 现代API)

```python
from langchain.agents import create_agent
from langchain.tools import tool
from langchain.chat_models import init_chat_model

# 定义工具
@tool
def search(query: str) -> str:
    """Search for information."""
    return f"Results for: {query}"

@tool
def get_weather(location: str) -> str:
    """Get weather for a location."""
    return f"Weather in {location}: Sunny, 72°F"

# 创建Agent (最简方式)
agent = create_agent(
    model="openai:gpt-4o",
    tools=[search, get_weather],
)

# 执行
result = agent.invoke({
    "messages": [{"role": "user", "content": "What's the weather in Tokyo?"}]
})
```

### Agent类型选择指南

| Agent类型 | 特点 | 适用场景 |
|-----------|------|----------|
| `create_agent` | 现代统一API，推荐使用 | 所有新项目 |
| `create_react_agent` | ReAct推理模式 | 复杂推理任务 |
| `create_openai_functions_agent` | OpenAI原生函数调用 | OpenAI模型 |
| `create_tool_calling_agent` | 通用工具调用 | 跨模型提供商 |
| `create_structured_chat_agent` | 强制结构化 | 严格格式要求 |

## 2.6 AgentExecutor配置

```python
from langchain.agents import AgentExecutor

executor = AgentExecutor.from_agent_and_tools(
    agent=agent,
    tools=tools,
    max_iterations=10,           # 最多执行10次工具调用
    max_execution_time=60.0,     # 最大执行时间(秒)
    handle_parsing_errors=True,  # 将错误发回给LLM重试
    return_intermediate_steps=True,  # 返回中间步骤
    verbose=True
)

result = executor.invoke({"input": "你的问题"})
```

## 2.7 结构化输出

```python
from pydantic import BaseModel, Field
from langchain.agents import create_agent
from langchain.agents.structured_output import ToolStrategy

class ContactInfo(BaseModel):
    """联系信息结构"""
    name: str = Field(description="姓名")
    email: str = Field(description="邮箱")
    phone: str = Field(description="电话")

agent = create_agent(
    model="gpt-4",
    tools=[],
    response_format=ToolStrategy(ContactInfo)
)

result = agent.invoke({
    "messages": [{"role": "user", "content": "提取: 张三, zhangsan@example.com, 13800138000"}]
})
# 返回: ContactInfo(name='张三', email='zhangsan@example.com', phone='13800138000')
```

## 2.8 Memory记忆组件

### ConversationBufferMemory

```python
from langchain.memory import ConversationBufferMemory
from langchain.chains import ConversationChain

memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True,
    ai_prefix="AI",
    human_prefix="用户"
)

conversation = ConversationChain(
    llm=llm,
    memory=memory,
    verbose=True
)

response = conversation.predict(input="我叫张三")
response = conversation.predict(input="我刚才说什么名字了?")
```

### ConversationBufferWindowMemory

```python
from langchain.memory import ConversationBufferWindowMemory

# 只保留最后k轮对话
memory = ConversationBufferWindowMemory(
    memory_key="chat_history",
    k=5,  # 只保留最近5条消息
    return_messages=True
)
```

---

# 三、LlamaIndex RAG实现

## 3.1 RAG核心流程

```
文档 → 切分 → 向量化 → 存入向量库
                        ↓
用户问题 → 向量化 → 相似度检索 → 取回Top-K chunks
                                    ↓
                    问题 + 检索结果 → LLM → 回答
```

## 3.2 基础RAG实现

```python
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader
from llama_index.embeddings.openai import OpenAIEmbedding

# 1. 加载文档
documents = SimpleDirectoryReader("./data").load_data()

# 2. 创建索引（自动切分 + 向量化）
index = VectorStoreIndex.from_documents(
    documents,
    embed_model=OpenAIEmbedding()
)

# 3. 查询
query_engine = index.as_query_engine()
response = query_engine.query("文档里说了什么？")
print(response)
```

## 3.3 文档切分策略

### SentenceSplitter

```python
from llama_index.core.node_parser import SentenceSplitter

splitter = SentenceSplitter(
    chunk_size=1024,      # 每块最大token数
    chunk_overlap=200,    # 重叠token数
)

nodes = splitter.get_nodes_from_documents(documents)
```

### SemanticSplitter (语义切分)

```python
from llama_index.core.node_parser import SemanticSplitterNodeParser
from llama_index.embeddings.openai import OpenAIEmbedding

semantic_splitter = SemanticSplitterNodeParser(
    buffer_size=1,
    breakpoint_percentile_threshold=95,
    embed_model=OpenAIEmbedding()
)
```

### 切分策略选择

| 策略 | 适用场景 | 优点 |
|------|----------|------|
| 固定大小 | 通用 | 简单可靠 |
| 递归切分 | 代码/Markdown | 保持结构 |
| 语义切分 | 需要语义完整 | 含义完整 |

## 3.4 向量存储集成

### Chroma (本地)

```python
from llama_index.vector_stores.chroma import ChromaVectorStore
from llama_index.core import StorageContext
import chromadb

db = chromadb.PersistentClient(path="./chroma_db")
chroma_collection = db.get_or_create_collection("my_collection")
vector_store = ChromaVectorStore(chroma_collection=chroma_collection)

storage_context = StorageContext.from_defaults(vector_store=vector_store)
index = VectorStoreIndex.from_documents(
    documents,
    storage_context=storage_context
)
```

### Milvus

```python
from llama_index.vector_stores.milvus import MilvusVectorStore
from llama_index.core import StorageContext

vector_store = MilvusVectorStore(
    uri="http://localhost:19530",
    collection_name="my_collection"
)

storage_context = StorageContext.from_defaults(vector_store=vector_store)
index = VectorStoreIndex.from_documents(
    documents,
    storage_context=storage_context
)
```

## 3.5 检索策略

### 相似度检索

```python
retriever = index.as_retriever(
    similarity_top_k=5  # 返回最相似的5个节点
)
```

### Hybrid Search (混合检索)

```python
from llama_index.core.vector_stores.types import VectorStoreQueryMode

hybrid_retriever = index.as_retriever(
    vector_store_query_mode=VectorStoreQueryMode.SEMANTIC_HYBRID,
    similarity_top_k=10
)
```

### Reranking (重排序)

```python
from llama_index.postprocessor.cohere_rerank import CohereRerank

cohere_rerank = CohereRerank(
    api_key="YOUR_COHERE_API_KEY",
    top_n=5
)

query_engine = index.as_query_engine(
    similarity_top_k=10,
    node_postprocessors=[cohere_rerank]
)
```

## 3.6 高级优化技术

### HyDE (假设文档嵌入)

```python
from llama_index.core.indices.query.query_transform import HyDEQueryTransform

hyde = HyDEQueryTransform(
    llm=model,
    include_original=False
)

hyde_query_engine = TransformQueryEngine(
    base_query_engine,
    hyde
)
```

### Query Transformation

```python
from llama_index.core.indices.query.query_transform import StepDecomposeQueryTransform

step_decompose = StepDecomposeQueryTransform(
    llm=model,
    verbose=True
)
```

---

# 四、向量数据库实战

## 4.1 数据库选型对比

| 特性 | Milvus | Weaviate | Pinecone | Qdrant |
|------|--------|-----------|----------|--------|
| **类型** | 开源 | 开源 | 托管 | 开源 |
| **延迟 P50** | 6ms | 10ms | 8ms | 4ms |
| **索引类型** | 8+种 | HNSW/Flat | S1 | HNSW |
| **GPU 支持** | ✅ | ❌ | ✅ | ✅ |
| **分布式** | ✅ | ✅ | ✅ | ✅ |

## 4.2 Milvus完整指南

### Docker部署

```bash
# 使用Docker Compose
curl -sfL https://raw.githubusercontent.com/milvus-io/milvus/master/scripts/standalone_embed.sh -o standalone.sh
chmod +x standalone.sh
./standalone.sh start
```

### Python连接与操作

```python
from pymilvus import MilvusClient, DataType

client = MilvusClient(uri="http://localhost:19530")

# 创建Collection
schema = client.create_schema(auto_id=False, enable_dynamic_field=True)

schema.add_field(field_name="id", datatype=DataType.INT64, is_primary=True)
schema.add_field(field_name="vector", datatype=DataType.FLOAT_VECTOR, dim=768)
schema.add_field(field_name="text", datatype=DataType.VARCHAR, max_length=65535)

# 创建索引
index_params = client.prepare_index_params()
index_params.add_index(
    field_name="vector",
    index_type="AUTOINDEX",
    metric_type="COSINE"
)

client.create_collection(
    collection_name="my_collection",
    schema=schema,
    index_params=index_params
)

# 插入数据
data = [{"id": 1, "vector": [0.1]*768, "text": "文档内容"}]
client.insert("my_collection", data)

# 搜索
results = client.search(
    collection_name="my_collection",
    data=[query_vector],
    limit=10,
    filter="category == 'tech'",
    output_fields=["id", "text"]
)
```

### 索引类型选择

| 索引类型 | 查询速度 | 召回率 | 内存占用 | 适用场景 |
|---------|---------|--------|---------|---------|
| **FLAT** | 慢 | 100% | 高 | <10万，精确结果 |
| **IVF_FLAT** | 中 | 高 | 中 | 中等规模 |
| **IVF_PQ** | 很快 | 中 | 很低 | 大规模，内存受限 |
| **HNSW** | 很快 | 高 | 高 | 低延迟，高召回 |

## 4.3 Weaviate指南

### Docker部署

```yaml
# docker-compose.yml
version: '3.8'
services:
  weaviate:
    image: cr.weaviate.io/semitechnologies/weaviate:1.35.9
    ports:
      - "8080:8080"
    environment:
      AUTHENTICATION_ANONYMOUS_ACCESS_ENABLED: "true"
```

### Python操作

```python
import weaviate
from weaviate.classes.config import Configure, Property, DataType

client = weaviate.connect_to_local()

# 创建Collection
questions = client.collections.create(
    name="Question",
    vectorizer=Configure.Vectors.text2vec_openai(),
    properties=[
        Property(name="question", data_type=DataType.TEXT),
        Property(name="answer", data_type=DataType.TEXT),
    ],
)

# 插入数据
questions.data.insert(
    properties={
        "question": "什么是向量数据库?",
        "answer": "向量数据库是专门用于存储和检索向量数据的数据库。",
    }
)

# 向量搜索
results = questions.query.near_text(
    query="向量数据库是什么?",
    limit=10
)
```

---

# 五、FastAPI AI后端

## 5.1 项目结构

```
ai-backend/
├── app/
│   ├── __init__.py
│   ├── main.py                 # 应用入口
│   ├── config.py               # 配置管理
│   ├── dependencies.py         # 依赖注入
│   ├── routers/                # 路由层
│   │   ├── chat.py             # 聊天API
│   │   └── auth.py             # 认证API
│   ├── services/               # 业务逻辑
│   │   └── llm_service.py
│   └── schemas/                # Pydantic模型
├── tests/
├── Dockerfile
└── docker-compose.yml
```

## 5.2 Pydantic模型设计

```python
from pydantic import BaseModel, Field, field_validator
from typing import Optional, List
from enum import Enum

class MessageRole(str, Enum):
    SYSTEM = "system"
    USER = "user"
    ASSISTANT = "assistant"

class ChatMessage(BaseModel):
    role: MessageRole
    content: str

class ChatRequest(BaseModel):
    messages: List[ChatMessage] = Field(..., min_length=1)
    model: str = Field(default="gpt-4")
    temperature: float = Field(default=0.7, ge=0.0, le=2.0)
    max_tokens: Optional[int] = Field(default=2048)
    stream: bool = Field(default=False)
    
    @field_validator("messages")
    @classmethod
    def validate_messages(cls, v):
        if v[-1].role != MessageRole.USER:
            raise ValueError("最后一条消息必须是用户消息")
        return v
```

## 5.3 LLM集成模式

### 同步调用

```python
from openai import OpenAI

client = OpenAI(api_key=settings.OPENAI_API_KEY)

def chat_sync(request: ChatRequest):
    response = client.chat.completions.create(
        model=request.model,
        messages=[msg.model_dump() for msg in request.messages],
        temperature=request.temperature,
        max_tokens=request.max_tokens,
    )
    return response
```

### 异步调用

```python
from openai import AsyncOpenAI

async_client = AsyncOpenAI(api_key=settings.OPENAI_API_KEY)

async def chat_async(request: ChatRequest):
    response = await async_client.chat.completions.create(
        model=request.model,
        messages=[msg.model_dump() for msg in request.messages],
        temperature=request.temperature,
        max_tokens=request.max_tokens,
    )
    return response
```

## 5.4 SSE流式响应

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
from openai import AsyncOpenAI

app = FastAPI()
client = AsyncOpenAI()

@app.post("/chat/stream")
async def chat_stream(request: ChatRequest):
    async def generate():
        stream = await client.chat.completions.create(
            model=request.model,
            messages=[msg.model_dump() for msg in request.messages],
            stream=True
        )
        async for chunk in stream:
            if chunk.choices[0].delta.content:
                yield f"data: {chunk.choices[0].delta.content}\n\n"
        yield "data: [DONE]\n\n"
    
    return StreamingResponse(
        generate(),
        media_type="text/event-stream",
        headers={
            "Cache-Control": "no-cache",
            "Connection": "keep-alive",
        }
    )
```

### 前端对接

```javascript
const eventSource = new EventSource('/api/v1/chat/stream');

eventSource.onmessage = (event) => {
  const data = event.data;
  if (data === '[DONE]') {
    eventSource.close();
    return;
  }
  outputDiv.textContent += data;
};

eventSource.onerror = (error) => {
  console.error('SSE Error:', error);
  eventSource.close();
};
```

## 5.5 中间件配置

### CORS

```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### JWT认证

```python
from fastapi.security import OAuth2PasswordBearer
from jose import JWTError, jwt

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

async def get_current_user(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, settings.SECRET_KEY, algorithms=[settings.ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise HTTPException(status_code=401)
    except JWTError:
        raise HTTPException(status_code=401)
    return username
```

## 5.6 异步任务处理

### BackgroundTasks

```python
from fastapi import BackgroundTasks

def send_email(email: str, content: str):
    # 发送邮件逻辑
    pass

@app.post("/process")
async def process_data(
    background_tasks: BackgroundTasks,
    data: dict
):
    background_tasks.add_task(send_email, "user@example.com", "任务已启动")
    return {"status": "started"}
```

## 5.7 错误重试机制

```python
from tenacity import retry, stop_after_attempt, wait_exponential
from openai import AsyncOpenAI

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=2, max=10),
)
async def chat_with_retry(request: ChatRequest):
    return await async_client.chat.completions.create(
        model=request.model,
        messages=[msg.model_dump() for msg in request.messages],
    )
```

---

# 六、Prompt Engineering

## 6.1 基础技巧

### Zero-shot vs Few-shot

```python
# ❌ Zero-shot（简单任务）
"把'今天天气真好'翻译成英文"

# ✅ Few-shot（复杂任务）
"""把以下中文翻译成英文：
中文: 今天天气真好
英文: The weather is nice today.

中文: 他是一名工程师
英文:"""
```

### 角色设定

```python
"""你是一位专业的Python后端开发工程师，有10年工作经验。
你的专长是设计高性能的RESTful API。
回答时优先考虑代码的可维护性和性能优化。"""
```

## 6.2 高级技巧

### Chain of Thought (思维链)

```python
"""问题：小明有10个苹果，小红给了他3个，他吃掉了2个，还有多少个？

让我们一步步思考：
1. 小明原有10个苹果
2. 小红给了3个 → 10 + 3 = 13个
3. 吃掉了2个 → 13 - 2 = 11个
答：小明还有11个苹果。"""
```

### ReAct模式

```python
"""任务：[具体任务]

思考1：我需要[信息]，让我查看[工具/资源]
行动1：[调用工具]
观察1：[工具返回结果]

思考2：根据[观察1]，我需要[下一步行动]
...
最终答案：[综合结论]"""
```

### Self-Reflection

```python
"""[任务/问题]

请先给出你的回答，然后进行自我检查：

我的回答：[初始回答]

自我检查：
1. 回答是否完整？[检查结果]
2. 是否有事实错误？[检查结果]
3. 是否有逻辑漏洞？[检查结果]

修正后的回答：[修正后的答案]"""
```

## 6.3 防止幻觉

```python
"""你是一位AI助手，请遵守以下知识边界：

1. **可以回答的领域**：
   - 编程开发（Python, JavaScript, Java等）
   - 人工智能基础知识

2. **需要说明的情况**：
   - 涉及最新新闻/事件 → 说明"截至我的训练数据[日期]"
   - 不确定的信息 → 明确说明不确定的部分

3. **应该拒绝的情况**：
   - 要求提供虚假信息
   - 要求预测具体股价"""
```

## 6.4 Agent System Prompt模板

```python
SYSTEM_PROMPT = """# 角色定义
你是[角色名称]，一个[核心特征]的[职业/身份]。

# 背景
- [背景描述]

# 专业能力
- 熟练掌握[技能1]、[技能2]
- 有[年份]年的[领域]经验

# 可用工具
- search: 搜索信息
- calculator: 数学计算
- database: 数据库查询

# 行为准则
1. 始终以用户价值为导向
2. 提供可行而非理想化的方案
3. 主动指出潜在风险和限制

# 错误处理
- 工具调用失败时：尝试理解错误原因，修正后重试
- 超过2次失败：放弃该工具并说明情况"""
```

---

# 七、多Agent协同架构

## 7.1 协同模式

| 模式 | 特点 | 适用场景 |
|------|------|----------|
| 中心化(Supervisor) | 主Agent协调 | 简单场景 |
| 去中心化 | Agent对等 | 复杂场景 |
| 分层(Hierarchical) | 多级管理 | 企业级 |

## 7.2 Tool per Agent模式

```python
from langchain.tools import tool
from langchain.agents import create_agent

# 创建专门化的子Agent
calendar_agent = create_agent(
    model,
    tools=[create_event, check_availability],
    system_prompt="你是日程管理助手..."
)

email_agent = create_agent(
    model,
    tools=[send_email, read_inbox],
    system_prompt="你是邮件助手..."
)

# 包装成工具
@tool
def schedule_event(request: str) -> str:
    """安排日程，处理自然语言请求"""
    result = calendar_agent.invoke({
        "messages": [{"role": "user", "content": request}]
    })
    return result["messages"][-1].content

@tool
def manage_email(request: str) -> str:
    """处理邮件相关请求"""
    result = email_agent.invoke({
        "messages": [{"role": "user", "content": request}]
    })
    return result["messages"][-1].content

# 主智能体协调
supervisor = create_agent(
    model,
    tools=[schedule_event, manage_email],
    system_prompt="你是总调度，根据用户需求分配给日程或邮件助手..."
)
```

## 7.3 Handoff机制

```python
from langchain.tools import tool
from langgraph.types import Command

@tool
def transfer_to_sales() -> Command:
    """转交给销售Agent"""
    return Command(
        goto="sales_agent",
        update={"active_agent": "sales_agent"}
    )

@tool
def transfer_to_support() -> Command:
    """转交给客服Agent"""
    return Command(
        goto="support_agent",
        update={"active_agent": "support_agent"}
    )

sales_agent = create_agent(
    model,
    tools=[transfer_to_support],
    system_prompt="销售Agent。遇到技术问题转给客服。"
)

support_agent = create_agent(
    model,
    tools=[transfer_to_sales],
    system_prompt="客服Agent。遇到价格问题转给销售。"
)
```

---

# 八、LLM API调用与错误处理

## 8.1 OpenAI API

```python
from openai import OpenAI, AsyncOpenAI
import httpx

# 同步
client = OpenAI(api_key="sk-...")

response = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Hello"}],
    temperature=0.7,
    max_tokens=1000,
)

# 异步
async_client = AsyncOpenAI(
    api_key="sk-...",
    timeout=httpx.Timeout(60.0, connect=10.0)
)

response = await async_client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Hello"}],
    stream=True
)

async for chunk in response:
    print(chunk.choices[0].delta.content)
```

## 8.2 Anthropic Claude API

```python
from anthropic import Anthropic

client = Anthropic(api_key="sk-...")

response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Hello"}],
)
```

## 8.3 错误处理最佳实践

```python
from tenacity import retry, stop_after_attempt, wait_exponential
import httpx

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=2, max=10),
)
async def call_llm_with_retry(messages):
    try:
        response = await async_client.chat.completions.create(
            model="gpt-4",
            messages=messages,
        )
        return response
    except httpx.TimeoutException:
        print("请求超时，正在重试...")
        raise
    except httpx.ConnectError:
        print("连接失败，正在重试...")
        raise
    except Exception as e:
        print(f"未知错误: {e}")
        raise
```

---

# 九、RAG优化与评估

## 9.1 检索优化

### Chunking优化

| 参数 | 建议 | 说明 |
|------|------|------|
| Chunk大小 | 512-1024 tokens | 太小丢失上下文，太大噪声多 |
| 重叠 | 10-20% | 防止关键信息被切断 |

### Hybrid Search

```python
# 向量检索 + BM25关键词检索
retriever = index.as_retriever(
    vector_store_query_mode="SEMANTIC_HYBRID",
    alpha=0.5,  # 0=纯关键词, 1=纯向量
    similarity_top_k=10
)
```

### Reranking

```python
from llama_index.postprocessor.cohere_rerank import CohereRerank

reranker = CohereRerank(top_n=5)
query_engine = index.as_query_engine(
    similarity_top_k=20,
    node_postprocessors=[reranker]
)
```

## 9.2 评估指标

### 检索评估

| 指标 | 说明 |
|------|------|
| Recall@K | 前K个结果中相关文档的比例 |
| MRR | 第一个相关结果的排名倒数 |
| NDCG | 考虑排名的归一化折扣累积增益 |

### 生成评估

| 指标 | 说明 |
|------|------|
| Faithfulness | 答案是否基于检索内容 |
| Relevance | 答案是否回答了问题 |
| Hallucination | 是否包含检索内容外的虚假信息 |

---

# 十、常见问题排查

## 10.1 Agent陷入循环

**问题表现**: Agent反复调用同一个工具

**解决方案**:
1. 设置 `max_iterations` 限制
2. 在prompt中明确"不要重复调用同一工具超过3次"
3. 使用Memory记录已尝试的路径

```python
executor = AgentExecutor.from_agent_and_tools(
    agent=agent,
    tools=tools,
    max_iterations=5,
    early_stopping_method="force"
)
```

## 10.2 RAG检索不准

**解决方案**:
1. 检查Chunk大小（建议512-1024 token）
2. 尝试Hybrid Search（向量+BM25）
3. 添加Reranking重排序
4. 尝试HyDE（假设文档嵌入）

## 10.3 Token超限

**解决方案**:
1. 使用ConversationSummaryMemory压缩历史
2. 设置 `trim_intermediate_steps`
3. 工具输出限制长度

```python
executor = AgentExecutor.from_agent_and_tools(
    agent=agent,
    tools=tools,
    trim_intermediate_steps=lambda steps: steps[-100:]
)
```

## 10.4 LLM调用失败

**解决方案**:
1. 添加重试逻辑（指数退避）
2. 设置合理超时时间
3. 准备降级方案（备用模型）

---

# 十一、实习第一天检查清单

## 环境准备

- [ ] Python 3.10+ 环境
- [ ] 安装核心库：`langchain`, `llama-index`, `pymilvus`, `fastapi`
- [ ] 获取 OpenAI/Anthropic API Key
- [ ] Docker环境（向量数据库）
- [ ] Git配置完成

## 第一件事问带教老师

1. **用的是 LangChain 还是 LlamaIndex？**
2. **向量库用的哪个？**（Milvus/Weaviate/其他）
3. **有没有现有代码可以参考？**
4. **代码规范和提交要求是什么？**
5. **团队的技术栈和工具链是什么？**

## 快速上手建议

1. **先读代码**：熟悉现有项目的结构和风格
2. **从简单任务开始**：修bug、写测试、加日志
3. **多问多记**：不懂就问，记好笔记
4. **主动沟通**：定期和带教老师同步进度

---

**祝实习顺利！**

*如有问题，可参考本手册对应章节或查阅官方文档。*
