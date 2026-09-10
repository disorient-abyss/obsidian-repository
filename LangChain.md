# 1  简单认识 LangChain 

LangChain连接了底层开发细节与上层应用👇：
![](image/Pasted%20image%2020260814155551.png)

## 1.1  LangChian 的高层架构
![](image/Pasted%20image%2020260814155924.png)

## 1.2  **LangChain / LangGraph 主流开发架构**

- **langchain-core**: 基础抽象和LangChain表达式语言.
- **langchain-community**: 第三方集成 合作伙伴包 (例如 langchain-openai, langchain-anthropic)
- **langchain**: 构成应用程序认知框架的链, 代理, 检索.
- **langgraph**: 通过将步骤建模为图中的边和节点, 使用LLM构建健壮且有状态的多角色应用程序.
- **langserve**: 将LangChain链部署为REST API.

## 1.3  LangChain 的主要模块
- agents
- memory
- chains
- data_connection
- model_io

## 1.4  LangChain与搜索🔍 + RAG 功能结合的开发架构

![](image/Pasted%20image%2020260814160917.png)

# 2  LangChian 核心组件

## 2.1  Agents

- 在整个LangChain的开发架构中，Agents居于核心地位，类比于强化学习中的智能体，一切"乐高积木"的搭建都是围绕Agents来进行的。
---

### 2.1.1  先看一个 Agents 的实例代码
```python
import os

from dotenv import load_dotenv
from langchain.agents import create_agent
from langchain_core.tools import tool
from langchain_openai import ChatOpenAI

# 1. 加载配置并校验
load_dotenv("./keys/deepseek_key.txt")

api_key = os.getenv("DEEPSEEK_API_KEY")
api_base = os.getenv("DEEPSEEK_API_BASE")

if not api_key:
    raise ValueError("未读取到 DEEPSEEK_API_KEY，请检查 ./keys/deepseek_key.txt 文件路径或内容。")

# 2. 显式定义带 @tool 的工具
@tool
def get_word_length(word: str) -> int:
    """计算并返回给定单词的字母数量。"""
    return len(word.strip())

# 3. 显式配置 Model
llm = ChatOpenAI(
    model="deepseek-chat",
    api_key=api_key,
    base_url=api_base,
    temperature=0,  # 工具调用场景建议设为 0 保证稳定
)

# 4. 创建并运行 Agent
agent = create_agent(
    model=llm,
    tools=[get_word_length],
    system_prompt="你是一个非常有用的助手，擅长提供单词长度信息。",
)

res = agent.invoke({"messages": [{"role": "user", "content": "单词 education 总共有几个字母?"}]})
print(res['messages'][-1].content)
```

>[!Question] 关于在 V1.x 版本中创建 Agent?
>1. 大模型，model = ?
>2. 工具箱，tools = ?

### 2.1.2  Agent 中增加工具调用能力
```python
import os

from dotenv import load_dotenv
from langchain.agents import create_agent
from langchain_core.tools import tool
from langchain_openai import ChatOpenAI

# 1. 加载配置并校验
load_dotenv("./keys/deepseek_key.txt")

api_key = os.getenv("DEEPSEEK_API_KEY")
api_base = os.getenv("DEEPSEEK_API_BASE")

if not api_key:
    raise ValueError("未读取到 DEEPSEEK_API_KEY，请检查 ./keys/deepseek_key.txt 文件路径或内容。")

# 这里定义一个无效的工具，工具还是启作用的 
@tool
def get_weather(city: str) -> str:
    """Get weather for a given city."""
    return f"It's always sunny in {city}!"

# 3. 显式配置 Model
llm = ChatOpenAI(
    model="deepseek-chat",
    api_key=api_key,
    base_url=api_base,
    temperature=0,  # 工具调用场景建议设为 0 保证稳定
)

agent = create_agent(
    model=llm,
    tools=[get_weather],
    system_prompt="你是一个非常有用的助手，擅长提供城市天气信息。",
)

# Run the agent
res = agent.invoke(
    {"messages": [{"role": "user", "content": "上海的天气怎么样?"}]}
)
print(res['messages'][-1].content)
```

