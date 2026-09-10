# 对话的记忆管理

## 1  为什么要记忆？
这是因为大模型的每次调用都是独立的、无状态的，它不会记住用户是谁。

### 1.1  测试大模型记忆
- 代码逻辑
```python
import os

from dotenv import load_dotenv
from langchain_community.chat_models import ChatTongyi
from langchain_core.chat_history import InMemoryChatMessageHistory
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.runnables.history import RunnableWithMessageHistory

# 1. 加载配置并校验
load_dotenv("./keys/qwen_key.txt")

api_key = os.getenv("Qwen_API_KEY")
api_base = os.getenv("Qwen_API_BASE")

if not api_key:
    raise ValueError("未读取到 Qwen_API_KEY，请检查 ./keys/qwen_key.txt 文件路径或内容。")

# 注意：若使用官方标准模型名，常用为 "qwen-max"、"qwen-plus" 等；参数名为 dashscope_api_key
llm = ChatTongyi(
    model="qwen-plus",
    dashscope_api_key=api_key,
    temperature=0.2,
    top_p=0.8,
    enable_search=False,
    max_tokens=3000,
)

# 2. 定义支持历史上下文的 Prompt
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个友善、专业且乐于助人的 AI 助手。"),
    MessagesPlaceholder(variable_name="history"),
    ("human", "{input}"),
])

chain = prompt | llm

# 4. 会话状态管理 (必须在 RunnableWithMessageHistory 之前定义)
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
    # dialogues = [
    #     "嗨，你好啊！",
    #     "你好，我是disorient，是一个学习ai的学生",
    #     "我是谁？"
    # ]

    # for user_input in dialogues:
    #     print(f"User: {user_input}")
    #     reply = chat(user_input)
    #     print(f"AI: {reply}\n" + "-" * 40)

    # 第一次调用
    user_query = "你好，我是disorient，是一个学习ai的学生"
    print(f"disorient: {user_query}")
    reply = chat(user_query)
    print(f"AI: {reply}\n" + "-" * 40)

    # 第二次调用
    user_query_2 = "我是谁？"
    print(f"disorient: {user_query_2}")
    reply_2 = chat(user_query_2)
    print(f"AI: {reply_2}\n" + "-" * 40)
```

## 2  如何让大模型有记忆
- 目前让大模型有记忆的唯一方式：每次调用时，将之前的完整对话历史（会话列表）连同当前问题一起发送给模型，让模型基于上下文进行回答。

# 3  如何设计上下文记忆？
### 3.1  携带“全量上下文“的缺点
- 每次请求都携带全部对话历史，存在三个主要缺点：
	1. 成本高：对话越长，Token消耗越大，费用线性增长
	2. 速度慢：传输和处理大量数据，响应延迟增加
	3. 超长截断：模型有上下文窗口限制（如8K/128K），超长内容会被强行截断或报错
    
**一句话总结：** 全量记忆虽简单直接，但随着对话增长会面临成本、速度、长度三重瓶颈。

### 3.2  设计上下文的方案
- 由于每次请求携带全量上下文会大量消耗Token，一个直接的优化思路是：不保存全部历史，而是只截取最近N轮对话，每次将这部分消息与当前问题一同发送给模型。这个策略，就是**滑动窗口机制**。
	- 滑动窗口是指仅保存最近N轮对话作为记忆，超出窗口的历史信息自动丢弃。

#### 3.2.1  滑动窗口 - 保留最近N轮消息
 