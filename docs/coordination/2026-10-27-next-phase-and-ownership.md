# XylophoneOS：下一阶段建议与开发占用确认

**日期：** 2026-10-27  
**性质：** 协作讨论稿（PROPOSED / NOT_ASSIGNED / NOT_RUN），不是已批准的开发任务、已实现能力或验收报告。  
**只读核查基线：** 上游 `Xiao-John/XylophoneOS` 的 `main` @ `a265061ee00b787287011e5939054ebe3da4e397`。  
**协作中的文档变更：** [PR #5](https://github.com/Xiao-John/XylophoneOS/pull/5)，head `0fe87d83af26e3c081ec6d4825383df29aef326b`，仍未合并。本稿不会直接改写该 PR 的规范。

## 1. 已确认事实与证据边界

- [PR #3](https://github.com/Xiao-John/XylophoneOS/pull/3) 已合并：E14/G02 的 QEMU 单全屏 XRGB8888 显示、PS/2 输入、受保护的 Display Service 有冻结证据；这不意味着真机、SMP 或 Qt 已运行。
- [PR #4](https://github.com/Xiao-John/XylophoneOS/pull/4) 已合并：熵/墙钟使用 syscall 63/64，显示后端改用 **65/66**，有组合 build `76a2e4380f64fb2e` 的 7 组**定向**客体验收；原 E14 的 19 组 G02 冻结报告对应旧编号，**没有在新组合代码上完成等价的完整 G02 重跑**。不能改绑历史报告。
- T0–T2 线程、T3-PY、Dash C1–C3、GCC/G++/Make、CPython 等具备各自受限验收；**T3-QT、QtCore G01/G03、QtGui/QPA G04 均未显示已实现证据**。
- [PR #5](https://github.com/Xiao-John/XylophoneOS/pull/5) 正在重构文档和规范，草案 [Qt 协作契约](https://github.com/Xiao-John/XylophoneOS/blob/codex/docs_constitution/docs/contracts/qt_platform.md) 与 [Qt 计划](https://github.com/Xiao-John/XylophoneOS/blob/codex/docs_constitution/docs/plans/qt.md) 明确当前未派 Qt 实现任务。该 PR 的 ABI 预留号段**不是已分配系统调用**，待维护者确认合并后才能按规则认领。
- 上游公开未关闭 Issue 为空；目前开放的 PR 是文档 PR #5。**公开没有占用记录，不等于维护者本地或其他会话没有开展工作。**

## 2. 推荐的下一步（按依赖，而非简单按功能数量排序）

| 优先级 | 建议任务 | 交付边界 / 最小验收 | 建议负责人（待确认） |
|---|---|---|---|
| P0 | **先协调归属 + 完成 PR #5 审查** | 确认当前开发分支、在途任务、Qt 版本、文件所有者；明确是先合并规范还是并行只读预研 | 上游维护者 |
| P0 | **在当前 main 上独立复验 E14/G02** | 用最新 sys65/66 和实际 SDK 重新构建，执行 G02 关键及受影响回归，保存新的 build/image/hash；不将旧 G02 回执冒充当前成果 | 可独立的 QA/显示线 |
| P1 | **E15-0 / G01：QtCore 移植前置验证** | 先冻结 Qt 6 精确 tag、QtBase/Core 最小 feature、第三方许可、宿主生成器与 target 交叉编译边界；用真实测试定位 C++17/ABI、线程/TLS、时钟、FD、内存及映射依赖，不假设必然需要完整 POSIX/mmap | Qt 移植线 |
| P1 | **E15 / G03：真实 QtCore 客体移植** | `QString`/`QByteArray`、`QObject` 与 signal/slot、`QCoreApplication`、`QTimer`、跨线程 post/wakeup、`QThread`、文件与资源读写、错误路径和退出资源回收；QEMU 客体证明，空闲不长期 yield 忙轮询 | Qt 移植线 + 必要的等待机制提供方 |
| P2（依赖明确后） | **QtCore 事件分发的最小通用等待机制** | 先提交实际消费者调用点和失败探针，明确 fd / timer / display input / 跨线程 wake 的最小组合需求；由内核/运行库维护者实现可复用机制，不增加 Qt 专属 syscall | 等待/内核线 |
| P2（可并行、非 Qt 前置） | **xypm P2-2 负例与真实客体资格** | 共享有效/畸形输入、ELF 检查、只读 verify/plan 的客体 gate；之后才考虑 P2-3 journal、P2-4 客体写事务 | 包管理线 |
| P2（按需） | **PX4-A VMA/匿名映射设计与探针** | 列出 Qt/其他实际消费者的最小 `mmap/mprotect` 需求、回退选项与权限/生命周期；先设计和失败探针，不直接开工全量 VMA/COW | 内存线 |
| 后续 | **E16/G04：QtGui/QPA 首个全屏窗口** | 以 E14 的单 surface、真实 mode/stride、dirty rect、PS/2 事件为基础，Qt `QPlatformScreen/Window/BackingStore`、字体/焦点/重绘和退出回收；不先做完整桌面/Quick | QtGui 线，待 E15 |

**推荐实际顺序：** 维护者确认占用 → E14 当前基线复验与 G01 QtBase 探针可并行 → G03 真实 QtCore → G04 首个全屏 QWidget/QtGui 窗口。若 Qt 确需新公共等待或 VMA 接口，先由实际失败探针申请最小机制，不能因为将来可能用到就创建空 syscall。

## 3. 准备工作的技术约束

1. 当前 QEMU/BIOS 单核 512MiB 环境依旧是基本验收平台；优先 CPU raster。E14 仅保证一个独占全屏 surface、XRGB8888 和原始键鼠事件，不保证多窗口、IME、vblank 或 GPU。
2. **Qt 6 精确版本尚未冻结。** 不把旧评估中示例版本当成已选版本；QtBase/Core 的真实源码、feature/许可证/SDK 版本必须锁定，宿主的 moc/rcc/uic 与在 XOS 执行的目标代码分别核验。
3. QtCore 的通用多源等待/唤醒、Qt 线程应用级 gate 和 VMA/mmap 仍是待验证缺口。区分确实缺机制、库侧可回退和仅当前配置未启用。
4. 上游 ABI 当前以源码为准：sys63/64 用于熵/墙钟，65/66 用于显示。PR #5 的 67–79（内存）、80–95（等待/信号）、96–111（显示扩展）是**拟定预留**；没有登记、合并和授权，不得生成对应源码接口。
5. 不覆盖 `main`、历史 Release、冻结报告、日常工作盘或 `localpilot/`；所有实现独立 worktree/分支/PR，各自私有 build，最终共享回归串行。成功、失败、未运行明确标注。

## 4. 给项目维护者 @Xiao-John 的明确问题

为避免在你本地已有的任务上重复投入，烦请确认：

1. **E15 QtCore（含 G01 版本/feature/许可冻结、G03 事件循环）** 是否已经由你或其他 Agent 开工？若已开工，请给出负责人、分支/commit、当前已验证范围和正在修改的文件。
2. **E14 在 PR #4 合并后的完整 G02/系统回归** 是否有人重跑？目前看到的是组合环境 7 组定向 gate。是否希望我独立补这一轮？
3. **通用多源等待/唤醒、T3-QT 和 VMA/PX4-A** 是否已经由你安排？如已占用，请说明 ABI/文件所有者；如果还未定稿，我可以先只做 QtBase 依赖探针，不碰共享 ABI。
4. **xypm P2-2 的客体负例/有效包验收** 现在是否有人负责？若 Qt 线已在推进，我可以独立领取这个相对不冲突的任务。
5. PR #5 何时作为 Qt 开发的协作基线？是否同意由我的 fork 负责 **E15-0/G01 preflight**，后续 G03 再按你确定的责任划分提交独立 PR？

请直接按“任务 → 已在做/未开始/希望我做 → 分支与文件范围”回复即可。确认之前，此文仅作协调建议，不视为抢占模块所有权。

## 5. 对照入口

- [上游当前代码](https://github.com/Xiao-John/XylophoneOS/tree/a265061ee00b787287011e5939054ebe3da4e397)
- [E14 显示服务契约](https://github.com/Xiao-John/XylophoneOS/blob/a265061ee00b787287011e5939054ebe3da4e397/docs/display_backend.md)
- [当前状态与 Qt 旧计划](https://github.com/Xiao-John/XylophoneOS/blob/a265061ee00b787287011e5939054ebe3da4e397/docs/xos_plan.md)
- [PR #5 的 Qt 平台协作契约（未合并）](https://github.com/Xiao-John/XylophoneOS/blob/codex/docs_constitution/docs/contracts/qt_platform.md)
- [PR #5 的 ABI 登记表（未合并）](https://github.com/Xiao-John/XylophoneOS/blob/codex/docs_constitution/docs/contracts/abi_registry.md)

---
*本文件为仅依据当时公开 GitHub 证据形成的建议，未启动 Qt、xypm、VMA 实现或客体测试。*
