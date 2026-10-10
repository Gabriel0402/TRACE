# TRACE Dataset Release

[Project Home](../../README_EN.md) | [中文](README.md)

This package accompanies *Formal Temporal-Semantic Augmentation for Vision-Language-Action Foundation Models*. It contains the dataset snapshot used for specification induction and discrete primitive-action evaluation, without code, model weights, experiment outputs, or manuscript files.

## Download and Extraction

Download `TRACE_dataset_v1.zip` from the [project download links](../../README_EN.md#dataset-downloads) and obtain [SHA256SUMS.txt](SHA256SUMS.txt). Place both files in the same directory. The Baidu Netdisk access code is `iqbh`.

Verify the archive using the command at the end of this guide, then extract it with your system's archive utility or `unzip TRACE_dataset_v1.zip`. Viewing images, instructions, and JSON annotations requires neither OpenVLA nor a running simulator.

Extraction produces `TRACE_dataset_v1/`. Its `dataset/` directory is the dataset root. Images are regular files rather than symbolic links. Paths in `dataset/splits.json` are relative to the dataset root and must be joined with that root when loading examples.

The extracted package also includes both READMEs, `DATASET_STATS.json`, `MANIFEST.json`, `THIRD_PARTY_NOTICE.md`, and `UPSTREAM_REPOSITORY_LICENSE.txt`.

## Version and Splits

The snapshot is `commit-explicit-first-v1`, matching the data used in the paper's `trigger_grounding_v1` experiments. Container-choice consistency instructions explicitly identify the first object to place. The subsequent trigger-verb grounding fix changes the method implementation, not this dataset snapshot.

There are **500 task instances across ten temporal structure types**, with 50 instances per type. The retained splits contain 350 training, 62 validation, and 70 test instances. Another 18 instances remain in the package but are not included in these splits. Their identifiers are listed in [DATASET_STATS.json](DATASET_STATS.json); do not silently add them to the paper's validation or test sets.

| Directory identifier | Temporal structure type | Total | Train | Validation | Test | Unassigned |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| `ordered_delivery` | Ordered placement | 50 | 35 | 6 | 7 | 2 |
| `commit_container` | Container-choice consistency | 50 | 35 | 6 | 7 | 2 |
| `within_deadline` | Bounded response | 50 | 35 | 6 | 7 | 2 |
| `avoid_unsafe_contact` | Contact avoidance | 50 | 35 | 6 | 7 | 2 |
| `visit_distinct` | Non-repeating visits | 50 | 35 | 8 | 7 | 0 |
| `maintain_after_activation` | Condition maintenance after activation | 50 | 35 | 6 | 7 | 2 |
| `disjunctive_strategy` | Mutually exclusive branches | 50 | 35 | 6 | 7 | 2 |
| `repeated_collection` | Multi-object collection | 50 | 35 | 6 | 7 | 2 |
| `branch_after_selection` | Selection-conditioned branching | 50 | 35 | 6 | 7 | 2 |
| `compound_non_markov` | Compound temporal constraints | 50 | 35 | 6 | 7 | 2 |

Existing directory names and legacy JSON keys such as `family` are retained for loader compatibility; they identify temporal structure types. The unique instance identifier is `type/exampleN`. Instances may share scene images, so 500 task instances do not imply 500 independent scene images.

## Instance Contents

Each instance has an `input/` and an `output/` directory.

| File | Description |
| --- | --- |
| `input/input.png` | Initial scene image |
| `input/instruction.txt` | Active task instruction, preserved in its original English form |
| `input/candidate_actions.json` | Primitive-action candidates with names, object arguments, IDs, and signatures |
| `input/object_index*.json`, `object_aliases.json` | Object indices and alternative levels of object descriptions, spatial or visibility information |
| `input/input_labeled.png` | Auxiliary annotated image, not the default unannotated visual input |
| `input/instruction_variants.json` | Instruction variants and historical generation metadata; use root-level `splits.json` for splits and `instruction.txt` for the active instruction |
| `input/scene_source.json` | Source-scene identifiers |
| `input/episode_config.json` | Scene configuration; referenced simulation assets are not included |
| `output/ap_catalog.json`, `ap_text_catalog.json` | Mappings between actions and atomic propositions, with optional action descriptions |
| `output/ltlf_formula.txt`, `ltlf_formula.json` | Executable reference LTLf formula, syntax tree, atoms, and operators |
| `output/automaton.json` | Reference automaton states, transitions, and initial, accepting, and rejecting states |
| `output/trace_suite.json` | Specification-test traces with action IDs, proposition-event sequences, and acceptance labels |
| `output/global_spec.json`, `source_spec.json` | Structured generation and source-specification records; evaluation uses `ltlf_formula.*` and its automaton |

Proposition aliases such as `p00` are local to each instance. Resolve their meanings through that instance's `ap_catalog.json`. Each step represents one discrete action event; trace files encode it as a singleton proposition list.

## Usage

The public vision-language input for specification induction is the initial image and task instruction. Object indices and candidate-action semantics support grounding and execution. Reference formulas, automata, and trace acceptance labels are supervision or evaluation data, not test-time inputs to specification prediction. Folder placement alone does not determine input eligibility; comparisons should state which fields each method uses.

This is a discrete temporal-specification benchmark, not a collection of continuous robot-control trajectories or native OpenVLA action-generation evaluations. Full simulation assets are not included. Reading the dataset requires no model weights; model inference and experiment reproduction require the forthcoming code and dependencies.

## Integrity and Availability

`MANIFEST.json` records per-file SHA-256 checksums and sizes, including the split-file path conversion. Instance images, instructions, and annotations are unchanged. Only path representations in `splits.json` are made portable. `DATASET_STATS.json` reports observed counts and structural validation results.

In the directory containing the archive and checksum file, run `shasum -a 256 -c SHA256SUMS.txt` on macOS or `sha256sum -c SHA256SUMS.txt` on Linux. The output `TRACE_dataset_v1.zip: OK` confirms that the checksum matches.

**Code release soon.** Dataset download links are on the project home page; training and evaluation code is not yet released. See [THIRD_PARTY_NOTICE.md](THIRD_PARTY_NOTICE.md) for source and licensing information, and confirm applicable permissions before use or redistribution.