### 2.1.3  增加 Agents 的 ReAct 能力，ReAct 的能力可以归纳为“推理 + 操作”
```python
import os

from dotenv import load_dotenv
from openai import OpenAI  # <-- 改用原生 openai 库

# 1. 加载配置并校验
load_dotenv("./keys/qwen_key.txt")

api_key = os.getenv("Qwen_API_KEY")
api_base = os.getenv("Qwen_API_BASE")

if not api_key:
    raise ValueError("未读取到 Qwen_API_KEY，请检查 ./keys/qwen_key.txt 文件路径或内容。")

client = OpenAI(
    api_key=api_key,
    base_url=api_base,
)

response = client.chat.completions.create(
    # model="qwen-deep-research-2025-12-15",
    model="qwen-plus",
    # 🌐 启用搜索
    web_search_options={},
    messages=[
        {
            "role": "system",
            "content": [
                {
                    "type": "text",
                    "text": "你是一个非常有用的助手，搜索信息并计算数字。"
                }
            ]
        },
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": "姚明的老婆的身高是多少?"
                }
            ]
        }
    ],
    temperature=0.7
)

print(response.choices[0].message.content)
```

## 2.2  [[Memory]]

- LangChain为了支持多轮对话，一个很重要的功能就是对过去信息的记忆，即Memory模块.  

- 展示一个Memory应用的小栗子🌰.
```python
import os

from dotenv import load_dotenv
from langchain_core.chat_history import InMemoryChatMessageHistory
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_openai import ChatOpenAI

# 1. 加载配置并校验
load_dotenv("./keys/deepseek_key.txt")

api_key = os.getenv("DEEPSEEK_API_KEY")
api_base = os.getenv("DEEPSEEK_API_BASE")

if not api_key:
    raise ValueError("未读取到 DEEPSEEK_API_KEY，请检查 ./keys/deepseek_key.txt 文件路径或内容。")

# 3. 显式配置 Model
llm = ChatOpenAI(
    model="deepseek-chat",
    api_key=api_key,
    base_url=api_base,
    temperature=0,  # 工具调用场景建议设为 0 保证稳定
)

# 3. 定义支持历史上下文的 Prompt
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个友善、专业且乐于助人的 AI 助手。"),
    MessagesPlaceholder(variable_name="history"),
    ("human", "{input}"),
])

chain = prompt | llm

# 4. 会话状态管理 (基于 Session ID 隔离)
session_store: dict[str, InMemoryChatMessageHistory] = {}

def get_session_history(session_id: str) -> InMemoryChatMessageHistory:
    if session_id not in session_store:
        session_store[session_id] = InMemoryChatMessageHistory()
    return session_store[session_id]

# 5. 构建带记忆的 Runnable 链
conversation = RunnableWithMessageHistory(
    runnable=chain,
    get_session_history=get_session_history,
    input_messages_key="input",
    history_messages_key="history",
)

# 6. 对话调用函数
def chat(message: str, session_id: str = "default_user") -> str:
    response = conversation.invoke(
        {"input": message},
        config={"configurable": {"session_id": session_id}}
    )
    return str(response.content)

# 7. 测试多轮上下文对话
if __name__ == "__main__":
    dialogues = [
        "嗨，你好啊！",
        "我很好，正在和AI进行对话呢！",
        "给我讲讲关于你的事情吧，顺便提一下我上一句话说了什么？"
    ]

    for user_input in dialogues:
        print(f"User: {user_input}")
        reply = chat(user_input)
        print(f"AI: {reply}\n" + "-" * 40)
```

### 2.2.1  Memory-Cut

