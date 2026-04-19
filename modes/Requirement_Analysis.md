## 需求上下文分析(Requirement context completion)  

1. 需求上下文分析需要消耗较长上下文，所以请**创建一个子任务**后执行步骤2。  
2. 调用requirement-context-completion skill对需求上下文分析。  

<br>  

## 需求结构化分解(Requirement structured decomposition)  
Based on the current functional requirements, perform a structured decomposition of those requirements in order to thoroughly analyze all potential test points that may arise from each condition in the requirements. Please follow these steps.  

1. 模式划分(系统状态、阈值区间)  
    * 识别并定义功能点生效的所有可能系统状态和阈值区间。测试将围绕不同模式展开，模式的划分有利于验证不同系统状态下的表现差异，也便于测试模式之间状态跳转对功能的影响。  
    * 典型的模式划分包括：  
        * 充电模式 / 放电模式、运行模式 / 休眠模式、 不同的电压/电流/温度/SOC区间等。  
        * 充电模式可以进一步划分为：AC充电、DC充电(包括：小直流充电、HPC充电、公桩1000V充电、公桩750V充电、公桩500V充电等)。若测试涉及充电协议，请参考具体的标准及其版本。  
        * 放电模式可以进一步划分为：上高压状态和不上高压状态，通过变量HighVoltSupplyCommand控制。  
        * 在故障等级定义中，充电模式指“交流枪连接状态(CCSts)=1/2 或 直流枪连接状态(CC2Sts)=1”，其他状态均为放电模式。  
    * 请注意：  
        * 模式划分通过区分同一功能的不同触发场景和多重阈值组合，以简化测试用例设计。  
        * 当存在多维度组合时，应体现在模式划分中，如:  
            * Comb_Mode_1a | 放电且电流大于50A (充放电模式和电流阈值2维度组合)  
            * Comb_Mode_1bα | 国标2011充电、握手阶段、CRM超时(充电协议、充电阶段、故障类型3维度组合)  
        * 模式划分过程中衍生出的其他联想，可以作为补充测试思路的参考。若这些联想超出当前需求范围，请先判断该模式下的测试是否可行且有意义，然后询问用户是否需要将其纳入测试范围。  
2. 提取条件(前置、禁用、触发、恢复)  
    * 基于需求描述，识别与功能点相关的所有条件，并按类型进行归类：  
        * 前置条件(使能条件)：使功能点正常生效所必须满足的前提，如系统上高压、处于某充电模式、关键信号已就绪等。  
        * 禁用条件(抑制条件)：使功能点被暂停、禁用或抑制的条件(如有)。  
        * 触发条件(置位条件)：在前置条件满足后，直接触发该功能或导致故障发生的事件 / 阈值 / 条件组合。  
        * 恢复条件(复位条件)：使系统从故障状态或特殊状态恢复到正常状态的条件。  
    * 请区分每个条件的适用模式。  
3. 列出输出信号与执行动作  
    * 列出功能触发后系统对外可观测的行为(RX变量)，例如：故障标志位、故障等级、BMS发出的命令、状态信号、可被台架回采的动作等。  
    * 若存在中间变量仅用于计算或判断，也应说明其作用并标注为中间变量。  
    * 输出信号/动作涉及到后续的测试用例编写，因此必须具体到信号名。  
4. 提取时间特性(debounce时间)  
    * 说明各模式下，触发和恢复该功能的时间要求(确认时间、保持时间、恢复时间等)。  
    * 如需求中使用毫秒(ms)，请先换算为秒。  
