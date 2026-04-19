## 测试环境  

1. 兼容性要求  
    * 脚本需同时兼容 Robot Framework 3.0 和 4.1 语法。  
2. 环境配置  
    | 环境 | Python 版本 | RF 版本 |
    | ------------- | -------------- | --------- |
    | VSCode 调试环境 | Python 3.10+ | RF 4.1+ |
    | HIL/SIL/PC | Python 2.7 | RF 3.0 |
  
<br>  

## 环境适配方案  

1. HIL 台架 / 个人 PC 整车测试  
    * VSCode(Python 3)  
        * BMS_Resource.txt 中的 AutomationTestLoader.py 检测到 Python 3 环境后，自动加载 AutomationTest_Py3.py  
    * RIDE(Python 2.7)  
        * BMS_Resource.txt 中的 AutomationTestLoader.py 检测到 Python 2 环境后，自动加载 AutomationTest.py  
2. SIL 台架  
    * VSCode(Python 3，当前)  
        * BMS_Resource.txt 直接引用 AutomationTest.py(Python 2/3 兼容的 SIL 版本)  
        * 通过 test_proxy_server.bat 运行代理服务，将 Python 3 的 Silver API 指令转换为 Python 2 格式(Silver 仅支持 Python 2 版本的 API)  
    * RIDE(Python 2.7)  
        * BMS_Resource.txt 直接引用 AutomationTest.py(Python 2/3 兼容的 SIL 版本)  
        * 直接调用 Python 2 版本的 Silver API 指令，无需代理转换

<br>  

## 脚本编写规范  

* **当前台架为SIL环境，请参考本地脚本**  
* **严格遵循历史脚本的简洁风格，避免过度封装和复杂化**  
* **优先使用常用关键字而非robotframework原生关键字**  
* **尽量兼容RF3.0/RF4.1+**  
* **代码即文档，避免冗余注释**  
  
1. 测试脚本的创建和归档规范：  
    * 所有测试任务在 TestCase/ 路径下创建独立目录，期待当前目录结构如下: 
        ```
        TestCase/  
        └── {测试阶段}_{日期}/{待测功能}/  
            ├── [已完成]Mermaid.md # 思维导图文档  
            ├── [已完成]TestPoint.md # 测试点文档  
            ├── [已完成]TestCase.md # 测试用例文档  
            ├── [进行中]TestCase01.robot # 测试套件(可多个)  
            └── [进行中]Resource.robot # 资源文件  
        ```
    * 测试阶段包括：Mule、EP、PPV、PP、P、SOP、OTA等，需要主动咨询用户当前测试阶段  
    * 待测功能使用简洁的英文缩写(全小写且不超过20字符)  
        * 举例：TestCase/EP3.1_20250101/can_busoff  
        * 该目录用于整个测试任务  
    * 测试套件和资源文件均要求使用.robot格式(.robot格式支持VSCode手动逐条调试,且兼容RF3.0)  
    * 测试套件命名为"TestCase.robot"，若需要创建多个测试套件，则增加序号，e.g. TestCase01.robot、TestCase02.robot...  
    * 资源文件命名为"Resource.robot"，关键字、配置量公共内容应当放入资源文件  

2. 常用关键字：  
| 关键字名 | 用途 |
| -- | -- |
| KeyOn | 上电/唤醒, SIL环境不支持唤醒 |
| Set Parameters <TX变量1=值> <TX变量1=值> | 设置<TX变量> |
| If_all_of_the_Results_are_met <时间(s)> <周期(s)> <RX变量1判断条件> <RX变量2判断条件>... | 判断<RX变量>满足, 时间/周期为数值非公式，判断条件仅能使用<=,>=,!=,=,>,<符号, 支持直接链式不等式 |
| If_all_of_the_Results_always_met <时间(s)> <周期(s)> <RX变量1判断条件> <RX变量2判断条件>... | 	判断<RX变量>持续满足 |
| If_all_of_the_Results_always_not_met <时间(s)> <周期(s)> <RX变量1判断条件> <RX变量2判断条件>... | 判断<RX变量>持续不满足 |
| Wait <时间(s)> | 等待<时间(s)> |
| ${Name}= Get Parameters <RX变量> | 获取<RX变量>, 返回值为list，需要用${Name}[0]索引取值 |
| ${Dim}= Get Length ${Parameters} | 获取变量长度, 返回值为list |
| The_Current_DTCs_are `0x<XXXXXX>` | 当前的DTC, SIL环境不支持测试DTC |
| The_History_DTCs_are `0x<XXXXXX>` | 历史的DTC, SIL环境不支持测试DTC |
 
3. 测试套件模板： 
    ```
    *** Settings ***  
    Documentation     <简短的描述需求>  
    Default Tags      <项目名, e.g. W02>      
    Resource          ../../BMS_Resource.txt    # 常用关键字库，必须添加，请核对BMS_Resource.txt的相对路径  
    Resource          Resource.robot    # 本次测试的资源文件  
    Suite Setup       Suite_Setup    # 标准写法  
    Suite Teardown    Suite_Teardown    # 标准写法  

    *** Test Cases ***  
    TC_<CaseName>_<index>  
    [Documentation]    <测试用例描述>  
    [Tags]    <标签>  
    [Setup]    Test_Setup  
    <测试脚本正文>  
    [Teardown]    Test_Teardown  
    ```