- 通过定义消息修建器保证记忆不过长
```python
import os
from operator import itemgetter

from dotenv import load_dotenv
from langchain_core.chat_history import InMemoryChatMessageHistory
from langchain_core.messages import trim_messages
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.runnables import RunnablePassthrough
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_openai import ChatOpenAI

# 1. 加载配置并校验
load_dotenv("./keys/deepseek_key.txt")

api_key = os.getenv("DEEPSEEK_API_KEY")
api_base = os.getenv("DEEPSEEK_API_BASE")

if not api_key:
    raise ValueError("未读取到 DEEPSEEK_API_KEY，请检查 ./keys/deepseek_key.txt 文件路径或内容。")

# 2. 显式配置 Model
llm = ChatOpenAI(
    model="deepseek-chat",
    api_key=api_key,
    base_url=api_base,
    temperature=0,  # 工具调用场景建议设为 0 保证稳定
)

# 例如：保留最近 2 轮历史（即 4 条消息）
K = 2

# 3. 定义消息修剪器（保留最后 max_tokens，并保证 system 提示词始终保留）
trimmer = trim_messages(
    max_tokens= K * 2,                # 最大保留 Token 数，也可以通过 K * <?> 的方式记录保留几轮对话
    strategy="last",                  # 保留最近的消息（滑动窗口）
    token_counter=len,                # 使用对应模型的分词器计算精确长度
    include_system=True,              # 始终保留 system 角色指令
    start_on="human",                 # 裁剪后确保以用户提问开头，防止格式错乱
)

# 4. 定义支持历史上下文的 Prompt
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个AI助手。"),
    MessagesPlaceholder(variable_name="history"),
    ("human", "{input}"),
])

chain = (
    RunnablePassthrough.assign(
        history=itemgetter("history") | trimmer
    )
    | prompt
    | llm
)

# 5. 会话状态管理 (基于 Session ID 隔离)
session_store: dict[str, InMemoryChatMessageHistory] = {}

def get_session_history(session_id: str) -> InMemoryChatMessageHistory:
    if session_id not in session_store:
        session_store[session_id] = InMemoryChatMessageHistory()
    return session_store[session_id]

# 6. 构建带记忆的 Runnable 链
conversation = RunnableWithMessageHistory(
    runnable=chain,
    get_session_history=get_session_history,
    input_messages_key="input",
    history_messages_key="history",
)

# 7. 对话调用函数
def chat(message: str, session_id: str = "default_user") -> str:
    response = conversation.invoke(
        {"input": message},
        config={"configurable": {"session_id": session_id}}
    )
    return str(response.content)

# 8. 测试多轮上下文对话
if __name__ == "__main__":
    dialogues = [
        # 第 1 轮：植入特征信息（暗号）
        "我的幸运数字是 9527，请记住它。",
        # 第 2 轮：正常闲聊（占用 1 轮）
        "今天北京的天气真不错，适合出去散步。",
        # 第 3 轮：继续闲聊（此时第 1 轮应该已被挤出 2 轮窗口）
        "帮我推荐一本关于人工智能的书籍吧。",
        # 第 4 轮：验证提问
        "你还记得我的幸运数字是多少吗？"
    ]
    
    for user_input in dialogues:
        print(f"User: {user_input}")
        reply = chat(user_input)
        print(f"AI: {reply}\n" + "-" * 40)
```

## 2.3  data_connection

- LangChain 开发框架需要处理数据的模块由 data_connection 处理

### 2.3.1  langchain 的文档读取
- 读取本地文档
```python
from pathlib import Path

from langchain_core.documents import Document

# 1. 使用 pathlib 处理路径，基于当前文件位置定位，避免因执行目录不同导致找不到文件
file_path = Path(__file__).resolve().parent / "tests/state_of_the_union.txt"

try:
    # 2. 原生读取文件
    with open(file_path, "r", encoding="utf-8") as f:
        text = f.read()

    # 3. 将读取的文本封装为 LangChain 标准 Document 对象列表
    docs = [Document(page_content=text, metadata={"source": str(file_path)})]

    # 3. 结构化格式化输出
    if docs:
        first_doc = docs[0]
        print(f"文档总数: {len(docs)}")
        print(f"文档对象类型: {type(first_doc)}")
        print(f"元数据 (Metadata): {first_doc.metadata}")
        print("-" * 40)
        print(f"前 100 字符预览:\n{first_doc.page_content[:100]}...")
    else:
        print("未加载到任何文档内容。")

except FileNotFoundError:
    print(f"错误: 找不到文件 -> {file_path.resolve()}")
except Exception as e:
    print(f"加载失败: {e}")
```

