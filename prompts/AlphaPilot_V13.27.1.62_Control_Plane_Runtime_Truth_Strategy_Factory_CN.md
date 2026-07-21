# AlphaPilot V13.27.1.62 - Control Plane Runtime Truth and Strategy Factory

## 目标

在不重启、不热修改当前 OKX Demo Runtime 的前提下，完成下一版控制平面：

- 策略、Demo、实盘页面只展示真实运行态和真实账本投影；
- 策略参数通过不可变、可哈希、可审计的版本对象修改；
- 策略工厂可以后台研究、归档失败和生成批准请求，但不能强制通过；
- 页面保持简洁，问题才突出提示，Hash、Gate 和血缘默认折叠；
- 当前 Demo、Live、Withdraw 的安全边界不被改变。

## 不可变安全边界

1. Raw API Key 不写入文件、数据库、日志或页面。
2. Withdraw 始终不存在或关闭。
3. Demo 与 Live 使用独立环境、独立批准和独立进程 ARM。
4. Release、Risk Overlay、Exit Policy、Model Policy 和策略参数不得原地修改。
5. 策略工厂不得批准自己、ARM、创建订单或绕过研究门槛。
6. 缺失账户数据必须显示为不可用，不能伪装成数值零。
7. 当前运行中的 Demo 继续使用原冻结版本，V62 只在隔离工作树实现和验收。

## 实现范围

### 运行态真实投影

统一读取并投影：

- Unified Auto Execution Runtime；
- Demo / Live 订单与执行账本；
- 脱敏账户快照、权益、持仓、已实现与浮动盈亏；
- 当前不可变 Release、RiskProfile 和 StrategyExecutionPolicy；
- 策略工厂 Campaign、结果、归档状态和批准请求。

### 策略参数版本

支持资金分配、单笔名义金额、单笔风险、杠杆、逐仓模式、并发持仓、
单策略币种上限、扫描范围、流动性和深度门槛、延迟、冷却、手续费、
滑点、止损与退出方式。首次配置通过安全 Bootstrap 创建不可变草稿；
敏感字段会被拒绝，任何风险提升或执行语义变化仍需新的精确批准路径。

### 极简控制台

- 策略：生成或组合策略、真实后台进度、结果状态、失败归档和批准请求。
- Demo：连接、权益、盈亏、运行策略、扫描漏斗、持仓、订单和异常。
- 实盘：与 Demo 复用读模型和组件，但不复用批准、风险或执行权限。
- 所有页面提供一次性问题提醒，详情默认折叠。

## 验收

- Console 和 Quant 全量测试通过；
- Python compileall、前端 JavaScript 语法、安全扫描和 `git diff --check` 通过；
- 桌面与 390px 手机截图无横向溢出或浏览器错误；
- 当前 Demo PID、Release、ARM 和运行状态在实施前后保持不变；
- Live 未 ARM，Withdraw 关闭，无新增订单或持仓；
- Console、Quant、Docs 分别 Commit、Tag、Push，并核对远端 SHA。

## 切换规则

V62 验收完成不等于自动部署。只有当前 Demo 达到零未知订单、零未对账部分
成交、零持仓的安全检查点，才允许人工安排切换。切换会清除进程内凭据和
ARM，需要重新输入 process-only Demo 凭据并对精确 Release 再次 ARM；
未改变 Hash 的既有 Release 批准不需要重复创建。
