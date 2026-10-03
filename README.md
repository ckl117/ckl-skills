# ckl-skills

`ckl-skills` 是一组面向 LLM 推理和 CUDA kernel 优化的可复用知识、提示词与工作流。仓库的目标是把“问题分析、资料检索、候选实现、正确性验证、性能 profiling 和迭代决策”组织成一套适合 coding agent 执行的流程，减少重复探索，提高优化效率和结果的可复现性。

当前仓库通过 `skills/kda` 引入 [Kernel Design Agents (KDA)](https://github.com/NVlabs/kda)。KDA 提供通用的 agent 工作流；其中包含 CUDA kernel 优化知识库、Nsight Compute 分析技能和可直接复用的 prompt 模板。

## 仓库目录

```text
ckl-skills/
├── README.md                 # 仓库总览、目录说明和使用入口
├── .gitmodules               # 顶层 submodule 配置
└── skills/
    └── kda/                  # Kernel Design Agents，顶层 submodule
        ├── README.md         # KDA 功能、安装方式和工作流说明
        ├── CLAUDE.md         # 面向 agent 的仓库使用约束
        ├── docs/
        │   └── agent-flow.md # 从任务定义到候选晋级的最小闭环
        ├── prompts/
        │   ├── README.md     # prompt 模板使用说明
        │   └── basic-flow.md # 新任务的通用起始 prompt
        ├── skills/
        │   ├── KernelWiki/   # CUDA kernel 优化知识库，嵌套 submodule
        │   └── ncu-report-skill/
        │                       # Nsight Compute profiling 与报告分析技能，
        │                       # 嵌套 submodule
        ├── third_party_licenses/
        │                           # 第三方组件许可证文本
        ├── THIRD_PARTY_NOTICES.md   # 第三方内容与许可证说明
        └── CONTRIBUTING.md          # KDA 子项目贡献说明
```

### 各目录的作用

| 路径 | 作用 |
| --- | --- |
| `skills/kda/prompts/` | 存放通用任务模板。`basic-flow.md` 用于启动一次研究、实现、验证和迭代流程。 |
| `skills/kda/docs/` | 说明 agent 工作流、任务契约、证据记录和候选方案晋级规则。 |
| `skills/kda/skills/KernelWiki/` | 面向 Hopper 和 Blackwell 的 CUDA kernel 优化知识库，覆盖硬件特性、优化技术、kernel 案例、代码片段、上游 PR 和交叉索引。 |
| `skills/kda/skills/ncu-report-skill/` | 基于 Nsight Compute 的 profiling 技能，支持独立 harness、报告采集、关键指标提取、stall hotspot 分析和证据化优化报告。 |
| `skills/kda/third_party_licenses/` | 随 KDA 分发的第三方组件许可证文件。 |

## 推荐使用方式

这个仓库主要提供参考资料、agent skill 和工作流。具体 kernel、模型或推理任务应在独立的实现工作区中完成，不要把任务私有代码、数据集、benchmark 日志和生成结果直接放进 KDA 子项目。

1. 初始化全部 submodule：

   ```bash
   git submodule update --init --recursive
   ```

2. 阅读通用工作流和起始 prompt：

   ```text
   skills/kda/docs/agent-flow.md
   skills/kda/prompts/basic-flow.md
   ```

3. 对需要 CUDA 优化的任务，按需使用：

   - `KernelWiki`：查询硬件、指令、内存访问、warp specialization、TMA、MMA、量化 GEMM 等背景知识和案例。
   - `ncu-report-skill`：构建 profiling harness，采集 Nsight Compute 报告，并根据指标定位瓶颈。

4. 在独立任务工作区中记录过程和证据，例如：

   ```text
   task-workspace/
   ├── docs/
   │   ├── draft.md
   │   └── plan.md
   ├── runs/
   ├── outputs/
   ├── profile/
   ├── benchmark.csv
   └── candidates.jsonl
   ```

   这样可以保留每个候选实现的父子关系、正确性结果、性能数据、profiling 证据和最终选择原因，便于后续复盘和继续优化。

## Submodule 说明

根仓库只维护 submodule 的来源和 gitlink，不在本仓库中修改 submodule 内部文件。当前层级如下：

| 路径 | 来源 | 当前固定提交 |
| --- | --- | --- |
| `skills/kda` | [NVlabs/kda](https://github.com/NVlabs/kda) | `ef6ce617693ef0782b3ecb9f37e39bbf10226a90` |
| `skills/kda/skills/KernelWiki` | [mit-han-lab/KernelWiki](https://github.com/mit-han-lab/KernelWiki) | `76d27b56f804e7e7295d4c570e1e5d7eef4b0a75` |
| `skills/kda/skills/ncu-report-skill` | [mit-han-lab/ncu-report-skill](https://github.com/mit-han-lab/ncu-report-skill) | `d1887948c7d53690cfe6605f59c1329b8a1c6bb5` |

如需升级依赖，只更新对应的 submodule commit，并在根仓库提交 gitlink 变化；不要把 submodule 的内容复制到根仓库，也不要直接修改其内部文件。

## 相关文档

- [KDA README](skills/kda/README.md)：完整的 KDA 介绍、安装方式和最小工作流。
- [Agent Flow](skills/kda/docs/agent-flow.md)：agent 驱动的任务闭环和证据记录方式。
- [Prompt Templates](skills/kda/prompts/README.md)：通用 prompt 模板说明。
- [KernelWiki README](skills/kda/skills/KernelWiki/README.md)：CUDA kernel 知识库的查询工具和目录结构。
- [ncu-report-skill README](skills/kda/skills/ncu-report-skill/README.md)：Nsight Compute profiling 技能的安装和使用说明。