5. After completing the requirement structured decomposition, you can summarize the results in the following form:   
    * Modes  
        * Specify the mode name, the conditions for entering the mode, and the system feedback (if any).  
        * Conditions may include one or more TX variables/keywords (and their required values or states), including any necessary order or timing.  
        * One mode per line, format:  
            * `Mode_<index>` | `<Mode Name>` | set: `<TX/keyword settings and sequence>` | feedback: `<RX signal feedback>`
        * If any multidimensional combination exists, list basic modes of all dimensions, then list all achievable combination modes:  
            * `Dimension_<index>` | `<Dimension Name>`  
                * `Base_Mode_<index>` (distinguish dimensions by different index categories, e.g. 123/abc/αβγ...) | `<Mode Name>` | set: `<TX/keyword settings and sequence>`  
                * (more dimensions...)  
            * Combinations (list all valid and invalid combinations, and combination invalid reason)  
                * `Comb_Mode_<index>` (index should corresponds to the base mode, start from Comb_Mode_1aα) | `<Mode Name>` | set: `<TX/keyword settings and sequence>` | feedback: `<RX signal feedback>`  
                * `Comb_Mode_<index>` | `<Mode Name>` | invalid| `<invalid reason>`
    * Conditions  
        * List all relevant conditions for the function, and classify each one by type:  
            * Precond  
            * Disable  
            * Trigger  
            * Recover  
        * For each condition, specify its description, applicable modes, and how to achieve or observe it.  
        * One condition per line, format:  
            * `<Precond/Disable/Trigger/Recover>_<index>` | `<Description>` | `<Applicable Modes>` | set: `<TX/keyword settings and sequence>` | feedback: `<RX signal feedback(only when intermediate variables need to be checked)>` | `<Reference (where it is defined, link or doc name)>`  
    * Output signals and execution actions  
        * Specify the output signals, execution actions, and the applicable modes.  
        * One item per line, format:  
            * `Output_<index>` | feedback: `<RX variables>`  
    * Debounce time  
        * Specify the debounce time and the applicable modes.  
        * One item per line, format:  
            * `Debounce_<index>` | `<Description>` | `<Applicable Modes>` | `<Reference>`  
6. After finish requirement structured decomposition, please update todo list.  
    ⚠️**历史脚本只能作为变量和关键字的参考，不能作为逻辑和阈值的来源**  
    ⚠️**如有来源不明的条件、阈值等，请咨询用户确认**  

<br>  

## 测试点维度分析(Test point dimension analysis)  
Based on the requirements decomposition, proavtively brainstorm and design testing strategies that cover various interactions, robustness, and boundary scenarios. Please follow these steps.  

1. 单输入分析  
    * 对每个输入进行系统性的取值分析，覆盖边界值、典型值、异常值、初始值、信号有效/无效范围、硬件采样范围、缺失值、替代值等。  
        * 边界值：需求定义的边界  
        * 初始值：参考矩阵、系统初始状态  
        * 信号有效/无效范围、硬件采样范围：参考矩阵、需求中对采样范围的定义...  
        * 缺失值：信号丢失场景  
        * 替代值策略：参考替代值策略表(暂未导入)  
    * 将取值分为以下等价类：  
        * 有效等价类：使得条件成立的一组取值，期望系统走“正常/允许/触发/恢复”等对应路径  
        * 无效等价类：使条件不成立的一组取值，期望系统走“拒绝/不触发/被禁用/不恢复/报错”等对应路径  
    * 测试边界值时偏移量的取值：偏移量应足够小以确保测试的有效性，但也应考虑实际系统的分辨率和噪声容忍度。具体要求：  
        | 信号类别 | 示例 | 偏移量取值  
        | -- | -- | -- |
        | 电芯电压 | Cell_V/Cell_Volt_1 | 0.02V  
        | 高压 | PackVolt/LinkVolt/DCVolt | 3V  
        | 低压 | KL30/KL30C/KL15/HALL5V供电/CSU供电 | 0.2V  
        | 温度 | Cell_T/Cell_Temp_1/ShuntTemp | 1℃  
        | 标定 | SOC_Cal/SOH_Cal | 1%  
        | 电流 | SensorCurrent/SensorCurrent2 | 电流<50A偏移量取2A，电流>50A偏移量取5A  
        | PWM | 硬线碰撞PWM/高压互锁PWM | 1%  