- 读取 arxiv 网站上的论文
```python
import json
import re
import urllib.error
import urllib.request

import arxiv
import fitz  # 来自 pymupdf
from langchain_core.documents import Document

# 匹配现代 arXiv ID (如 2412.02612 / 2412.02612v1) 与旧版分类 ID (如 math/0309136)
ARXIV_ID_PATTERN = re.compile(
    r"^(\d{4}\.\d{4,5}(v\d+)?|[a-zA-Z\-]+(\.[a-zA-Z]{2})?\/\d{7})$"
)


def is_arxiv_id(query: str) -> bool:
    """精准判断输入是 arXiv ID 还是常规搜索词"""
    return bool(ARXIV_ID_PATTERN.match(query.strip()))


def extract_text_from_pdf_stream(pdf_bytes: bytes) -> str:
    """从内存中的 PDF 二进制流提取纯文本"""
    with fitz.open(stream=pdf_bytes, filetype="pdf") as doc:
        return "\n\n".join(page.get_text() for page in doc).strip()


def load_arxiv_documents(
    query: str,
    max_docs: int = 5,
    timeout: int = 30,
    headers: dict | None = None,
) -> list[Document]:
    """
    通过 arXiv API 检索论文并在内存中解析为 LangChain Document 列表。
    
    :param query: 论文 ID (如 '2412.02612v1') 或搜索关键词
    :param max_docs: 最大返回篇数（仅关键词搜索时生效）
    :param timeout: PDF 下载超时时间 (秒)
    :param headers: 自定义 HTTP 请求头
    """
    client = arxiv.Client(page_size=max_docs, delay_seconds=3.0, num_retries=3)
    req_headers = headers or {"User-Agent": "ArxivDocLoader/1.0 (Python/LangChain)"}

    # 构造检索条件
    query_clean = query.strip()
    search = (
        arxiv.Search(id_list=[query_clean])
        if is_arxiv_id(query_clean)
        else arxiv.Search(query=query_clean, max_results=max_docs)
    )

    documents: list[Document] = []
    for result in client.results(search):
        try:
            # 携带 Header 与 Timeout 避免被服务端拦截或长时间挂起
            req = urllib.request.Request(result.pdf_url, headers=req_headers)
            with urllib.request.urlopen(req, timeout=timeout) as response:
                pdf_bytes = response.read()

            text = extract_text_from_pdf_stream(pdf_bytes)

            # 遵循 LangChain 标准元数据命名规范
            doc = Document(
                page_content=text,
                metadata={
                    "source": result.pdf_url,
                    "title": result.title,
                    "authors": [a.name for a in result.authors],
                    "published": result.published.strftime("%Y-%m-%d"),
                    "summary": result.summary,
                    "entry_id": result.entry_id,
                    "primary_category": result.primary_category,
                    "categories": result.categories,
                },
            )
            documents.append(doc)

        except (urllib.error.URLError, TimeoutError) as net_err:
            print(f"[警告] 下载 PDF 失败 ({result.pdf_url}): {net_err}")
        except Exception as parse_err:
            print(f"[警告] 解析 PDF 失败 ({result.entry_id}): {parse_err}")

    return documents


if __name__ == "__main__":
    target_query = "2412.02612v1"
    docs = load_arxiv_documents(query=target_query, max_docs=5)

    if not docs:
        print(f"未检索到与 '{target_query}' 相关的论文或下载解析失败。")
    else:
        print(f"成功获取文档数量: {len(docs)}")
        print("=" * 50)

        first_doc = docs[0]
        print("元数据 (Metadata):")
        print(json.dumps(first_doc.metadata, ensure_ascii=False, indent=2))

        print("-" * 50)
        print(f"论文正文前 200 字符预览:\n{first_doc.page_content[:200].strip()}...")
```

