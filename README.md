<img width="5723" height="2564" alt="framework_final" src="https://github.com/user-attachments/assets/9387f68e-d25b-40ef-8b79-2e799fd9d298" /># Rhythm-Aware Dysarthric Wake-Up Word Spotting (RAWS)

This repository contains the official implementation of our Rhythm-Aware Dysarthric Wake-up Word Spotting (RAWS) system.

> 🚧 **Repository Status**  
> This repository is currently under active preparation and will be fully released upon acceptance of the paper.  
>  
> We have already made available:  
> - LLM-based rhythm annotations and prompts:  
>   https://github.com/greeeenmouth/MDSC-Rhythm-Annotations  
>  
> The full implementation, including training scripts, configuration files, and the best-performing trained model, will be released to support reproducibility.

---

## 1. Overview

<!-- TODO: Insert the overall architecture figure (e.g., Fig. 3 in the paper) here -->


The proposed RAWS framework consists of three main components:
- **Temporal Prosody Structure Encoder (TPSE)** for rhythm feature modeling
- **LLM-Guided Auxiliary Learning (LLM-GAL)** for rhythm supervision
- **Progressive Adapter-Based Domain Alignment (PADA)** for stable adaptation

## 2. Repository Structure

```text
RAWS/
├── docs/                         # Documentation files
├── examples/
│   └── lrd/
│       └── s0/                   # Main experimental directory
│           ├── best_model/        # Saved best model checkpoints
│           ├── conf/              # Configuration files
│           ├── data_control/      # Data lists for non-dysarthric speech
│           ├── data_uncontrol/    # Data lists for dysarthric speech
│           ├── dict/              # Keyword dictionary and label files
│           ├── LLM-based_annotation/  # LLM-based rhythm annotation and prompt file
│           ├── path.sh            # Environment path configuration
│           ├── run_control.sh     # Script for non-dysarthric experiments
│           ├── run_uncontrol.sh   # Script for dysarthric experiments
│           ├── rhythm_analysis/   # Rhythm feature analysis scripts
│           └── tools/             # Local utility scripts
├── wekws/                         # Core WEKWS-based toolkit
│   ├── bin/                       # Training, inference, and runtime scripts
│   ├── dataset/                   # Dataset loading and feature processing
│   ├── model/                     # Model architectures
│   └── utils/                     # Utility functions
├── CPPLINT.cfg
├── LICENSE
├── README_WEKWS.md                # Original WeKWS README
├── README.md                      # Project README
└── requirements.txt               # Python dependencies
```

## 3. Environment Setup

### 3.1 Create Conda Environment

```bash
conda create -n raws python=3.9
conda activate raws
```

### 3.2 Install Dependencies
```bash
conda install -y pytorch==2.4.1 torchvision==0.19.1 torchaudio==2.4.1 pytorch-cuda=12.1 -c pytorch -c nvidia
pip install -r requirements.txt
```

## 4. Data Download
### 4.1 Download Dataset

Please download the dataset from:

```text
https://www.aishelltech.com/AISHELL_6B
```

### 4.2 Data Format

The data lists have already been prepared under `data_control/` and `data_uncontrol/`.

`data.list` follows a Kaldi-style JSON-line format. Each line corresponds to one utterance:

```json
{"key": "DM0012_0015", "txt": "小爱同学", "wav": "/path/to/your/audio/DM0012_0015.wav", "duration": 1.52}
```

`data_rhy.list` uses the same format as `data.list`, with an additional `score` field:

```json
{"key": "DM0012_0015", "txt": "小爱同学", "wav": "/path/to/your/audio/DM0012_0015.wav", "duration": 1.52, "score": 2}
```

For models without rhythm supervision, use `data.list`. For models with the auxiliary rhythm-supervision branch, use `data_rhy.list`. Users only need to replace the value of the `wav` field with the actual path to their local audio files.

## Training
### Pre-train with non-dysarthria data
```bash
cd examples/lrd/s0
bash run_control.sh --stage 1 --stop_stage 1
```

### Train RAWS model with dysarthria data
```bash
bash run_uncontrol.sh --stage 1 --stop_stage 1
```

## Evaluation
To evaluate a trained model, run:

```bash
bash run_uncontrol.sh --stage 2 --stop_stage 2
```

Alternatively, you can directly evaluate the best model reported in our paper using `run_test.sh`. The pretrained checkpoint is provided at:

```text
best_model/avg_30.pt
```

Run the following command:

```bash
bash run_test.sh
```