4. 资源文件模板：
    ``` 
    *** Settings ***  
    Resource          ../../BMS_Resource.txt  
    
    *** Variables ***  
    ${<定义的配置量>}    <值>    # <配置量释义>  
    
    *** Keywords ***  
    <自定义关键字名>  
    [Arguments]    ${<参数>}  
    <关键字正文>  
    [RETURN]    ${<返回值>}    # 可选, 此写法兼容RF3/RF4  
    ```
5. 控制流语法：  
    * 控制流语法应当尽量兼容RF3/RF4  
    * For循环  
        * **由于RF4+不支持RF3旧语法，因此请尽量减少循环语法的使用**  
        * 如需使用则写为新语法 
            ```
            FOR    ${i}    IN RANGE    5  
            Log    ${i}  
            END  
            ```
    * IF判断  
        * 使用关键字Run Keyword If以兼容RF3/RF4 
            ```
            Run Keyword If    ${Var}<0    Fail    测试失败  
            ```
    * 循环控制  
        * 使用Exit For Loop以兼容RF3/RF4 
            ```
            Run Keyword If    ${Var} == 2    Exit For Loop 
            ``` 
    * 关键字返回值  
        * 使用老版本[RETURN]以兼容RF3/RF4  
1.  脚本风格规范  
    * 简洁性原则  
        * 禁止：为简单操作创建自定义关键字(如 Setup_XXX、Trigger_XXX、Calc_XXX)  
        * 推荐：直接使用基础关键字组合实现功能  
        * 示例对比： 
            ```
            # 错误：过度封装  
            Setup_ClosedLoop_Preconditions    soc_val=${SOC_Low}    temp_val=${Temp_Mid}
            
            # 正确：直接设置  
            Set Parameters    SOCDsp=1    Cell_T=25  
            If All Of The Results Are Met    2    0.01    0.5<=SOCDsp<=1.5    24<=Cell_T<=26  
            ```
    * 注释规范  
        * 禁止：过度注释，使用 Comment Step1: 等步骤注释  
        * 推荐：仅在 [Documentation] 中说明测试目的，代码本身应具备自解释性  
        * 必要时使用简短的内联注释(# 注释内容)，但要避免冗余  
    * 变量使用原则  
        * 禁止：测试套件中过度使用中间变量存储简单值  
        * 推荐：创建关键字  
        * 示例对比： 
            ```
            # 错误：测试套件中直接使用变量  
            ${Initial_Power}=    Get Parameters    RESSMaxDisPwr  
            ${After_Power}=    Get Parameters    RESSMaxDisPwr  
            ${Rate}=    Calc_Rate    ${Initial_Power}[0]    ${After_Power}[0]  
            
            # 正确：使用资源文件中的关键字  
            CalcDisChgPwrRiseRate    ${CLOSED_LOOP_RISE_RATE}    1  
            
            #资源文件中的关键字  
            CalcDisChgPwrRiseRate  
            [Arguments]    ${Rate}    ${Time}  
            [Documentation]    计算放电功率上升速率  
            ${Value1}=    Get Parameters    RESSMaxDisPwr  
            wait    ${Time}  
            ${Value2}=    Get Parameters    RESSMaxDisPwr  
            ${CalcRate}=    Evaluate    (${Value2}[0]-${Value1}[0])/${Time}  
            run keyword and continue on failure    run keyword if    ${CalcRate}<${Rate}*0.85 or ${CalcRate}>${Rate}*1.15    Fail    上升速率不正确 
            ```
        
<br>  

## 脚本编写流程  

你应当创建任务清单并按照如下流程编写脚本:  
1. 确认关键字和配置量  
    * 充分理解测试用例并找出**测试中反复出现且需要连续动作或断言**的步骤，例如：  
        * 进入某种模式  
        * 设置某个条件组合  
        * 判断...保持...后置出期望值  
    * 找出测试用例中**多次使用的阈值**，例如：  
        * 使能/置出/禁用/恢复条件阈值  
        * debounce时间  
        * 查表的行/列/范围(list)  
2. 编写资源文件  
    * 在{测试任务目录}中创建资源文件"Resource.robot"，并编写本次测试需要的配置量和关键字  
    * 对资源文件进行脚本检查 python Script/validate_variables.py -d "{资源文件路径}"  
3. 规划测试套件结构  
    * 充分了解测试用例结构，规划测试套件数量和对应关系  
4. 试编写测试套件脚本和初步调试  
    * 在{测试任务目录}中创建一个测试套件"TestCase0.robot"，包含一条基本功能的测试脚本，如置位条件测试  
    * 执行脚本检查: python Script/validate_variables.py -d "{脚本路径}"  
    * 调试该测试套件，确保软件基本功能正常，同时确保基本的测试逻辑正确  
5. 编写测试套件脚本  
    * 在{测试任务目录}中创建其他测试套件"TestCase1.robot"、"TestCase2.robot"...，要求脚本顺序与测试用例一一对应(测试脚本ID应当与测试用例的index严格对应)  
    * 要求每创建一个脚本后立即执行脚本检查: python Script/validate_variables.py -d "{脚本路径}"

<br>

## 脚本执行

1. 目前人工执行脚本  
2. 后续考虑自动化执行脚本. 如所处环境未HIL/SIL台架，可以编写批处理文件以执行测试脚本  
