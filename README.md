# <img src="results/logo.png" alt="icon" width="24" height="40" style="vertical-align: middle;"> Hanfu-Bench: A Multimodal Benchmark on Cross-Temporal Cultural Understanding and Transcreation

<div align="center">
<a href="https://arxiv.org/pdf/2506.01565v1" target="_blank"><img src=https://img.shields.io/badge/arXiv-b5212f.svg?logo=arxiv></a>
<a href="https://huggingface.co/datasets/lizhou21/Hanfu-Bench" target="_blank"><img src=https://img.shields.io/badge/%F0%9F%A4%97%20HuggingFace%20Datasets-27b3b4.svg></a>
</div>

📖[Learn more about Hanfu](https://github.com/hlt-cuhksz/TemporalCulture/blob/main/results/hanfu-info.pdf)

## Overview
We introduce Hanfu-Bench, a novel, expert-curated multimodal dataset. Hanfu, a traditional garment spanning ancient Chinese dynasties, serves as a representative cultural heritage that reflects the profound temporal aspects of Chinese culture while remaining highly popular in Chinese contemporary society. Hanfu-Bench comprises two core tasks: cultural visual understanding and cultural image transcreation. The former task examines temporal-cultural feature recognition based on single- or multi-image inputs through multiple-choice visual question answering, while the latter focuses on transforming traditional attire into modern designs through cultural element inheritance and modern context adaptation. Our evaluation shows that closed VLMs perform comparably to non-experts on visual cutural understanding but fall short by 10% to human experts, while open VLMs lags further behind non-experts. For the transcreation task, multi-faceted human evaluation indicates that the best-performing model achieves a success rate of only 42%. Our benchmark provides an essential testbed, revealing significant challenges in this new direction of temporal cultural understanding and creative adaptation.
<p align="center">
  <img src="results/framework.png"/>
</p>

## Getting Started
### Installation
```sh
pip install -r requirements.txt
```

### Examples
meta-info.json
```
Dataset({
  "1000": {
    "source": "TaoBao",
    "img_list": [
      "num1000_img1.jpg"
    ],
    "gender": "female",
    "type": "传统汉服形制",
    "period": "唐朝",
    "xiu": "unsure",
    "jin": "对襟",
    "ling": "直领",
    "bottoms": "unsure",
    "outerwear": "披帛"
  },
})
```

merged-sivqa.json
```
Dataset([
  {
    "question_id": "single_0",
    "question_type": "gender",
    "cloth_id": "1000",
    "img_list": [
      "num1000_img1.jpg"
    ],
    "base_question": "图片中的服饰通常适合什么性别？",
    "base_question_en": "Which gender is the clothing in this image typically suitable for, among the following options?",
    "choices": "A.男; B.女",
    "choices_en": "A.Male; B.Female",
    "answer": "B"
  },
])
```

merged-mivqa.json
```
Dataset([
  {
    "question_meta": {
      "answer": {
        "image": "num1080_img4.jpg",
        "meta": {
          "gender": "female",
          "type": "汉元素服饰",
          "style": "unsure",
          "period": "unsure",
          "xiu": "unsure",
          "jin": "对襟",
          "ling": "圆领",
          "bottoms": "unsure",
          "outerwear": ""
        }
      },
      "candidates": [
        {
          "image": "num166_img10.jpg",
          "meta": {
            "gender": "female",
            "type": "汉服改良版",
            "style": "TwoPiece",
            "period": "unsure",
            "xiu": "unsure",
            "jin": "对襟",
            "ling": "unsure",
            "bottoms": "马面裙",
            "outerwear": ""
          }
        },
        {
          "image": "num1169_img5.jpg",
          "meta": {
            "gender": "male",
            "type": "传统汉服形制",
            "style": "unsure",
            "period": "unsure",
            "xiu": "unsure",
            "jin": "大襟",
            "ling": "unsure",
            "bottoms": "裤",
            "outerwear": ""
          }
        },
        {
          "image": "num1310_img1.jpg",
          "meta": {
            "gender": "male",
            "type": "传统汉服形制",
            "style": "TwoPiece",
            "period": "unsure",
            "xiu": "unsure",
            "jin": "unsure",
            "ling": "unsure",
            "bottoms": "unsure",
            "outerwear": "甲胄"
          }
        }
      ],
      "answer_img": "num1080_img4.jpg",
      "candidate_imgs": [
        "num166_img10.jpg",
        "num1169_img5.jpg",
        "num1310_img1.jpg"
      ],
      "question": "以下图片中的服饰属于汉元素服饰的是？",
      "question_type": "type",
      "question_formular": "type_t1"
    },
    "question": "以下图片中的服饰属于汉元素服饰的是？",
    "options": [
      "num1080_img4.jpg",
      "num166_img10.jpg",
      "num1169_img5.jpg",
      "num1310_img1.jpg"
    ],
    "answer_idx": 0,
    "qid": "mivqa_0",
    "question_id": "multi_0",
    "question_type": "type"
  },
])
```


### Task 1:  Cultural visual understanding
single-image VQA:
```
python3 src/svqa/open_vlm_svqa.py --model_name Qwen2.5-VL-7B-Instruct --instruction svqa_1
```

multi-image VQA:
```
python src/mvqa/open_vlm_mvqa.py --model_name MiniCPM-V-2_6 --instruction mvqa_1 mvqa_2 mvqa_3 mvqa_4 mvqa_5
```


### Task 2: Cultural Image Transcreation
Step 1: Generate and Modernize Descriptions
Generate detailed descriptions for Hanfu images and transform them into modern fashion concepts:
```
python step1_generate_captions_gpt4o.py --config config.yaml --data_dir ./input_images --output ./captions.json
```
Parameters:
--config: Path to configuration file
--data_dir: Directory containing Hanfu images
--output: Output JSON file for descriptions


Step 2: Generate Modern Fashion Images
Choose one of the following models:

Option A: Stable Diffusion base
```
python stable_diffusion_img2img.py --config config.yaml --captions ./captions.json --output ./output_sd
```
Option B: Stable Diffusion XL 
```
python sdxl_img2img.py --config config.yaml --captions ./captions.json --output ./output_sdxl
```
Option C: InstructPix2Pix 
```
python instruct_pix2pix_img2img.py --config config.yaml --captions ./captions.json --output ./output_instruct
```
Common Parameters:
--captions: JSON file with image descriptions
--output: Output directory for generated images


## Citation
If you find Hanfu-Bench helpful, please consider citing our [paper](https://arxiv.org/pdf/2506.01565v1).
```bibtex
@inproceedings{zhou-etal-2025-hanfu,
    title = "Hanfu-Bench: A Multimodal Benchmark on Cross-Temporal Cultural Understanding and Transcreation",
    author = "Zhou, Li  and
      Yu, Lutong  and
      Xie, Dongchu  and
      Cheng, Shaohuan  and
      Li, Wenyan  and
      Li, Haizhou",
    booktitle = "Proceedings of the 2025 Conference on Empirical Methods in Natural Language Processing",
    month = nov,
    year = "2025",
    address = "Suzhou, China",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2025.emnlp-main.1251/",
    pages = "24627--24649",
    ISBN = "979-8-89176-332-6",
}
```