2. 多输入组合  
    * 通过多个输入的组合以覆盖各模式关键逻辑的测试场景。  
    * 对每个模式定义：  
        * 正向组合：进入该模式；使能条件全满足；禁用/抑制条件全不满足；触发条件全满足  
        * 反向组合：基于正向组合，每次只改一个条件状态，分别为该模式的每个使能/禁用/抑制/触发条件各生成1条反向用例  
3. 事件列表  
    * 覆盖每一个TX信号会引起系统行为的动态变化的事件：  
        * 离散信号跳变事件：布尔信号0/1间切换、枚举信号各枚举值之间的跳转  
        * 连续信号阈值穿越事件：从阈值一侧穿越到另一侧  
        * 模式切换事件：模式/系统状态之间的切换  
4. 时序组合  
    * 测试系统在特定事件序列下的行为，揭示与竞争条件、状态管理和优先级相关的缺陷。  
        * 时间精度边界：各条件debounce时间的边界测试  
            * debounce边界测试时，需考虑信号周期和台架设备响应速度。故障置出流程为：置出条件满足debounce时间后，输出信号在一个报文周期内置位，再经过偏差时间被台架确认。因此，检测等待时间 = Debounce时间 + 输出报文/动作周期 + 偏移量。偏移量推荐取值：有报文周期时取0.5倍周期，无周期时取0.1倍debounce时间。  
            * e.g. debounce为1s，输出信号报文周期为0.02s。则实际测试时，故障信号在1s内不应置出，在1s+0.02s+0.02*0.5s=1.03s内应当置出。  
        * 因果链：验证条件发生顺序对系统的影响  
            * e.g. 使能条件与触发条件的顺序；触发条件和禁用条件的顺序；触发条件和恢复条件的顺序；唤醒前报文丢失，唤醒后报文丢失  
        * 中断场景：正在进行的计时过程被更高级别的事件打断,  
            * e.g. 触发计时开始，禁用条件满足，验证计时是否立即中止  
5. After completing the test point dimension analysis, you can summarize the results in the following form:   
    * 单输入分析  
        * List the possible values, applicable modes, and value definitions of each input variable  
        * Use a formular to calculate the value with offset, format:  
            * `<Input Name>` | numeric type: `<Continuous or Boolean or Enum>`  
                * `<Input Name>_Valid_EC_<index>` | `<Input Name>` = `<Value (or formular: boundary ± offset...)>` | `<Applicable Mode>` | `<Definition (e.g. 使能条件边界，置位条件上边界内，初始值，替代值...)>`  
                * `<Input Name>_Invalid_EC_<index>` | `<Input Name>` = `<Value>` | `<Applicable Mode>` | `<Definition(e.g. 使能条件上边界外，无效值，禁用条件上边界内...)>`  
                * ...  
    * 多输入组合  
        * List the positive and negative combinations of each modes, format:  
            * `<Mode Name>`  
                * `<Mode Name>_Pos_Comb_<index>` | set: `<describe the test process that meets conditions and triggers the function, including settings and sequence>`  
                * `<Mode Name>_Neg_Comb_<index>` | set: `<modify the test process for one condition of Pos_Comb, so that the function can still be triggered or not triggered.>`  
                * ...  
    * 事件列表  
        * 离散信号跳变事件  
            * List all possible transitions for boolean and enumerated signals, format:  
                * `<Input Name>` | numeric type: `<Boolean or Enum>` | values: {`<Value1>`,`<Value2>`}  
                * `<Input Name>_Jump_<index>` | set: `<Value1>`->`<Value2>`
                * `<Input Name>_Jump_<index>` | set: `<Value2>`->`<Value1>`  
            * If the signal has multiple discrete values, use a 'value transition tabel' for analysis.  
            * A self-loop event can be viewed as a signal maintaining its original value and waiting.   
            * If a jump path cannot be reached, mark it as --, format:  
            * `<Input Name>` | numeric type: `<Enum>` | values: {`<Value1>`,`<Value2>`,`<Value3>`}  
                | FromTo | `<Value1>` | `<Value2>` | `<Value3>` |
                | ---------- | ---------- | ---------- | ---------- |
                | `<Value1>` | `<Wait_1>` | `<Jump12>` | `<Jump13>` |
                | `<Value2>` | `<Jump21>` | `<Wait_2>` | `<Jump23>` |
                | `<Value3>` | `<Jump31>` | < -- > | `<Wait_3>` |
        * 连续信号阈值穿越事件  
            * List all possible threshold crossing events for continuous signals, format:  
                * `<Input Name>` | numeric type: `<Continuous>` 
                    * `<Input Name>`_Cross_`<index>` | set: `<Value1>`->`<Threshold>`->`<Value2>`
                    * ...  
        * 模式切换事件  
            * List all possible mode switching events, format:  
                * `Mode_<index1>_switch_<index2>` | set: `<TX/keyword settings and sequence>`  
            * If there are multiple modes, use a 'mode transition tabel' for analysis, e.g.  
                | FromTo | `<Mode_1>` | `<Mode_2>` | `<Mode_3>` |
                | - | - | - | - |
                | `<Mode_1>` | < -- > | `<Jump12>` | `<Jump13>` |
                | `<Mode_2>` | `<Jump21>` | < -- > | `<Jump23>` |
                | `<Mode_3>` | `<Jump31>` | `<Jump32>` | < -- > |

    * 时序组合  
        * 时间精度边界  
            * List the possible values for the debounce time test.  
            * Use a formular to calculate the value with offset, format:  
                * `Debounce_<index>` | `<Description>`
                    * `Debounce_<index>_met` | `<Condition>` last `<time (unit: second, or use formular: debounce time (+ message cycle) ± offset...)>`  
                    * `Debounce_<index>_not_met` | `<Condition>` last `<time>` 
        * 因果链与顺序置换  
            * List all possible causal chains and sequence permutations, format:  
                * `Order_<index>` | `<Condition>` before `<Condition>`
        * 中断场景  
            * List all possible interrupt scenarios, format:  
                * `Interrupt_<index>` | `<Condition>` interrupt `<Condition>`  

