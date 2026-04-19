---
name: requirement-context-completion 
description: 补全需求的上下文和背景信息  
modeSlugs:
  - requirements-analysis
---

# Requirement Context Completion  
## Instructions  
1. 使用'memory-mcp'检索历史记忆学习经验！你有两个记忆工具：**记忆工程'memory-mcp'(融合云长期记忆，包含大量高价值流出问题经验)**，和内置的retrieve_memory工具 (项目工程记忆)。你应当依次使用这两个工具，检索相关历史记忆，学习过往经验，以提高对问题的覆盖度。  
2. Do some information gathering (using doors MCP and other provided tools) to get more context about the task and BMS requirements (if any). The goal is to complete the test requirement context so that it is clear enough for subsequent HIL‑based test design and automation. It should clearly specify:  
    * the relevant system conditions and preconditions ⚠️** All conditions MUST be evidence-based**,  
    * which inputs, signals, or operations need to be applied to the system, and  
    * the variables and keywords of the automated tests that need to be invoked or observed  
    * thresholds mentioned in the requirements.  
3. 你可以检索的主要信息及本项目相关文档的查询方式 **(若查询到的知识冲突，请咨询用户确认)**：  

    | 文档 | 可获取信息 | 查询方式 |
    | --- | --- | --- |
    | doors需求 | 系统需求、软件需求、软件详细设计 | MCP: doors-search |
    | 历史脚本 | 本项目的准出测试、smoke测试脚本等，变量和关键字尽可能参考历史脚本 | 本地路径：W02_HIL\\EP\\TestCase |
    | 电芯参数表 | 电芯基本参数、P-I Map、充电窗口、防脉冲大电流阈值、防过充浮充阈值、OCV、电芯相关故障、SOH参数、功能安全电流阈值、热管理参数、闭环策略&预欠压、预过压、老化修正系数、SOC映射、切电流策略等 | 本地路径：W02_BMS需求\\06_电芯参数表，可以使用MCP: doc-snapshot中的功能查询 |
    | CAN矩阵 | PT-CAN矩阵、SCAN矩阵、快充CAN矩阵 | MCP: knowledge-base-search |
    | 故障列表 | 故障定义、DTC等 | MCP: knowledge-base-search |
    | 诊断调查表 | 支持的UDS服务、OBD服务、DID、ECU ID、IO控制ID、DTC、快照等 | 本地路径：W02_BMS需求\\20_诊断调查表\\W02_BMS_B_0034_诊断调查表 |
    | 替代值策略 | 报文丢失/E2E故障时的信号替代值策略 | MCP: cloud-doc-reader, 查询w02_replacement_strategy |
    | 标准 | 暂支持部分国标企标 | 云端快速检索国标使用MCP: knowledge-base-search，若无法检索/需要视觉快照，可以检索本地路径 D:\\AI_PROJ\\ai_scripts\\标准 |
    | 交流充电流程 | 交流充电流程图 | / |
    | 直流充电流程 | 直流充电流程图 | / |
    | 高压上下电控制流程 | 高压上下电控制流程 | / |
    | 台架接线 | 台架接线表 | / |
    | 台架模型 | 台架的simulink模型 | / |
    | jira | 历史jira问题 | MCP: jira |
  
  
4. Ask the user clarifying questions only when you have already searched all related requirement IDs and information and still cannot obtain sufficient requirement context. You may want to consult the user when encountering any of the following situations:  
    * Contradictory information in the collected data  
    * Variables in NVM that require XCP to read or set but cannot be found  
    * Chip-related variables or faults that HIL test may can not achieve. Make sure you understand:  
        * the HIL test scenario and preconditions,  
        * the function under test, its intermediate logic and its expected behavior, and  
        * for every operation, observation, and signal mentioned in the requirement, which TX and RX variables/keywords on the HIL bench are intended to be used to apply it or to observe it.  
5. Double check the following information:   
    * ⚠️** All conditions MUST be evidence-based**,  
    * Verify that every variable, signal, and keyword mentioned in the requirements is correctly named and exists in the variable list or keyword list (via MCP). If an item cannot be found or is ambiguous (e.g. similar names, unclear type or unit), do not assume its meaning or spelling – ask the user to clarify or correct it.  
    * If it's a fault-based requirement, you should check the fault list to confirm. **(**⚠️**如果故障列表和用户提供的信息不一致，请咨询用户确认)**  
6. 完成需求上下文分析后，你应当先按照如下格式输出并让用户确认你获取的信息正确:  
    * Original Requirement  
        * Original requirement text from the user.  
    * Completed Requirement  
        * Background of the requirements.  
        * Refined and completed requirement description.  
        * Include, where applicable and evidence-based: preconditions, disabling conditions (if any), triggering conditions, recovery conditions(if any), and execution actions.()  
        * If a DTC is present, please convert it to hexadecimal, example: 0x163049.  
    * Variables and Keywords  
        * Must include all variables and keywords required (for automated testing later) in the requirements.  
        * All variables and keywords used should be searchable via variable search in MCP.  
        * One variable per line, format:  
            * `Variable_<index>` | `<Variable Name>` | `<Direction: TX or RX>`**(**⚠️**TX means HIL set/send, RX means HIL read)**  
        * One keyword per line, format:  
            * `Keyword_<index>` | `<Keyword Name>`  
    * References  
        * Source of the requirement, e.g. DOORS link, file path, or document name/version.  
        * Please note the source of every supplementary information.  
7. 必须在用户确认后，再按照上一步的格式返回结果给父任务。  
