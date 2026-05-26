# RAG 本地知识库问答系统

这是一个基于 RAG（Retrieval-Augmented Generation，检索增强生成）的本地知识库问答项目。项目将服装尺码推荐、颜色搭配、洗涤养护等文本资料构建为本地向量知识库，用户提问时系统会先检索相关知识片段，再结合大语言模型生成回答。

## 项目简介

本项目主要用于演示一个轻量级 RAG 应用的完整流程，包括：

- 本地文本资料上传
- 文本内容 MD5 去重
- 长文本自动切分
- 文本向量化
- Chroma 本地向量数据库存储
- 基于相似度的知识检索
- 结合通义千问模型生成问答结果
- 本地文件保存多轮对话历史

相比普通大模型问答，本项目的回答会优先参考本地知识库内容，适合用于垂直领域知识问答、课程设计、RAG 原型验证等场景。

## 技术栈

- Python
- LangChain
- Chroma
- DashScope Embeddings
- 通义千问 ChatTongyi
- Streamlit

## 项目结构

```text
RAG/
├── app_file_uploader.py      # Streamlit 文件上传页面
├── config_data.py            # 项目配置文件
├── file_history_store.py     # 本地对话历史存储
├── knowledge_base.py         # 知识库构建、文本切分、MD5 去重
├── rag.py                    # RAG 问答主链路
├── vector_stores.py          # Chroma 向量库封装
├── data/                     # 示例知识资料
│   ├── 尺码推荐.txt
│   ├── 颜色推荐.txt
│   └── 洗涤养护.txt
├── chroma_db/                # 本地向量数据库目录
├── chat_history/             # 本地聊天历史目录
└── md5.text                  # 已导入文本的 MD5 记录
```

## 环境准备

建议使用 Python 3.10 或更高版本。

安装基础依赖：

```bash
pip install langchain langchain-community langchain-core langchain-chroma chromadb streamlit dashscope
```

如果本地环境缺少文本切分相关依赖，也可以补充安装：

```bash
pip install langchain-text-splitters
```

## API Key 配置

项目使用 DashScope 的向量模型和通义千问聊天模型，运行前需要配置环境变量：

```bash
set DASHSCOPE_API_KEY=你的API_KEY
```

PowerShell 中可以使用：

```powershell
$env:DASHSCOPE_API_KEY="你的API_KEY"
```

Linux 或 macOS 中可以使用：

```bash
export DASHSCOPE_API_KEY="你的API_KEY"
```

## 使用方法

### 1. 启动知识库上传页面

```bash
streamlit run app_file_uploader.py
```

打开页面后上传 `.txt` 文件，系统会自动读取文本内容，将其切分、向量化并写入本地 Chroma 向量数据库。

### 2. 运行 RAG 问答测试

可以直接运行 `rag.py` 中的测试代码：

```bash
python rag.py
```

也可以在代码中调用 `RagService`：

```python
from rag import RagService
import config_data as config

service = RagService()
result = service.chain.invoke(
    {"input": "身高170cm，体重130斤，推荐什么尺码？"},
    config.session_config
)

print(result)
```

## 核心流程

1. 用户上传或准备本地文本资料。
2. 系统计算文本 MD5，判断内容是否重复导入。
3. 对较长文本进行分段切分，保留合理上下文。
4. 使用 DashScope Embeddings 将文本片段转为向量。
5. 将向量和文本元数据保存到 Chroma 本地数据库。
6. 用户提问时，系统从向量库中检索最相关的知识片段。
7. 将检索结果、用户问题和历史对话一起交给大语言模型。
8. 模型生成最终回答，并保存对话历史。

## 示例知识库内容

当前 `data` 目录中包含服装相关示例资料：

- 尺码推荐：根据身高、体重推荐服装尺码
- 颜色推荐：根据肤色、场合、体型、季节推荐服装颜色
- 洗涤养护：根据材质和季节提供衣物清洗、晾晒、收纳建议

## 后续优化方向

- 增加完整的聊天 Web 页面
- 支持 PDF、Word、Markdown 等更多文档格式
- 增加检索结果重排序
- 支持多用户知识库隔离
- 增加知识库删除、更新和管理功能
- 添加 `requirements.txt` 统一管理依赖

## 说明

本项目适合作为 RAG 入门实践、课程设计或小型知识库问答系统原型。当前版本重点展示 RAG 的核心实现链路，功能较轻量，后续可以根据实际业务场景继续扩展。
