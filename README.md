# PipeTGL + Flash：代码与实验证据归档

截至 2026-09-19，归档已完成的 Wikipedia 原型与状态关键路径实验。后续研究指定 LastFM，但这里没有 LastFM 实验结果。研究判断与文献笔记见 [distribution-tgn](https://github.com/wangwenqingqq/distribution-tgn)。

## 当前有效结论

- 八卡正确状态协议下，受控 GPU 延迟支持状态链限制吞吐。
- 候选相对直接融合，单轮加速约 1.123×；完整达标时间配对几何平均加速 1.026×，五种子 bootstrap 95% 区间 [0.985, 1.075]，不足以宣称稳定的额外端到端收益。
- 旧 1.18× 结果因未来状态读取已撤销，保留诊断记录，不纳入有效比较。
- 原生 baseline 指共同修正状态读取后的 PipeTGL；直接融合是在同一 PipeTGL 框架内接入融合算子，不是独立完整 FlashTGN。
- 结果限于 Wikipedia、单层、batch 600、单机八卡和禁用 NCCL P2P/IB 的既定配置。

## 阅读入口

| 内容 | 文件 |
| --- | --- |
| 最终结果和边界 | [RESULTS.md](tgn_pipeflash_causal_20260911/RESULTS.md) |
| 原环境运行方式 | [REPRODUCE.md](tgn_pipeflash_causal_20260911/REPRODUCE.md) |
| 图表 | [PDF](tgn_pipeflash_causal_20260911/analysis/causal_evidence.pdf) |
| 正式对比计划 | [final_confirmation_plan.json](tgn_pipeflash_causal_20260911/analysis/final_confirmation_plan.json) |
| 完整达标结果 | [final_confirm_p8_summary.json](tgn_pipeflash_causal_20260911/analysis/final_confirm_p8_summary.json) |
| 逐种子配对与区间 | [final_confirm_p8_pairs.json](tgn_pipeflash_causal_20260911/analysis/final_confirm_p8_pairs.json) |
| 因果诊断 | [final_mechanism_summary.json](tgn_pipeflash_causal_20260911/analysis/final_mechanism_summary.json) |
| 状态正确性资格 | [prefix_frozen_v2_qualification.json](tgn_pipeflash_causal_20260911/analysis/prefix_frozen_v2_qualification.json) |
| 文件校验和归档范围 | [ARCHIVE_MANIFEST.json](ARCHIVE_MANIFEST.json) |

## 代码入口与历史探索

最终对照入口是 tgn_pipeflash_causal_20260911/src/pipe_prefix_bench.py；
候选入口是 src/pipe_prefix_candidate_bench.py；共同状态协议是 src/prefix_snapshot.py。
正式确认重放入口是 src/replay_final.py，必须使用新运行标签。

保留两个项目原有 src/，以便追溯探索过程；不是所有脚本都属于最终有效协议。
phase*.py、confirm_p8.py 和其他早期控制脚本只作历史记录，不能不加区分地重跑或合并其结果。
有效集合以最终报告和 final_confirmation_plan.json 为准。

## 归档内容与复现范围

本仓库保留原源文件、构建补丁、轻量分析文件、15 次最终正式运行的 120 份 rank summary 和图表。
正式运行的 run.json 只保留统计必需的状态与完整进程耗时，并登记原文件哈希。
大体积逐事件 trace、数据集、模型／状态权重、运行环境和编译二进制未上传，仍由原实验目录保存。
ARCHIVE_MANIFEST.json 列出导入文件、哈希以及分析目录中主动省略的文件。

这是原环境的研究归档，**不是安装依赖后即可在任意机器直接训练的独立发行包**。
运行代码保持实测版本，包括原环境路径；运行依赖见两个项目的 REPRODUCE.md。
父实验源码出处及构建记录在 tgn_pipeflash_20260909/analysis/。
第三方依赖本体没有整体复制；PipeTGL、GNNFlow、DGL 和原 Flash artifact 的版本／文件哈希保留在来源记录中。
Flash snapshot 没有上游 git 元数据，不宣称它是官方最新提交，也不对第三方代码另行授予许可证。

不启动 GPU 训练也可以复算最终正式汇总：

    python3 tgn_pipeflash_causal_20260911/src/aggregate_causal.py --tag final_confirm_p8

该命令仅依赖 Python 标准库，读取本仓库保存的运行汇总并重写对应分析 JSON。
重新验证完整状态逐位一致仍需要未上传的原始状态 checkpoint。
