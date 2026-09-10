# 1  Agent 介绍

- Agent = 大模型 + 工具 + 记忆 + 自主决策能力
	- LLM
	- Tools
	- Memory
	- ReAct
- Agent（智能体）这个概念是从强化学习来的，已经有接近 30 年的历史!!
![304](image/Pasted%20image%2020260810213101.png)

## 1.2  Memory
- Memory 记忆模块
	1. 大模型的 Memory 是内化在神经网络中的先验知识
	2. Agent 中 Memory 是分短期记忆，中期记忆，长期记忆的

## 1.3  Context
- 大模型的上下文窗口是有限的 --- 目前最大支持 100 万，200 万 tokens.

## 1.4  RAG
- RAG: Retrieval-Augmented Generation
	- RAG 全称检索增强生成，就是让大模型在回答问题前先去“查资料（检索）”，相当于开卷考试，模型本身可能没有学习过公司的内部文档，但只要把相关内容找出来给它，它就能结合这些资料给出靠谱的答案。
	![462](image/Pasted%20image%2020260810214837.png)

## 1.5  Harness
- Harness Engineering，驾驭工程
	- 是给 AI Agent 设计工作环境的一套工程方法，包括上下文管理，工具调用，沙箱环境，权限控制，测试验证，日志观测，评审机制和反馈回路。
	- 目的是为了让 Agent 在真实工程系统中更可靠，更可控，并持续完成任务

# 2  Function Calling, MCP and Skill？
- Function Calling (OpenAI 2023.6)
    - Function Calling 是最先出现的，当时的核心问题是「模型只会生成文本，怎么让它能触发外部调用」，所以 Function Calling 解决的是最基础的调用协议问题。
    - Function Calling 发生在模型和函数之间，是单次调用的格式规范。
	
- MCP (Anthropic 2024.11)
    - 后来 Function Calling 普及了，大家发现一个新的痛点：每个应用都要自己写代码对接各种工具，数据库一套，文件系统一套，API 又一套，重复劳动太多了。MCP 就是在这个背景下出现的，Function Calling 是最底层的通信语言，往上一层就是 MCP，它把工具的接入标准化了，一次实现到处复用。
    - 从调用关系上看，它发生在 AI 客户端 (MCP Client) 和工具服务 (MCP Server) 之间，是工具的标准化封装协议。
    - MCP 为什么在 Function Calling 之上？因为 MCP 做的是工具管理和标准化封装，但归根到底，MCP Server 暴露的工具最终还是要通过 Function Calling 的格式传给模型，让模型来触发，所以 MCP 天然依赖 Function Calling。
	
- Skill (Anthropic 2025.10)
    - 再后来，工具有了，接入也标准化了，又冒出了新问题：Agent 有了一堆工具，但面对一个复杂任务，它不知道该按什么流程，什么标准来用这些工具。
		- **Skill 就是为了解决这个「知识和流程复用」的问题才诞生的**
	- 从架构关系上看，MCP 再往上一层就到了 Skill，它发生在 Agent 和知识模块之间。Agent 启动的时候会扫描可用的 Skill 列表，当用户提了一个任务，Agent 自己判断哪个 Skill 和这个任务相关，主动去加载 SKILL.md 里的执行指令，脚本和模板。
		- **Skill 的粒度比 MCP 工具粗得多：**
		    - 「代码审查」是一个 Skill
		    - 「数据分析报告」是一个 Skill
		- 每个 Skill 内部可能涉及好几个步骤，调用好几个 MCP 工具
	- 所以完整的链条是：**Skill (定义流程) -> MCP (提供工具) -> Function Calling (模型触发调用)**
		- 从上到下三层，每一层都建立在下一层的基础之上，各司其职。
	![](image/Pasted%20image%2020260811172945.png)

