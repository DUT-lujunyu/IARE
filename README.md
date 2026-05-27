# IARE: Explainable Hateful Video Detection

This repository contains the dataset resources for the paper:

**Decoding Multimodal Cues: Unveiling the Implicit Meaning Behind Hateful Videos** (SIGIR2026)

The project studies **explainable hateful video detection**. Instead of only predicting whether a video is hateful or non-hateful, the task requires a model to generate an evidence-grounded rationale that explains its decision.

> **Content Warning**
>
> This repository is associated with research on hateful, offensive, and potentially harmful video content. Some annotations, transcripts, rationales, or extracted textual signals may contain profane, discriminatory, or hateful language. The materials are released only for academic research on online safety, explainable AI, and multimodal content moderation.

---

## Note

- Dataset annotations for **Ex-HateMM** and **Ex-ImpliHateVid** are released.
- The original raw videos are **not redistributed** in this repository. Please refer to the corresponding source repositories listed below.

---

## Overview

We construct two explainable hateful video detection datasets:

- **Ex-HateMM**
- **Ex-ImpliHateVid**

These datasets extend two existing peer-reviewed hateful video datasets by adding fine-grained explanatory annotations. Each sample is designed to support both binary classification and rationale generation.

The released annotations include:

- video captions;
- textual harmful elements;
- visual harmful elements;
- contextual rationales;
- SFT-style data for supervised instruction tuning;
- DPO-style preference data for reasoning enhancement.

The purpose of this repository is to support research on transparent and reliable hateful video detection systems.

---

## Source Video Data

Due to copyright and ethical considerations, this repository **does not include or redistribute the original raw videos**.

Please obtain the raw videos from the original dataset repositories and follow their access policies, licenses, and ethical requirements:

| Dataset | Source Repository |
|---|---|
| HateMM | https://github.com/hate-alert/HateMM |
| ImpliHateVid | https://github.com/videohatespeech/Implicit_Video_Hate |

This repository only releases derived annotation files and training/evaluation data formats used in our study.

---

## Repository Structure

```text
Dataset/
├── HateMM/
│   ├── HateMM_SFT_for_DPO_train.json
│   ├── HateMM_SFT_for_DPO_dev.json
│   ├── HateMM_SFT_for_DPO_test.json
│   └── HateMM_DPO.json
│
└── IHV/
    ├── IHV_SFT_for_DPO_train.json
    ├── IHV_SFT_for_DPO_dev.json
    ├── IHV_SFT_for_DPO_test.json
    └── IHV_DPO.json
```

---

## File Description

| File | Description |
|---|---|
| `*_SFT_for_DPO_train.json` | Training data for supervised instruction tuning. |
| `*_SFT_for_DPO_dev.json` | Development data for validation and model selection. |
| `*_SFT_for_DPO_test.json` | Test data for final evaluation. |
| `*_DPO.json` | Preference data for reasoning enhancement with Direct Preference Optimization. |

---

## Dataset Statistics

The two datasets are built on HateMM and ImpliHateVid and are split following the settings of the original datasets.

| Dataset | Hate | Non-Hate | Total |
|---|---:|---:|---:|
| Ex-HateMM | 419 | 651 | 1,070 |
| Ex-ImpliHateVid | 1,007 | 998 | 2,005 |


> **Note:**  
> Due to subsequent preprocessing, data cleaning, and repository reorganization, the actual dataset split in the released open-source version may slightly differ from the statistics reported in the paper. Please refer to the released repository version as the final version for reproduction and further research. These minor differences do not affect the overall experimental conclusions or the main findings reported in the paper.

---

## Data Format

### SFT Data

The SFT files follow a multimodal instruction-tuning style format. Each sample contains a video path and a conversation-style instruction-response pair.

Example:

```json
{
  "videos": "path/to/video.mp4",
  "messages": [
    {
      "role": "user",
      "content": "<video>\nDetermine whether the given video contains hate speech and provide a concise rationale."
    },
    {
      "role": "assistant",
      "content": "Prediction: hate\nRationale: ..."
    }
  ]
}
```

Depending on the training framework, the exact key names may use `messages`, `conversations`, `from`, `role`, `value`, or `content`. Please adapt the format to your local training pipeline if necessary.

### DPO Data

The DPO files contain preference pairs for reasoning enhancement.

Example:

```json
{
  "videos": "path/to/video.mp4",
  "conversations": [
    {
      "from": "human",
      "value": "<video>\nDetermine whether the given video contains hate speech and provide a concise rationale."
    }
  ],
  "chosen": {
    "from": "gpt",
    "value": "Prediction: non-hate\nRationale: ..."
  },
  "rejected": {
    "from": "gpt",
    "value": "Prediction: hate\nRationale: ..."
  }
}
```