6. 完成测试点维度分析后绘制需求分析思维导图

<br>

## 需求分析思维导图(Test point mermaid graph)

1. 思维导图的绘制和归档规范  
    * 所有测试任务在 TestCase/ 路径下创建独立目录，结构如下： 
        ```
        TestCase/  
        └── {测试阶段}_{日期}/{待测功能}/  
            ├── [进行中]Mermaid.md # 思维导图文档  
            ├── [未完成]TestPoint.md # 测试点文档  
            ├── [未完成]TestCase.md # 测试用例文档  
            ├── [未完成]TestCase01.robot # 测试套件(可多个) 
            └── [未完成]Resource.robot # 资源文件  
        ```
    * 测试阶段包括：Mule、EP、PPV、PP、P、SOP、OTA等，需要主动咨询用户当前测试阶段  
    * 待测功能使用简洁的英文缩写(全小写且不超过20字符)  
        * 举例：TestCase/EP3.1_20250101/can_busoff  
        * 该目录用于整个测试任务  
    * 将需求分析总结并输出为一个markdown思维导图，命名为"Mermaid.md"

2. 思维导图format如下:

    ```mermaid
    %%{init: {   
        "theme": "base",   
        "themeVariables": { "lineColor": "#555", "mainBkg": "#fff","nodeBorder": "#555", "primaryTextColor": "#000"},  
        "flowchart": { "nodeSpacing": 18, "rankSpacing": 40, "wrappingWidth": 9999}  
        }}%%  
        graph LR  
        %% 需求描述  
        DEMAND("<一句话简短精炼地描述需求>")  
    
        %% 需求结构化分解，在需求左侧  
        Mode(["模式"]) --- DEMAND  
            Mode_1(["<模式名>"]) --- Mode  
    
        Conditions(["条件"]) --- DEMAND  
            Precond(["使能条件"]) --- Conditions  
                Precond_1(["<使能条件描述>"]) --- Precond  
            Disable(["禁用条件"]) --- Conditions  
                Disable_1(["<禁用条件描述>"]) --- Disable  
            Trigger(["置位条件"])   --- Conditions  
                Trigger_1(["<置位条件描述>"]) --- Trigger  
            Recover(["恢复条件"])   --- Conditions  
                Recover_1(["<恢复条件描述>"]) --- Recover  
    
        Output(["输出信号"]) --- DEMAND  
            Output_1(["<输出信号名>"]) --- Output  
    
        %% 测试点维度分析，在需求右侧  
        DEMAND --- Single_Input([单输入分析])  
            Single_Input --- Input_1(["<信号类型，连续/布尔/枚举>"])  
                Input_1 --- Input_Valid_EC_1(["有效等价类 | <输入信号名>=<值> | <定义，e.g. 置位条件上边界内，初始值，替代值...>"])  
                Input_1 --- Input_Invalid_EC_1(["无效等价类 | <输入信号名>=<值> | <定义>"])  
    
        DEMAND --- Multi_Input([多输入组合])  
            Multi_Input --- Mode_Name_1(["<模式名>"])  
                Mode_Name_1 --- Mode_Name_Pos_Comb_1(["正向组合 | <描述满足条件并触发功能的测试流程>"])  
                Mode_Name_1 --- Mode_Name_Neg_Comb_1(["反向组合 | <修改正向组合的一个条件，使功能被触发/不触发>"])  
    
        DEMAND --- Event_List([事件列表])  
            Event_List --- Disc_Signal_Trans(["离散信号跳变"])  
                Disc_Signal_Trans --- Disc_Signal_1(["<离散信号名>"])  
                    Disc_Signal_1 --- Input_Name_Jump_1(["<值1> -> <值2>"])  
                    Disc_Signal_1 --- Input_Name_Jump_2(["<值2> -> <值1>"])          
            Event_List --- Cont_Signal_Trans(["连续信号阈值穿越"])  
                Cont_Signal_Trans --- Cont_Signal_1(["<连续信号名>"])  
                    Cont_Signal_1 --- Input_Name_Cross_1(["<值1> -> <阈值> -> <值2>"])  
            Event_List --- Mode_Trans(["模式切换"])  
                Mode_Trans --- Mode_1_switch_2(["<模式1> -> <模式2> | <描述模式切换的条件>"])  
    
        DEMAND --- Time_Sequence([时序组合])  
            Time_Sequence --- Debounce_Prec(["时间精度边界"])  
                Debounce_Prec --- Debounce_1(["<时间特性描述> | <适用模式>"])  
                    Debounce_1 --- Debounce_1_met(["满足 | <条件> last <时间>"])  
                    Debounce_1 --- Debounce_1_not_met(["不满足 | <条件> last <时间>"])
    
            Time_Sequence --- Causal_Chain(["因果链"])  
                Causal_Chain --- Order_1(["<因果链序号> | <条件> before <条件>"])  
    
            Time_Sequence --- Interrupt(["中断"])  
                Interrupt --- Interrupt_1(["<中断序号> | <条件> interrupt <条件>"])  
    
        DEMAND --- Outflow_Issue(["流出问题"])  
            Outflow_Issue --- Outflow_1(["<流出问题描述> | <补充测试场景>"])  
    
        %% ========== 样式 ==========  
        class DEMAND center
        class Mode,Conditions,Output,Precond,Disable,Trigger,Recover,Single_Input,Multi_Input,Event_List,Time_Sequence,Disc_Signal_Trans,Cont_Signal_Trans,Mode_Trans,Debounce_Prec,Causal_Chain,Interrupt,Outflow_Issue title
        classDef center fill:#555,color:#fff,stroke:#000
        classDef title fill:#ddd,color:#000,stroke:#000  
    ```

3. 完成思维导图后询问用户是否切换模式以完成：  
    * 测试点总结、测试用例编写(Test Case Mode)  
    * 测试脚本编写和执行(Test Code Mode)  