# 3  三大 Agent 框架之争的本质是什么？
- **OpenClaw, Hermes, LangGraph** 不是三个同层产品，而是三条不同的 **Agent** 路线：
    - **OpenClaw**
        - 解决的是：**Agent** 如何被普通人直接使用
        - OpenClaw 更像“现成助手”
        - 它关注的是聊天入口、工具调用、任务执行、个人场景闭环
    - **Hermes**
        - 解决的是：**Agent** 如何从使用经验中持续成长
        - Hermes 更像“会成长的代理”
        - 它关注的是长期记忆、Skill 沉淀、经验复用、技能修补、自我进化
    - **LangGraph**
        - 解决的是：**Agent** 如何被企业级应用稳定开发
        - LangGraph 更像“底层骨架”
        - 它关注的是节点、状态、流程、分支、持久化、人机协同
![](image/Pasted%20image%2020260812103848.png)

## 3.1  OpenClaw

- OpenClaw 🦞 是一个**本地优先，始终在线的 Agent 网关** —— 它不是 IDE 插件，也不是云端服务，而是跑在你自己机器上的守护进程，像一个全天候在线的私人助理 🕵️‍♂️。

|**步骤**|**官方说的时间**|**实际花的时间**|
|---|---|---|
|**跑安装脚本**|5分钟|5分钟 ✓|
|**配API Key + 基础环境**|"简单"|2小时|
|**配Docker + 网络**|"可选"|1.5小时|
|**调提示词让AI听懂人话**|—|3小时|
|**修间歇性失忆**|—|1.5小时|
|**总计**|**5分钟**|**8小时+**|

- 开始从零重新设计整个脚本，你只能眼睁睁看着，因为你打断不了它，你会发现这玩意儿根本没有"停止"按钮。别的AI工具按个Esc就停了，OpenClaw不行。你手忙脚乱找到输入框疯狂敲 "stop" —— 它还得反应一会儿，你只能看着它在那里一本正经地把刚才学会的东西重新学一遍，你的Token在燃烧，你的血压在飙升，而那只龙虾 🦞 在快乐地重新造轮子。

- OpenClaw： 核心是 WebSocket 服务器，负责在通信平台和 LLM 之间路由消息。  
	1. Always-On + 心跳机制  
		- OpenClaw 的 Gateway 是一个持续运行的守护进程。它有“心跳”❤️(Heartbeat) 机制——每隔一段时间自动检查邮件，日历，社交媒体动态，有重要信息时主动通知你。  
		- 这就像一个助理每天几次主动敲门：“老板，有封重要邮件”，“下午三点有个重要会议”。  
	2. 文件即记忆 (Text > Brain)  
		- OpenClaw 的记忆完全基于文件：  
			- SOUL.md : Agent 的人格身份  
			- USER.md : 关于你的信息  
			- MEMORY.md : 长期记忆 (仅在私聊中加载，群聊不加载——保护隐私)  
			- memory/YYYY-MM-DD.md : 每日原始记录  
		- 核心理念：“写下来📝，不要只记在脑子里🧠”，因为每次会话都是全新的，只有文件能在会话之间传递信息。  
	3. 内置 Cron 调度器  
		- 这是 OpenClaw 的独特优势：原生的定时任务系统。  
		- 你可以设定“每天早上 8 点分析 A 股市场”，“每小时检查邮箱动态”  
	4. 模型无关 + 多提供商  
		- OpenClaw 不绑定任何 LLM。同一套配置下，你可以用 GLM-5.1, Qwen3.7, DeepSeek V4, Claude opus 4.8, Claude Mythos 5 / Fable 5, GPT-5.5, 这让它在模型选择上极其灵活，特别适合中国用户接入国产模型。  
	5. Skills 兼容标准  
		- OpenClaw 采用 SKILL.md 格式，和 Claude Code, Codex 的技能格式互通。你在 OpenClaw 上积累的技能，可以无缝其他框架。