- 通过网址读取非结构化的 URL 地址内容
```python
import os

from dotenv import load_dotenv
from langchain_community.document_loaders import UnstructuredURLLoader
from langchain_core.documents import Document

# 1. 安全加载环境变量（若后续需要结合 LLM 处理，可在同目录创建 .env 文件写入 OPENAI_API_KEY）
load_dotenv()


def load_web_documents(
    urls: list[str],
    user_agent: str = (
        "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
        "AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"
    ),
) -> list[Document]:
    """
    使用 UnstructuredURLLoader 加载并解析指定网页列表为 Document。

    :param urls: 网页 URL 列表
    :param user_agent: 请求头 User-Agent，避免部分网站拦截
    """
    if not urls:
        return []

    # 配置请求头以应对防爬机制（如 403 Forbidden）
    headers = {"User-Agent": user_agent}
    loader = UnstructuredURLLoader(urls=urls, headers=headers)
    return loader.load()


if __name__ == "__main__":
    target_urls = ["https://react-lm.github.io/"]
    docs = load_web_documents(urls=target_urls)

    # 边界防御：检查抓取结果是否为空，避免 data[0] 引发 IndexError
    if not docs:
        print("[提示] 未抓取到任何内容或页面解析失败。")
    else:
        print(f"成功加载文档数量: {len(docs)}")
        print("=" * 50)

        first_doc = docs[0]
        print(f"来源 (Source): {first_doc.metadata.get('source', '未知')}")
        print("-" * 50)
        print("正文内容预览 (前 300 字符):")
        print(first_doc.page_content[:300].strip())
```

### 2.3.2  文档切分成 chunk
- 通过 RecursiveCharaterTextSplitter 对 txt 文件进行切分处理
```python
from pathlib import Path

from langchain_core.documents import Document
from langchain_text_splitters import Language, RecursiveCharacterTextSplitter


def split_text_file(
    file_path: str | Path,
    chunk_size: int = 100,
    chunk_overlap: int = 20,
    add_start_index: bool = True,
) -> list[Document]:
    """
    读取并基于字符递归分割文本文件。

    :param file_path: 待读取文本文件路径
    :param chunk_size: 每个块的最大字符长度
    :param chunk_overlap: 相邻块之间的重叠字符数
    :param add_start_index: 是否在元数据中包含块在原文本中的起始索引
    :return: 分割后的 Document 列表
    """
    path = Path(file_path).resolve()
    if not path.is_file():
        raise FileNotFoundError(f"未找到目标文件: {path}")

    content = path.read_text(encoding="utf-8")

    splitter = RecursiveCharacterTextSplitter(
        chunk_size=chunk_size,
        chunk_overlap=chunk_overlap,
        length_function=len,
        add_start_index=add_start_index,
    )
    return splitter.create_documents([content])


def get_supported_languages() -> list[str]:
    """获取 RecursiveCharacterTextSplitter 支持的代码/标记语言枚举值"""
    return [lang.value for lang in Language]

def find_overlap(text1: str, text2: str) -> str:
    """找出 text1 的后缀与 text2 的前缀之间的最大重叠字符串"""
    max_overlap_len = min(len(text1), len(text2))
    for i in range(max_overlap_len, 0, -1):
        if text1.endswith(text2[:i]):
            return text2[:i]
    return ""

if __name__ == "__main__":
    # 使用相对当前脚本文件的绝对路径定位，避免从不同目录运行时找不到文件
    current_dir = Path(__file__).parent
    target_file = current_dir / "tests" / "state_of_the_union.txt"

    # 1. 文本切分演示
    try:
        docs = split_text_file(
            target_file,
            chunk_size=120,
            chunk_overlap=40,
            add_start_index=True
        )
        
        print(f"成功切分文档，总块数: {len(docs)}")
        print("=" * 60)

        # 打印前 2 块及其元数据（包含 start_index）
        for idx, doc in enumerate(docs[:2], start=1):
            print(f"[ Chunk {idx} ] (元数据: {doc.metadata})")
            print(doc.page_content)
            print("-" * 60)

        # 遍历前 3 个 Chunk，并高亮对比相邻 Chunk 之间的重叠内容
        for i in range(min(3, len(docs))):
            doc = docs[i]
            print(f"\n👉 [ Chunk {i + 1} ] (起始索引 start_index: {doc.metadata['start_index']}, 字符数: {len(doc.page_content)})")
            print(doc.page_content)

            # 如果存在下一个 Chunk，计算并展示与下一个 Chunk 的重叠区域
            if i + 1 < len(docs):
                next_doc = docs[i + 1]
                overlap_text = find_overlap(doc.page_content, next_doc.page_content)
                print("\n" + "·" * 40)
                if overlap_text.strip():
                    print(f"🔗 【与下一 Chunk 的重叠文本 (共 {len(overlap_text)} 字符)】:\n「{overlap_text.strip()}」")
                else:
                    print("🔗 【与下一 Chunk 无重叠】(按大段自然换行断开)")
                print("·" * 40)

    except FileNotFoundError as err:
        print(f"[错误] {err}")

    # 2. 支持语言列表展示
    supported_langs = get_supported_languages()
    print(f"\n支持切分的代码/标记语言共 {len(supported_langs)} 种:")
    print(", ".join(supported_langs))
```

