# LlamaIndex RAG 生产级实战手册

本手册为 AI 智能体开发实习生提供完整的 LlamaIndex RAG 技术落地指南，涵盖从数据加载到生产部署的完整流程。每个章节都包含详细的代码示例和配置说明。

## 1 数据加载（Reader）

数据加载是 RAG 流水线的第一步。LlamaIndex 提供丰富的数据连接器支持各种数据源。

### 1.1 SimpleDirectoryReader 基础

```python
from llama_index.core import SimpleDirectoryReader

reader = SimpleDirectoryReader(input_dir="./data")
documents = reader.load_data()
print(f"共加载 {len(documents)} 个文档")
```

### 1.2 高级配置

```python
reader = SimpleDirectoryReader(
    input_dir="./documents",
    recursive=True,
    exclude=["*.tmp", "*.log"],
    required_exts=[".pdf", ".md", ".txt"],
    num_files_limit=100,
    encoding="utf-8"
)
documents = reader.load_data()
```

### 1.3 元数据提取

```python
import os
from datetime import datetime

def custom_file_metadata(file_path):
    stat = os.stat(file_path)
    return {
        "file_name": os.path.basename(file_path),
        "file_size": stat.st_size,
        "modified_time": datetime.fromtimestamp(stat.st_mtime).isoformat()
    }

reader = SimpleDirectoryReader(
    input_dir="./data",
    file_metadata=custom_file_metadata
)
documents = reader.load_data()
```

### 1.4 自定义 Reader

```python
from typing import List, Optional
import requests
from llama_index.core.readers.base import BaseReader
from llama_index.core.schema import Document

class APIDocumentReader(BaseReader):
    def __init__(self, api_url: str, api_key: Optional[str] = None):
        self.api_url = api_url
        self.api_key = api_key
    
    def load_data(self, endpoint: str = "") -> List[Document]:
        headers = {}
        if self.api_key:
            headers["Authorization"] = f"Bearer {self.api_key}"
        
        response = requests.get(f"{self.api_url}/{endpoint}", headers=headers)
        data = response.json()
        
        documents = []
        for item in data.get("documents", []):
            doc = Document(
                text=item["content"],
                metadata={"source": "api", "document_id": item.get("id")}
            )
            documents.append(doc)
        
        return documents
```

### 1.5 LlamaHub 数据连接器

```python
from llama_index.core import download_loader

GoogleDocsReader = download_loader("GoogleDocsReader")
loader = GoogleDocsReader(tree_level=2)
documents = loader.load_data(document_ids=["your-document-id"])
```

## 2 文档处理

### 2.1 Node 解析器基础

```python
from llama_index.core.node_parser import SentenceSplitter

parser = SentenceSplitter(
    chunk_size=512,
    chunk_overlap=50
)
nodes = parser.get_nodes_from_documents(documents)
```

### 2.2 TokenTextSplitter

```python
from llama_index.core.node_parser import TokenTextSplitter

token_splitter = TokenTextSplitter(
    chunk_size=512,
    chunk_overlap=50,
    separator=" "
)
```

### 2.3 SemanticSplitter

```python
from llama_index.core.node_parser import SemanticSplitterNodeParser
from llama_index.embeddings.openai import OpenAIEmbedding

semantic_splitter = SemanticSplitterNodeParser(
    embed_model=OpenAIEmbedding(),
    breakpoint_percentile_threshold=95
)
```

### 2.4 元数据提取

```python
from llama_index.core.extractors import TitleExtractor, QuestionsAnsweredExtractor
from llama_index.core.ingestion import IngestionPipeline

extractors = [
    TitleExtractor(),
    QuestionsAnsweredExtractor(questions=5)
]

pipeline = IngestionPipeline(
    transformations=[
        SentenceSplitter(chunk_size=512),
        *extractors
    ]
)

nodes = pipeline.run(documents=documents)
```

## 3 索引构建

### 3.1 VectorStoreIndex

```python
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader
from llama_index.embeddings.openai import OpenAIEmbedding

documents = SimpleDirectoryReader("./data").load_data()
index = VectorStoreIndex.from_documents(
    documents,
    embed_model=OpenAIEmbedding(model="text-embedding-3-small")
)
```

### 3.2 SummaryIndex

```python
from llama_index.core import SummaryIndex

summary_index = SummaryIndex(nodes)
query_engine = summary_index.as_query_engine()
```

### 3.3 PropertyGraphIndex