The `chosen` response corresponds to a correct or preferred reasoning path. The `rejected` response corresponds to an incorrect, weak, or spurious reasoning path.

---

## Video Path Configuration

The released JSON files may contain video paths from our experimental environment. Before training or evaluation, please replace them with your local paths to the original videos.

Please obtain the raw videos from the corresponding source repositories and ensure that the video paths in the JSON files correctly point to your local copies.

For example, the video paths should point to directories such as:

```text
/path/to/HateMM/videos/
```

or:

```text
/path/to/Implicit_Video_Hate/videos/
```

depending on the source dataset.

---

## Model Training

This repository does not include a separate training framework. We recommend using **LLaMA-Factory** for supervised fine-tuning and preference optimization.

Please refer to the official LLaMA-Factory repository:

https://github.com/hiyouga/LLaMA-Factory

The released files can be adapted to common SFT and DPO pipelines supported by LLaMA-Factory.

### Suggested Pipeline

1. **Supervised Instruction Tuning**

   Use the `*_SFT_for_DPO_train.json` files to teach the model to generate both binary predictions and rationales.

2. **Reasoning Enhancement with DPO**

   Use the `*_DPO.json` files to optimize the model with preference pairs. This stage encourages the model to prefer logically sound and evidence-grounded rationales over incorrect or spurious reasoning paths.

3. **Evaluation**

   Evaluate classification performance on the test set and assess the quality of generated rationales.

---


## Ethical Statement

This repository is released for **research purposes only**. The dataset is intended to support studies on hateful video detection, explainable content moderation, multimodal safety, and trustworthy AI.

The data must **not** be used to promote, generate, or amplify hateful content; harass, profile, or discriminate against individuals or communities; build unlawful surveillance or discriminatory decision-making systems; train models to produce hateful or abusive content; or support commercial deployment without appropriate legal, ethical, and institutional review.

The original videos and derived annotations may contain sensitive, offensive, or hateful material. Users should handle the data carefully, limit access to trained researchers, avoid unnecessary exposure to harmful content, minimize redistribution of sensitive information, comply with the licenses and terms of the original datasets, and obtain institutional ethics approval when required.

This repository does not endorse or promote any harmful viewpoints contained in the data. The examples and annotations are provided solely for academic research on harmful content detection and explainable AI, and do not represent the views of the authors or their affiliated institutions.

The authors are not responsible for misuse of the data, annotations, or models trained using this repository. Users are solely responsible for ensuring that their use complies with relevant laws, institutional policies, dataset licenses, and ethical standards.

---


## Data Redistribution Policy

Raw videos are **not included** in this repository.

Users must obtain the original video data from the corresponding source repositories:

- HateMM: https://github.com/hate-alert/HateMM
- ImpliHateVid: https://github.com/videohatespeech/Implicit_Video_Hate


---

## License

This repository contains different types of materials. We recommend using the following separated licensing policy.

### Code

Code, scripts, and configuration files are released under the **MIT License**, unless otherwise stated.

### Dataset Annotations

Dataset annotations are released for **non-commercial academic research only** under the:

**Creative Commons Attribution-NonCommercial 4.0 International License (CC BY-NC 4.0)**

See:

https://creativecommons.org/licenses/by-nc/4.0/

### Raw Videos

Raw videos are **not included** in this repository. Their use is governed by the licenses, access policies, and terms of the original datasets and source platforms.

### Third-Party Resources

This project may rely on third-party tools, datasets, or models, including but not limited to:

- LLaMA-Factory;
- Whisper;
- PaddleOCR;
- multimodal large language models;
- the original HateMM and ImpliHateVid datasets.

Users are responsible for complying with the licenses and terms of all third-party resources.

---

## Citation

If you use this repository or the released annotations, please cite our paper:

```bibtex
@inproceedings{lu2026decoding,
  title     = {Decoding Multimodal Cues: Unveiling the Implicit Meaning Behind Hateful Videos},
  author    = {Lu, Junyu and Ji, Deyi and Liu, Liqun and Zhang, Xiaokun and Wu, Youlin and Lee, Roy Ka-Wei and Shu, Peng and Yu, Huan and Jiang, Jie and Xu, Bo and Yang, Liang and Lin, Hongfei},
  booktitle = {Proceedings of the 49th International ACM SIGIR Conference on Research and Development in Information Retrieval},
  year      = {2026}
}
```