- 通过 RecursiveCharacterTextSplitter.from_language 对 HTML 文件进行切分处理
```python
from pathlib import Path
from langchain_core.documents import Document
from langchain_text_splitters import Language, RecursiveCharacterTextSplitter

SAMPLE_HTML = """
<!DOCTYPE html>
<html>
    <head>
        <title>LangChain</title>
        <style>
            body { font-family: Arial, sans-serif; }
            h1 { color: darkblue; }
        </style>
    </head>
    <body>
        <div>
            <h1>LangChain</h1>
            <p>Building applications with LLMs through composability.</p>
        </div>
        <div>
            As an open source project in a rapidly developing field, we are extremely open to contributions.
        </div>
    </body>
</html>
"""


def split_html_content(
    html_text: str,
    chunk_size: int = 100,
    chunk_overlap: int = 20,
) -> list[Document]:
    """
    针对 HTML 语义标签（如 <body>, <div>, <p>）进行结构化递归切分。

    :param html_text: 原始 HTML 字符串
    :param chunk_size: 每个分块的目标字符上限
    :param chunk_overlap: 分块间的重叠字符数
    :return: 封装好的 Document 列表
    """
    splitter = RecursiveCharacterTextSplitter.from_language(
        language=Language.HTML,
        chunk_size=chunk_size,
        chunk_overlap=chunk_overlap,
    )
    return splitter.create_documents([html_text.strip()])


def split_html_file(
    file_path: str | Path,
    chunk_size: int = 100,
    chunk_overlap: int = 20,
) -> list[Document]:
    """读取本地 HTML 文件并执行结构化切分"""
    path = Path(file_path).resolve()
    if not path.is_file():
        raise FileNotFoundError(f"未找到 HTML 文件: {path}")

    content = path.read_text(encoding="utf-8")
    splitter = RecursiveCharacterTextSplitter.from_language(
        language=Language.HTML,
        chunk_size=chunk_size,
        chunk_overlap=chunk_overlap,
    )
    # 使用 split_documents 保留文件来源元数据
    raw_doc = Document(page_content=content, metadata={"source": str(path)})
    return splitter.split_documents([raw_doc])


def find_overlap(text1: str, text2: str) -> str:
    """计算 text1 结尾与 text2 开头的最大重叠字符子串"""
    max_overlap_len = min(len(text1), len(text2))
    for i in range(max_overlap_len, 0, -1):
        if text1.endswith(text2[:i]):
            return text2[:i]
    return ""


if __name__ == "__main__":
    # 1. 查看 LangChain 针对 HTML 预设的分隔符优先级
    html_separators = RecursiveCharacterTextSplitter.get_separators_for_language(
        Language.HTML
    )
    print("HTML 语法切分优先级（按标签与换行逐级降级）:")
    print(html_separators)
    print("=" * 60)

    # 2. 切分 HTML 文本（设置 chunk_size=80 观察标签级别的断句效果）
    docs = split_html_content(SAMPLE_HTML, chunk_size=80, chunk_overlap=15)
    print(f"成功切分 HTML，总块数: {len(docs)}\n")

    # 3. 打印切分结果与重叠详情
    for i, doc in enumerate(docs):
        print(f"👉 [ Chunk {i + 1} ] (字符数: {len(doc.page_content)})")
        print(doc.page_content)

        if i + 1 < len(docs):
            overlap = find_overlap(doc.page_content, docs[i + 1].page_content)
            if overlap.strip():
                print(f"🔗 【重叠部分 ({len(overlap)} 字符)】: 「{overlap.strip()}」")
            else:
                print("🔗 【按 HTML 标签块独立断开，无字面重叠】")
        print("-" * 60)
```

