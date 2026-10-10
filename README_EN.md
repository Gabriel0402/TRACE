# TRACE

## Formal Temporal-Semantic Augmentation for Vision-Language-Action Foundation Models

[中文](README.md) | **English**

TRACE studies how temporal requirements in language instructions can be translated into executable formal specifications and used to guide action selection through explicit temporal states. This repository provides the project description, download links for the accompanying structured benchmark, and usage guides. The implementation will be released separately.

**Code release soon.** Dataset download links and bilingual usage guides are provided in the Dataset Downloads section below.

## Motivation

Manipulation tasks require more than selecting the correct objects. Execution must also respect ordering, choice-consistency, and response constraints. For example, an instruction may require placing the highest book on a shelf before placing the rightmost book. Reversing this order violates the task even if both books eventually reach the shelf.

TRACE represents such requirements in linear temporal logic over finite traces (LTLf). An automaton tracks completed actions and remaining obligations, allowing execution history to directly inform the next action choice.

## Method Overview

The framework consists of two modules.

### Temporal Specification Induction

A compiler-first mechanism extracts temporal structures and semantic roles from the instruction. Scene-object and candidate-action descriptions ground these roles into an executable LTLf specification. A learned model provides completion when rule matching fails. The compiler path supplies the primary specification-construction capability in the current implementation.

### Automaton-Guided Control

The specification is converted into a deterministic finite automaton whose state is updated after each action event. The controller excludes candidate actions that leave no satisfying continuation, then adjusts the rankings of the remaining actions using distance to an accepting state to support completion of outstanding goals.

The current experiments use a primitive-action scorer built on frozen OpenVLA representations. Control settings share the feature extractor and scoring head, with the initial image, task instruction, and textual action history as inputs. Temporal guidance changes candidate selection without updating the shared scorer. Evaluation uses a discrete primitive-action space.

## Structured Benchmark

The benchmark covers ten temporal structure types, including ordering, container-choice consistency, triggered response, and branching constraints. Instances separate concrete manipulation content from temporal structure and include an initial scene, an instruction, candidate-action semantics, a reference specification, and positive and negative traces for specification evaluation.

Evaluation examines specification quality and downstream execution separately, focusing on whether generated specifications capture task requirements and whether executed trajectories satisfy the reference specifications.

## Dataset Downloads

Baidu Netdisk and Google Drive provide the same dataset version. Download `TRACE_dataset_v1.zip` from the shared `TRACE_data_upload` folder.

| Provider | Link | Notes |
| --- | --- | --- |
| Baidu Netdisk | [Download dataset](https://pan.baidu.com/s/1Uf8Ezsz73XAHQQgsFta0yg) | Access code: `iqbh` |
| Google Drive | [Download dataset](https://drive.google.com/drive/folders/1Plep1y8aowrS6KJF7VS5Jc9Eohc6jvhd?usp=sharing) | Shared folder |

Read the [English Dataset Guide](docs/data/README_EN.md) or [中文数据说明](docs/data/README.md) for extraction instructions, data splits, file descriptions, and input conventions. Use the [SHA-256 checksum file](docs/data/SHA256SUMS.txt) to verify the downloaded archive.

The dataset contains 500 instances, with 350 training, 62 validation, and 70 test instances. The remaining 18 instances are included in the archive but are not assigned to these splits. See [DATASET_STATS.json](docs/data/DATASET_STATS.json) for the full counts.

## Code Release

**Code release soon.**

The implementation, experiment configurations, and reproduction instructions will accompany the code release. This version does not contain training or evaluation code.

## Data Sources and Licensing

Original TRACE materials are released under the [MIT License](LICENSE). Third-party scene images, assets, and other upstream materials remain subject to their respective licenses. See the [source and licensing notice](docs/data/THIRD_PARTY_NOTICE.md).
