# It is time to loop

生成式 AI 正在从"回答问题"走向"完成任务"。当 AI 进入 Loop Engineering 阶段，真正决定生产效率的，已不只是模型能力，而是能否把领域知识嵌入一个可持续运行、自动收敛的工作闭环。

所谓 Loop Engineering，就是让 AI 跑完一个闭环：生成、执行、观察证据、再决定下一步。一旦进入 Loop，竞争的焦点就变了：Prompt 技巧贬值，Domain knowledge 升值。因为 Loop 的每个环节——什么算证据、什么算收敛、什么时候必须停下来交给人——都不是通用知识，而是行业经验。AI 能把循环跑得很快，但循环往哪里跑、在哪里刹车，只有懂行的人才能定义。

以 CPU TOP 验证为例。一个 test 跑挂了，通用 AI 看到的是一堆 log；验证工程师却知道结果至少要分成 FAIL、HANG、TB_FAIL、INFRA_FAIL 四类。Debug 也不是"多看几眼波形"，而是 Reproduce → First Divergence → Hypothesis → Root Cause Proof 这条固定路径。Coverage hole 要先判断是不可达、缺激励还是缺观测，才谈得上补 test。把这些判断规则化，写进 Loop 的状态机和 Exit Gate，AI 才能在 Feature、Regression、Coverage 三个并列的循环里自主迭代，而把 Bug triage、Waive 和 Sign-off 留给人。

因此，Loop Engineering 的核心并非让 AI 无限重试，而是由专家定义目标、边界、观测点和退出条件。一个设计良好的 Loop，可以让 AI 自动处理重复劳动，让工程师专注于架构判断与疑难问题。Domain knowledge is power——It is time to loop.

![It is time to loop|200](image/loop_running_track.jpg)