### 2.3.3  调用 text-embedding-v3 (Qwen) 嵌入模型
```python
import os
from pathlib import Path

from dotenv import load_dotenv
from openai import OpenAI, OpenAIError

# 1. 加载配置并设置默认的阿里百炼兼容接口 Base URL
env_file = Path("./keys/qwen_key.txt")
if env_file.exists():
    load_dotenv(env_file)

api_key = os.getenv("Qwen_API_KEY")
# 若配置文件中未指定 API_BASE，则默认使用阿里百炼兼容模式 endpoint
api_base = os.getenv("Qwen_API_BASE")

if not api_key:
    raise ValueError("未读取到 Qwen_API_KEY，请检查 ./keys/qwen_key.txt 文件路径或内容。")

# 2. 初始化适配通义千问的客户端实例
client = OpenAI(
    api_key=api_key,
    base_url=api_base,
)


def get_embeddings(
    texts: list[str],
    model: str = "text-embedding-v3",
    dimensions: int | None = None,
    openai_client: OpenAI = client,
) -> tuple[list[list[float]], int]:
    """
    批量获取输入文本列表的 Qwen Embedding 向量。

    :param texts: 待向量化的文本列表
    :param model: 模型名称 (推荐 'text-embedding-v3'，默认 1024 维)
    :param dimensions: 自定义输出维度 (text-embedding-v3 支持 1024/768/512 等)
    :param openai_client: 已配置 Qwen Key 的客户端实例
    :return: (向量列表, 消耗的总 Token 数量)
    """
    if not texts:
        return [], 0

    request_params: dict = {"input": texts, "model": model}
    if dimensions is not None:
        request_params["dimensions"] = dimensions

    try:
        response = openai_client.embeddings.create(**request_params)

        # 提取向量数据（保持原输入顺序）
        embeddings = [item.embedding for item in response.data]
        total_tokens = response.usage.total_tokens if response.usage else 0

        return embeddings, total_tokens

    except OpenAIError as err:
        print(f"[API 错误] 生成 Qwen Embedding 失败: {err}")
        raise


if __name__ == "__main__":
    example_input = [
        "Hi there!",
        "Oh, hello!",
        "What's your name?",
        "My friends call me World",
        "Hello World!",
    ]

    try:
        # 1. 默认维度调用 (text-embedding-v3 默认为 1024 维)
        embeddings, tokens_used = get_embeddings(
            texts=example_input,
            model="text-embedding-v3",
        )

        print(f"成功生成向量条数: {len(embeddings)}")
        print(f"Qwen 向量默认维度 (Dimension): {len(embeddings[0])}")
        print(f"本次消耗 Token 数: {tokens_used}")
        print("=" * 50)
        print(f"首条向量预览 (前 5 个分量): {embeddings[0][:5]}...")

        # 2. 降维调用演示 (缩减至 512 维，节省向量数据库存储)
        print("-" * 50)
        compressed_embeddings, _ = get_embeddings(
            texts=example_input,
            model="text-embedding-v3",
            dimensions=512,
        )
        print(f"降维后向量维度: {len(compressed_embeddings[0])}")

    except Exception as e:
        print(f"执行中断: {e}")
```