## 3.2  Hermes
- Hermes Agent 🦙 由 Nous Research 开发，是目前**唯一内置自学习循环的开源 Agent**——它从使用经验中自动创建技能 (Skills)，在使用过程中自我改进，跨会话记住你是谁。
    - 源码：[https://github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
        
- Hermes 的架构很“正统”：一个 Python 包，通过 Agent Loop (推理 → 行动 → 观察循环) 驱动 LLM，工具注册表提供 47 个内置工具，持久化层管理记忆和技能。

- Hermes  最引人注目的宣称是“自学习循环” (Self-Improving Loop)，这不是营销术语，它由四个真实机制组成：  
	1. 自动技能创建 (Skill Creation)  
	    - 当你让 Hermes 完成一个复杂任务 (比如 “帮我用 Docker 部署一个 PostgreSQL 集群”)，它完成后会自动把成功的工作流保存为一个 Skill —— 下次遇到类似任务，直接加载复用，不用从头探索。  
	    - 这就像一个厨师每次创新菜谱后都会记在本子上，下次直接翻本子做。  
	2. 使用中改进 (In-Use Improvement)  
	    - 如果 Hermes 发现自己之前保存的 Skill 有问题 (比如某个命令在新版本失效了)，它会在下次使用时自动修正。  
		- 这是通过 skill_manage 工具的 patch 动作实现的 —— 只改出错的部分，不用重写整个技能。  
	3. 跨会话记忆 (Cross-Session Memory)  
		- Hermes 的记忆系统由两个文件组成：  
			- MEMORY.md (~800 tokens): Agent 的个人笔记 —— 环境事实，工具技巧，经验教训  
			- USER.md (~500 tokens): 你的档案 —— 偏好，习惯，沟通风格  
		- 每次新会话开始时，这些记忆被注入系统提示 (System Prompt)。Agent 知道你 “喜欢简洁回复，讨厌废话”，也知道 “这台机器装了 Docker 和 PostgreSQL 16”。  
	4. 会话搜索 (Session Search)  
		- 所有历史对话存在 SQLite 数据库中，Agent 可以搜索 “上周我们讨论过什么部署方案”。
		
**Hermes 🦙 真实现状定位 📌**
- Hermes 在**技能积累**方面做得很扎实，但在**记忆深度**和**自我反思质量**上还有明显差距。它的“自学习”更像是“自动化的经验记录”，而不是“真正的自我认知提升”。
    - 这符合当前业界现状——2025 ~ 2026 年的多项研究表明，没有外部反馈的纯自我修正往往失败，Agent 容易自信地强化自己的错误。Hermes 通过工具执行结果的反馈部分解决了这个问题，但离“可靠的自主学习”还有相当的距离。

| 维度     | Hermes Agent                              | OpenClaw                                 |
| ------ | ----------------------------------------- | ---------------------------------------- |
| 设计理念   | 单智能体自我进化                                  | 多智能体编排                                   |
| 技能创建   | ✅ 完全自主 + agentskills.io                   | 手动 + 自动捕获                                |
| 技能优化   | ✅ 使用中 + Patch                             | 使用追踪 + 微学习                               |
| 记忆系统   | ✅ 四层分离 (Prompt / Skills / Session / User) | 3 层 (MEMORY / memory / vault) + LightRAG |
| 用户建模   | ✅ 12 层 Honcho                             | ❌ 有限                                     |
| 多智能体支持 | ❌ 可选 (Sequential Rollout)                 | ✅ 原生 (CEO / Worker / Coordinator)        |
| 平台网关   | ✅ 15+ 平台                                  | ✅ 15+ 平台                                 |
| 知识图谱   | ❌ 无                                       | ✅ LightRAG (实体 + 关系)                     |
| 社区生态   | ✅ Skills Hub (agentskills.io)             | ✅ ClawHub (5400+ 技能)                     |
| 定时任务   | 有 (cronjob 工具)                            | 更强 (原生 Cron 调度器)                         |
| 子代理    | 有 (delegate_task)                         | 有 (subagent 系统)                          |

## 3.3  [[LangChain]] / LangGraph

![](image/Pasted%20image%2020260813093852.png)

- 企业级 Agent 工作流：
    - 研发自动化 Agent
    - 多步骤数据处理
    - 需要人工审批的流程
    - 需要可靠恢复的长任务

- 明显特征：
    - LangGraph 不是开箱即用的个人助手
    - 没有编排 (编程)，Agent 很容易变成一个黑盒，这是企业级开发最不能容忍的！！
    - 你需要自己定义节点，状态，工具，逻辑和产品入口 (写代码，工程师思维)
    - 所以它更适合开发者和工程团队。

### 3.3.1  **LangChain / LangGraph 发展历史**
- 2022年10月，LangChain项目由哈佛大学发起的LLM开源框架，首次在github上发布。
- 2024年1月8日，LangChain v0.1.0 正式发布，是官方推出的首个稳定版本，标志着从原型开发迈向生产阶段。
- 2024年5月20日，LangChain v0.2.0 正式发布，重点将LangChain和LangChain-Community完全分离，标志着框架向模块化和代理中心化的重要转变。
- 2024年9月，LangChain v0.3.0 正式发布，重点对类型进行升级以及多模态适配，自此LangChain已经非常匹配工业界的开发了。
- 2025年10月23日，LangChain v1.0.0 正式发布，这一版本的发布标志着LangChain框架从实验性工具向生产级AI智能体开发平台的重大跃迁，官方将其定位为“**生产就绪**”，并带来了架构级重构与工程化升级！！！
    - 🍊 截至2026年6月12号，最新版本更新到 v1.3.7

### 3.3.2  LangChain 生态

- 未来的 "LangChain + 大前端 + AI + 云计算" 开发生态 ✨：
![](image/Pasted%20image%2020260813104910.png)

>[!faq] 为什么未来的程序员需要用 LangChain 而不是直接使用 OpenAI，或者 Huggingface 上面的大模型？
>- 这些大厂和独角兽可以提供基座模型和 API，但是在产品集成和使用它们之间，还有大量的工作要做！

**LangChain / LangGraph 主流开发架构**

- **langchain-core**: 基础抽象和LangChain表达式语言.
- **langchain-community**: 第三方集成 合作伙伴包 (例如 langchain-openai, langchain-anthropic)
- **langchain**: 构成应用程序认知框架的链, 代理, 检索.
- **langgraph**: 通过将步骤建模为图中的边和节点, 使用LLM构建健壮且有状态的多角色应用程序.
- **langserve**: 将LangChain链部署为REST API.

>[!Summary] 结论：三者都在解决 Agent 落地问题，但落点完全不同。
>1. OpenClaw 解决“Agent 怎么真正用起来”
>	- 普通用户没有技术背景，怎么快速用起来的问题
>	- OpenClaw 更偏产品体验，不适合做高度定制的底层 Agent 编排系统          
>2. Hermes 解决“Agent 怎么越用越强”
>	- Hermes 的核心，就是把记忆，技能，反馈闭环做成长期成长机制
>3. LangGraph 解决“复杂 Agent 怎么稳定跑”
>	- 让开发者搭建复杂 Agent 的运行底座，适合企业级开发，AI工程师的工具
# 4  Agentic Agent 掌握什么技术才是AI时代真正的护城河？

1. **高层视角的架构思维**
    - 一个系统是如何运转的？
    - 需要注意哪些痛点和边缘条件？
    - 行业内的踩坑经验是什么？
        
2. **需求分析与产品思维**
    - 未来不懂技术的产品经理没法活
    - 未来不具备产品思维的工程师寸步难行
        
3. **底层原理的经典与不变性**
	- 1989年的反向传播算法
	- 2015年的残差连接
	- 2017年的 Transformer, PPO
	- 2017年的 Ring All-Reduce
	- 2021年的 ViT
	- 2022年的 CoT, RLHF
	- 2023年的 MoE
	
4. **永恒的项目经验 + 动手能力**
    - AI时代有了最强大脑的辅助，项目经验依然是 360 行最重要的护城河
    - AI时代的 Coding 能力只有在具备动手能力的工程师手中才能发挥最大效用