```python
from llama_index.core.indices import PropertyGraphIndex
from llama_index.graph_stores.neo4j import Neo4jPropertyGraphStore

graph_store = Neo4jPropertyGraphStore(
    username="neo4j",
    password="password",
    url="bolt://localhost:7687"
)

index = PropertyGraphIndex.from_documents(
    documents,
    property_graph_store=graph_store
)
```

## 4 向量存储集成

### 4.1 Chroma

```python
import chromadb
from llama_index.vector_stores.chroma import ChromaVectorStore

chroma_client = chromadb.PersistentClient(path="./chroma_db")
collection = chroma_client.get_or_create_collection("my_collection")
vector_store = ChromaVectorStore(chroma_collection=collection)
index = VectorStoreIndex.from_documents(documents, vector_store=vector_store)
```

### 4.2 Qdrant

```python
from qdrant_client import QdrantClient
from llama_index.vector_stores.qdrant import QdrantVectorStore

qdrant_client = QdrantClient(host="localhost", port=6333)
vector_store = QdrantVectorStore(
    client=qdrant_client,
    collection_name="my_collection",
    dimension=1536
)
```

### 4.3 Milvus

```python
from pymilvus import connections
from llama_index.vector_stores.milvus import MilvusVectorStore

connections.connect(host="localhost", port="19530")
vector_store = MilvusVectorStore(
    collection_name="my_collection",
    dimension=1536
)
```

### 4.4 Pinecone

```python
import pinecone
from llama_index.vector_stores.pinecone import PineconeVectorStore

pinecone.init(api_key="api_key", environment="us-west1-gcp")
pinecone.create_index("my_index", dimension=1536)
index_obj = pinecone.Index("my_index")
vector_store = PineconeVectorStore(pinecone_index=index_obj)
```

## 5 检索策略

### 5.1 相似度检索

```python
query_engine = index.as_query_engine(similarity_top_k=5)
response = query_engine.query("你的问题")
```

### 5.2 MMR

```python
query_engine = index.as_query_engine(
    vector_store_query_mode="mmr",
    vector_store_kwargs={"mmr_threshold": 0.3},
    similarity_top_k=10
)
```

### 5.3 混合检索

```python
query_engine = index.as_query_engine(
    vector_store_query_mode="hybrid"
)
```

### 5.4 重排序

```python
from llama_index.postprocessor.cohere_rerank import CohereRerank

cohere_rerank = CohereRerank(
    api_key="api_key",
    top_n=3
)

query_engine = index.as_query_engine(
    similarity_top_k=10,
    node_postprocessors=[cohere_rerank]
)
```

### 5.5 元数据过滤

```python
from llama_index.core.vector_stores import MetadataFilter, MetadataFilters, FilterOperator

filters = MetadataFilters(
    filters=[
        MetadataFilter(
            key="category",
            operator=FilterOperator.EQ,
            value="编程"
        )
    ]
)
query_engine = index.as_query_engine(filters=filters)
```

## 6 查询引擎

### 6.1 基础配置

```python
query_engine = index.as_query_engine(
    similarity_top_k=3,
    streaming=True,
    response_mode="compact"
)
```

### 6.2 流式响应

```python
query_engine = index.as_query_engine(streaming=True)
streaming_response = query_engine.query("详细解释RAG")
streaming_response.print_response_stream()
```

### 6.3 多轮对话

```python
from llama_index.core.chat_engine import CondenseQuestionChatEngine

chat_engine = CondenseQuestionChatEngine.from_defaults(
    query_engine=index.as_query_engine()
)

response = chat_engine.chat("你好")
response = chat_engine.chat("它解决了什么问题")
```

### 6.4 引用来源

```python
response = query_engine.query("问题")
print(response.response)
for i, source in enumerate(response.source_nodes):
    print(f"来源 {i+1}: {source.node.text[:200]}")
```

## 7 高级优化

### 7.1 HyDE

```python
from llama_index.core.indices.query.query_transform.base import HyDEQueryTransform
from llama_index.core.query_engine import TransformQueryEngine

hyde = HyDEQueryTransform(include_original=True)
query_engine = TransformQueryEngine(
    index.as_query_engine(),
    query_transform=hyde
)
```

### 7.2 Parent Document Retrieval

```python
from llama_index.core.retrievers import ParentRetriever

parent_retriever = ParentRetriever(
    index.as_retriever(),
    parent_documents=parent_nodes,
    child_nodes=child_nodes
)
```

## 8 生产部署

### 8.1 索引持久化

```python
from llama_index.core import StorageConte