### 2.3.4  对文本进行 embedding 然后存入 Chroma，进行语义相似度搜索。
```python
import os
import time
from pathlib import Path

from dotenv import load_dotenv
from langchain_community.document_loaders import TextLoader
from langchain_community.vectorstores import Chroma
from langchain_core.embeddings import Embeddings
from langchain_text_splitters import RecursiveCharacterTextSplitter
from openai import OpenAI

# 1. 安全读取配置与密钥
load_dotenv("./keys/qwen_key.txt")
api_key = os.getenv("Qwen_API_KEY")
api_base = os.getenv("Qwen_API_BASE")

if not api_key:
    raise ValueError("未读取到 Qwen_API_KEY，请检查 ./keys/qwen_key.txt 文件。")


class QwenEmbeddings(Embeddings):
    """
    基于 OpenAI 兼容协议实现的通义千问 Embeddings 封装类。
    支持批量调用以大幅降低网络延迟与建库耗时。
    """

    def __init__(
        self,
        api_key: str,
        base_url: str,
        model: str = "text-embedding-v3",
        dimensions: int | None = None,
        batch_size: int = 10,
    ):
        self.client = OpenAI(api_key=api_key, base_url=base_url)
        self.model = model
        self.dimensions = dimensions
        self.batch_size = batch_size

    def embed_documents(self, texts: list[str]) -> list[list[float]]:
        """批量对文档列表生成向量"""
        embeddings: list[list[float]] = []
        if not texts:
            return embeddings

        # 分批次批量调用，避免单次 payload 超限，同时避免单条请求的性能瓶颈
        for i in range(0, len(texts), self.batch_size):
            batch = texts[i : i + self.batch_size]
            params = {"input": batch, "model": self.model}
            if self.dimensions:
                params["dimensions"] = self.dimensions

            response = self.client.embeddings.create(**params)
            embeddings.extend([item.embedding for item in response.data])
        return embeddings

    def embed_query(self, text: str) -> list[float]:
        """对单条查询文本生成向量"""
        params = {"input": [text], "model": self.model}
        if self.dimensions:
            params["dimensions"] = self.dimensions

        response = self.client.embeddings.create(**params)
        return response.data[0].embedding


if __name__ == "__main__":
    # 2. 跨平台安全定位测试文本路径
    current_dir = Path(__file__).resolve().parent
    target_file = current_dir / "tests" / "state_of_the_union.txt"

    if not target_file.is_file():
        raise FileNotFoundError(f"未找到目标测试文件: {target_file}")

    # 3. 实例化 Qwen 嵌入模型
    embeddings = QwenEmbeddings(
        api_key=api_key,
        base_url=api_base,
        model="text-embedding-v3",
    )

    # 4. 加载并切分文本
    raw_documents = TextLoader(str(target_file), encoding="utf-8").load()
    text_splitter = RecursiveCharacterTextSplitter(chunk_size=300, chunk_overlap=30)
    documents = text_splitter.split_documents(raw_documents)
    print(f"成功加载并切分文档，生成分块数: {len(documents)}")

    # 5. 构建内存临时 Chroma 向量数据库并向量化入库
    t0 = time.time()
    db = Chroma.from_documents(
        documents=documents,
        embedding=embeddings,
        collection_name="state_of_the_union_qwen",
    )
    print(f"向量库构建完成，耗时: {time.time() - t0:.3f} 秒")
    print("=" * 60)

    query = "这里文档切片方面问了什么问题？"

    # 方式一：直接传文本进行相似度检索（自动由 embeddings.embed_query 转换为向量）
    t1 = time.time()
    docs_text_search = db.similarity_search(query, k=2)
    t1_cost = time.time() - t1

    print(f"【方式一：文本直搜】检索耗时: {t1_cost:.3f} 秒")
    print(f"Top 1 结果:\n{docs_text_search[0].page_content.strip()}")
    print("-" * 60)

    # 方式二：手动生成向量后通过向量检索（适合复杂管道预计算）
    t2 = time.time()
    query_vector = embeddings.embed_query(query)
    docs_vector_search = db.similarity_search_by_vector(query_vector, k=2)
    t2_cost = time.time() - t2

    print(f"【方式二：向量检索】向量化+检索耗时: {t2_cost:.3f} 秒")
    print(f"Top 1 结果:\n{docs_vector_search[0].page_content.strip()}")
    print("=" * 60)
```