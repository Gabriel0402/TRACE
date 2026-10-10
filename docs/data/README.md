# TRACE 数据发布包

[返回项目主页](../../README.md) | [English](README_EN.md)

本数据包对应论文《视觉语言动作大模型的形式化时序语义增强》，用于规约归纳与离散原语动作执行评测。它保留当前论文使用的数据快照，不包含代码、模型权重、实验输出或论文文件。

## 下载与解压

从[项目主页的下载入口](../../README.md#数据下载)获取 `TRACE_dataset_v1.zip`，并下载 [SHA256SUMS.txt](SHA256SUMS.txt)，将二者放在同一个文件夹中。百度网盘提取码为 `iqbh`。

先按照本文末尾的命令校验压缩包，再使用系统解压工具或 `unzip TRACE_dataset_v1.zip` 解压。无需安装 OpenVLA 或启动仿真环境即可查看图像、指令和 JSON 标注。

解压后得到 `TRACE_dataset_v1/`，其中 `dataset/` 是数据根目录。图片已保存为实际文件，不依赖作者电脑上的软链接。`splits.json` 中的路径相对于 `dataset/`，读取时需将数据根目录与该路径拼接。

```text
TRACE_dataset_v1/
  README.md
  README_EN.md
  DATASET_STATS.json
  MANIFEST.json
  THIRD_PARTY_NOTICE.md
  UPSTREAM_REPOSITORY_LICENSE.txt
  dataset/
    splits.json
    ordered_delivery/
      example0/
        input/
        output/
      ...
    ...
```

## 数据版本与划分

当前快照为 `commit-explicit-first-v1`，与论文的 `trigger_grounding_v1` 实验使用的数据一致。容器选择一致性指令已明确首个放置对象；后续触发动词接地修复属于方法实现变更，不改变此数据快照。

共有 **500 个任务实例、10 种时序结构类型**，每种类型 50 个实例。当前划分为训练集 350 个、验证集 62 个、测试集 70 个，另有 18 个实例未纳入这三份划分，但保留在数据包中。未使用实例清单见 [DATASET_STATS.json](DATASET_STATS.json)，请勿自行将其补入论文的验证集或测试集。

| 目录标识 | 时序结构类型 | 总数 | 训练 | 验证 | 测试 | 未纳入划分 |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| `ordered_delivery` | 顺序放置约束 | 50 | 35 | 6 | 7 | 2 |
| `commit_container` | 容器选择一致性约束 | 50 | 35 | 6 | 7 | 2 |
| `within_deadline` | 有界响应约束 | 50 | 35 | 6 | 7 | 2 |
| `avoid_unsafe_contact` | 禁止接触约束 | 50 | 35 | 6 | 7 | 2 |
| `visit_distinct` | 不重复访问约束 | 50 | 35 | 8 | 7 | 0 |
| `maintain_after_activation` | 触发后条件保持约束 | 50 | 35 | 6 | 7 | 2 |
| `disjunctive_strategy` | 互斥分支约束 | 50 | 35 | 6 | 7 | 2 |
| `repeated_collection` | 多对象收集约束 | 50 | 35 | 6 | 7 | 2 |
| `branch_after_selection` | 选择后分支约束 | 50 | 35 | 6 | 7 | 2 |
| `compound_non_markov` | 复合时序约束 | 50 | 35 | 6 | 7 | 2 |

目录名和 JSON 中的 `family` 等历史字段保持不变，以兼容已有读取程序；这些字段表示时序结构类型。实例以 `类型/example编号` 唯一标识，不同类型中的 `example0` 不是同一个实例。多个实例可以共享场景图像，500 个任务实例不等于 500 张独立场景图像。

## 实例文件说明

| 文件 | 含义 |
| --- | --- |
| `input/input.png` | 初始场景图像 |
| `input/instruction.txt` | 当前实例使用的任务指令，保留原始英文内容 |
| `input/candidate_actions.json` | 候选原语动作，含动作名称、对象参数、编号及签名 |
| `input/object_index*.json`、`object_aliases.json` | 对象索引、位置或可见性信息，以及不同粒度的对象描述 |
| `input/input_labeled.png` | 带标记的辅助场景图，不应替代无标记图像作为默认视觉输入 |
| `input/instruction_variants.json` | 指令变体与历史生成元数据；实际划分以根目录 `splits.json` 为准，实际指令以 `instruction.txt` 为准 |
| `input/scene_source.json` | 原始场景的来源标识 |
| `input/episode_config.json` | 场景配置记录，可能引用本包未包含的仿真资产 |
| `output/ap_catalog.json` | 动作与原子命题别名之间的对应关系 |
| `output/ap_text_catalog.json` | 带动作语义描述的命题目录 |
| `output/ltlf_formula.txt`、`ltlf_formula.json` | 当前可执行参考 LTLf 公式及其语法树、命题和算子信息 |
| `output/automaton.json` | 参考规约的状态、转移、初始状态及接受和拒绝状态 |
| `output/trace_suite.json` | 规约检验轨迹，包含动作编号、命题事件序列和接受标签 |
| `output/global_spec.json`、`source_spec.json` | 结构化生成记录与来源规约；评价以 `ltlf_formula.*` 和对应自动机为准 |

`p00` 等命题别名只在各自实例内有效，不能跨实例直接比较。同一别名对应的动作应查询该实例的 `ap_catalog.json`。本基准每步记录一个离散动作事件，轨迹文件以单元素命题列表表示该事件。

## 使用约定

规约归纳的公开视觉语言输入为初始图像与任务指令。对象索引和候选动作语义支持场景接地与执行；`output/` 中的参考公式、自动机和轨迹接受标签用于监督或评价，不应作为测试时待预测规约的输入。文件所在目录不自动决定它能否作为模型输入，比较实验应明确各方法使用的字段。

该数据包是离散时序规约评测资源，不包含连续机器人控制轨迹、完整仿真网格或原生 OpenVLA 动作生成评测。读取本包不需要模型权重；运行模型及复现实验仍需后续发布的代码与相应依赖。

## 完整性与发布状态

`MANIFEST.json` 记录每个数据文件的 SHA-256 和大小，并标记发布时改为相对路径的划分文件。除 `splits.json` 的路径表示外，实例图像、指令与标注均保持原样。`DATASET_STATS.json` 记录实际统计及整理时的结构核对结果。

在存放压缩包和校验文件的目录中，macOS 用户运行 `shasum -a 256 -c SHA256SUMS.txt`，Linux 用户运行 `sha256sum -c SHA256SUMS.txt`。输出 `TRACE_dataset_v1.zip: OK` 表示校验通过。

**Code release soon.** 数据下载地址见项目主页，训练和评测代码尚未发布。上游数据及资产的许可说明见 [THIRD_PARTY_NOTICE.md](THIRD_PARTY_NOTICE.md)，使用或再分发时应确认相应权限。
