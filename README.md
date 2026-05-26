# Automatic Essay Scoring

Repo này phục vụ hai việc chính:

1. Chạy lại các benchmark Automated Essay Scoring trên bộ ASAP/ASAP++.
2. Chạy demo IELTS Writing Task 2 gồm chấm điểm, truy hồi bài tương tự, kiểm tra lỗi và sinh feedback.

Notebook demo chính hiện tại là:

```text
CS338_DemoToolUse/full_inference/test_9_inference_QWEN3B_traitsStacked_1_compatible.ipynb
```

Notebook này dùng Qwen đã train để dự đoán điểm IELTS theo 4 tiêu chí `TR`, `CC`, `LR`, `GRA`, sau đó dùng các tool kiểm tra/rag/feedback để tạo báo cáo đầy đủ.

## Cây thư mục

```text
.
├── ASAP/
│   ├── asap_eda.ipynb
│   └── asap_preprocessing.ipynb
├── ASAP++/
│   └── prompt*_concat.ipynb
├── benchmark_asap/
│   ├── supervised_learning/
│   │   ├── TF-IDF-SVR-Ridge.ipynb
│   │   ├── BiLSTM-Attention.ipynb
│   │   ├── Longformer-Regression.ipynb
│   │   ├── deBerta-v3-large-Regression.ipynb
│   │   ├── XHPDF_Reproduction_Colab.ipynb
│   │   └── End2End_Longformer_XHPDF_Colab.ipynb
│   └── zeroshot_learning/
│       ├── vanilla_mistral_zero_shot_baseline_p1_p4 (1).ipynb
│       ├── vanilla_mistral_zero_shot_baseline_p5_p8.ipynb
│       ├── MTS_Mistral7B_Lite_1turn.ipynb
│       ├── MTS_Mistral7B_Full_2turn_paper_split.ipynb
│       ├── LCES_Reproduction/
│       └── LCES_Reproduction_UpgradePairwise/
├── benchmark_results/
│   └── ASAP/
│       ├── Supervised_learning/
│       └── ZeroShot/
│           ├── lces/
│           └── mts/
├── CS338_DemoToolUse/
│   ├── baseline/
│   ├── score_training/
│   ├── score_inference/
│   └── full_inference/
├── dataset/
│   ├── asap/
│   ├── asap++/
│   ├── hugging_face_dataset/
│   ├── augmented_dataset/
│   ├── augement_dataset_new_collected/
│   ├── full_concat_ds/
│   └── kaggle_dataset/
└── references/
```

## Dữ liệu

### ASAP

Các file chính nằm trong `dataset/asap/`:

| File | Ý nghĩa |
| --- | --- |
| `training_set_rel3.tsv`, `training_set_rel3.xls` | Dữ liệu ASAP gốc. |
| `training_set_rel3_preprocessed.csv` | Bản đã tiền xử lý. |
| `asap_train.csv` | Split train. |
| `asap_val.csv` | Split validation. |
| `asap_test.csv` | Split test. |

Các cột quan trọng:

| Cột | Ý nghĩa |
| --- | --- |
| `essay_id` | Mã bài viết. |
| `essay_set` | Prompt ASAP, từ 1 đến 8. |
| `essay` | Nội dung bài viết. |
| `domain1_score` | Điểm gốc. |
| `domain1_score_norm` | Điểm đã chuẩn hóa về khoảng 0-1. |

### IELTS

Các file phục vụ training/inference IELTS nằm trong `dataset/`:

| Thư mục | Nội dung |
| --- | --- |
| `hugging_face_dataset/` | Dataset IELTS train/val/test từ Hugging Face. |
| `augmented_dataset/` | Dataset đã augment, có `ielts_train_aug_df.csv`. |
| `augement_dataset_new_collected/` | Bản augment/new collected khác. |
| `full_concat_ds/` | Dataset gộp đầy đủ, có `train.csv`, `val.csv`, `test.csv`. |
| `kaggle_dataset/` | Dataset IELTS từ Kaggle. |

Với demo RAG trên Colab, nên dùng `dataset/augmented_dataset/ielts_train_aug_df.csv` hoặc `dataset/full_concat_ds/train.csv`. Nếu dùng `full_concat_ds/train.csv`, hãy đổi tên thành `ielts_train_df2.csv` khi upload lên Drive để notebook tự tìm thấy.

## Benchmark ASAP

Thư mục `benchmark_asap/` chứa toàn bộ notebook chạy lại thực nghiệm trên ASAP. Kết quả đã chạy sẵn nằm trong `benchmark_results/ASAP/`.

### Supervised learning

Các notebook này train mô hình trên dữ liệu ASAP có nhãn rồi đánh giá bằng QWK, MAE, RMSE.

| Notebook | Thí nghiệm |
| --- | --- |
| `TF-IDF-SVR-Ridge.ipynb` | Baseline truyền thống: TF-IDF + Ridge/SVR. |
| `BiLSTM-Attention.ipynb` | Mô hình sequence neural: BiLSTM + attention. |
| `Longformer-Regression.ipynb` | Fine-tune `allenai/longformer-base-4096` cho bài viết dài. |
| `deBerta-v3-large-Regression.ipynb` | Fine-tune `microsoft/deberta-v3-large` cho regression. |
| `XHPDF_Reproduction_Colab.ipynb` | Reproduction kiểu discourse-aware/XHPDF: lấy embedding + feature discourse rồi train linear regression head. Đây là bản approximate nếu chưa có đúng checkpoint HPD của paper. |
| `End2End_Longformer_XHPDF_Colab.ipynb` | Bản end-to-end của nhóm: Longformer trainable + XHPDF-style discourse features + MLP/regression head. |

Kết quả supervised đang có:

| File | Method | Test QWK trung bình |
| --- | --- | --- |
| `experiment1_tfidf_svr_ridge_results.csv` | TF-IDF + Ridge | 0.6671 |
| `experiment1_tfidf_svr_ridge_results.csv` | TF-IDF + SVR | 0.6700 |
| `experiment2_bilstm_attention_results.csv` | BiLSTM-Attention Regression | 0.6703 |
| `experiment3_longformer_results.csv` | Longformer Regression | 0.7889 |
| `experiment4_deberta_v3_large_results.csv` | DeBERTa-v3-large Regression | 0.6757 |

Ghi chú: một số notebook lưu cả `test_qwk_round` và `test_qwk`. Khi báo cáo, cần nói rõ dùng rounding trực tiếp hay validation threshold optimization.

### MTS

MTS là nhóm thí nghiệm zero-shot Multi-Trait Scoring bằng Mistral 7B. Model không fine-tune trên ASAP; nó được prompt để chấm từng trait, sau đó quy đổi về điểm ASAP và tính QWK.

| Notebook | Cách chạy |
| --- | --- |
| `MTS_Mistral7B_Lite_1turn.ipynb` | MTS lite: 4 trait x 1 lượt sinh = 4 generations/essay. |
| `MTS_Mistral7B_Full_2turn_paper_split.ipynb` | Full MTS: turn 1 trích quote theo trait, turn 2 chấm điểm theo quote; 4 trait x 2 lượt = 8 generations/essay. |

Protocol của bản full:

1. Gom toàn bộ ASAP hoặc dùng các split đã chuẩn bị.
2. Sample khoảng 10% essay mỗi prompt làm test paper-style.
3. Dùng `mistralai/Mistral-7B-Instruct-v0.2`.
4. Sinh điểm trait dạng zero-shot.
5. Aggregate trait scores.
6. Outlier clipping và min-max scaling theo từng prompt.
7. Tính QWK theo prompt và QWK trung bình.

Kết quả đang có:

| File | Average QWK |
| --- | --- |
| `benchmark_results/ASAP/ZeroShot/mts/mts_lite_mistral7b_qwk_average_fixed_splits.csv` | 0.5728 |
| `benchmark_results/ASAP/ZeroShot/mts/full_mts_mistral7b_qwk_average_paper_split.csv` | 0.5304 |

Full MTS tốn tài nguyên hơn nhiều vì khoảng 1,300 essay test x 4 trait x 2 generations, tức hơn 10,000 lần generation.

### LCES

LCES là nhóm thí nghiệm transductive pairwise ranking. Thay vì bắt LLM trả điểm trực tiếp, notebook yêu cầu LLM so sánh hai bài và chọn bài tốt hơn, sau đó train RankNet từ các nhãn pairwise.

| Thư mục/notebook | Cách chạy |
| --- | --- |
| `LCES_Reproduction/LCES_ASAP8_paperlike_official_rubricsP1_P4.ipynb` | Paper-like run với official rubrics cho prompt 1-4. |
| `LCES_Reproduction/LCES_ASAP8_paperlike_official_rubricsP5_P8ipynb.ipynb` | Paper-like run với official rubrics cho prompt 5-8. |
| `LCES_Reproduction_UpgradePairwise/LCES_transductive_prompt*.ipynb` | Bản nâng cấp, tách mỗi prompt thành một notebook, dùng pairwise prompt robust hơn. |

Pipeline LCES:

1. Load `asap_train.csv`, `asap_val.csv`, `asap_test.csv` hoặc `training_set_rel3.tsv`.
2. Với từng `essay_set`, sample 10% essay.
3. Tạo `M=5000` ordered pairwise comparisons.
4. Gọi LLM hai chiều: `(essay_i, essay_j)` và `(essay_j, essay_i)` để giảm position bias.
5. Parse preference thành `essay1`, `essay2` hoặc `tie`.
6. Bỏ/đánh dấu case parse fail hoặc không nhất quán.
7. Train RankNet từ pairwise labels.
8. Map latent score về score range chính thức của prompt.
9. Tính QWK/Spearman.

Kết quả judgment đang nằm trong:

```text
benchmark_results/ASAP/ZeroShot/lces/
```

Mỗi file `p*_Mistral_7B_Instruct_v02_temp0_m5000_seed42_frac01_judgments.csv` lưu các cột như `essay_id_1`, `essay_id_2`, `forward_preference`, `reverse_preference`, `label`, `consistent`, `forward_raw_text`, `reverse_raw_text`. Đây là dữ liệu quan trọng để debug LLM pairwise judgment.

### Discourse-aware / XHPDF-style

Nhóm này kiểm tra giả thuyết rằng chấm bài không chỉ cần embedding văn bản mà còn cần feature về discourse, cohesion, lexical diversity, readability, sentence structure.

Trong `benchmark_asap/supervised_learning/` có hai bản:

1. `XHPDF_Reproduction_Colab.ipynb`: frozen embedding proxy + scaled discourse features + linear regression. Prompt 1-7 dùng sentence embedding proxy, prompt 8 dùng XLNet.
2. `End2End_Longformer_XHPDF_Colab.ipynb`: Longformer nằm trong training loop, tức backbone được fine-tune end-to-end cùng feature discourse.

Trong phần IELTS, notebook training `CS338_DemoToolUse/score_training/QWEN3B_traitsStacked_9.ipynb` cũng dùng hướng discourse-aware:

```text
prompt + essay
  -> Qwen/Qwen2.5-3B encoder
  -> shared essay representation
  -> trait-specific stack cho TR, CC, LR, GRA
  -> kết hợp handcrafted discourse features
  -> derive Overall từ trung bình 4 trait
```

## Cách chạy lại benchmark ASAP

Khuyến nghị dùng Colab GPU hoặc máy CUDA. Với Mistral 7B, nên dùng GPU nhiều VRAM hơn T4 nếu chạy full.

1. Chuẩn bị dữ liệu:

```text
dataset/asap/asap_train.csv
dataset/asap/asap_val.csv
dataset/asap/asap_test.csv
dataset/asap/training_set_rel3.tsv
```

2. Upload các file cần thiết lên Colab. Nhiều notebook mặc định tìm ở:

```text
/content/asap_train.csv
/content/asap_val.csv
/content/asap_test.csv
/content/training_set_rel3.tsv
```

3. Mở notebook trong `benchmark_asap/`.
4. Chạy cell install/import đầu tiên.
5. Kiểm tra biến đường dẫn như `TRAIN_PATH`, `VAL_PATH`, `TEST_PATH`, `OUTPUT_DIR`.
6. Smoke test trước bằng cách giới hạn prompt hoặc số essay nếu notebook có các biến như `PROMPTS_TO_SCORE`, `MAX_ESSAYS_PER_PROMPT_PER_SPLIT`.
7. Chạy full khi smoke test ổn.
8. Lưu kết quả mới vào `benchmark_results/ASAP/` với tên file mới nếu muốn so sánh nhiều cấu hình.

## Full IELTS demo bằng Qwen

### Notebook chính

```text
CS338_DemoToolUse/full_inference/test_9_inference_QWEN3B_traitsStacked_1_compatible.ipynb
```

Notebook này làm các bước sau:

1. Mount Google Drive và cài thư viện.
2. Tự nhận GPU. Nếu là A100 thì chạy full mode; nếu là L4/T4 thì chạy fast demo profile để tránh OOM.
3. Tạo các helper xử lý text, feature, discourse, grammar, lexical risk.
4. Build retrieval database từ CSV IELTS train.
5. Tự tìm model export Qwen đã train.
6. Load Qwen base model + LoRA adapter + trait stacks.
7. Load Mistral 7B để viết giải thích/feedback.
8. Định nghĩa các tool: predict scores, task mismatch, coherence risk, lexical risk, grammar risk, retrieve similar essays, generate feedback, verify/revise.
9. Dùng LangGraph để điều phối tool.
10. Mở Gradio UI để người dùng paste prompt và essay.

### Yêu cầu tối thiểu

- Google Colab GPU L4 là mức tối thiểu nên dùng.
- A100 chạy ổn hơn và có thể chạy full mode.
- T4 có thể chạy chậm hoặc dễ OOM.
- Nên bật High-RAM nếu Colab cho phép.
- Cần Google Drive để chứa model export và dataset RAG.
- Cần Hugging Face token nếu Colab báo lỗi không tải được `mistralai/Mistral-7B-Instruct-v0.3` hoặc `Qwen/Qwen2.5-3B`.

### Chuẩn bị model Qwen đã train

Model output đã train được lưu ở Google Drive:

[Qwen trained output](https://drive.google.com/drive/folders/1J4RAzt2Z0eIF8_pfcBm6YKVK0b87-VBy?usp=sharing)

Người chạy demo cần tự download/copy thư mục hoặc file zip đó vào Google Drive của mình, ví dụ:

```text
MyDrive/ielts_model_exports/ielts_lora_BEST_checkpoint_export.zip
```

hoặc giải nén sẵn thành:

```text
MyDrive/ielts_model_exports/export_lora_best_inference/
```

Thư mục export hợp lệ phải có các file/thư mục sau:

```text
export_lora_best_inference/
├── inference_config.json
├── trait_stacks_and_features.pt
├── tokenizer/
└── qwen_lora_adapter/
```

Nếu training dùng full fine-tuning thay vì LoRA, thư mục có thể chứa `qwen_full_encoder/` thay cho `qwen_lora_adapter/`.

Notebook sẽ tự tìm model ở các vị trí phổ biến như:

```text
/content/export_lora_best_inference
/content/ielts_lora_BEST_checkpoint_export
/content/drive/MyDrive/ielts_model_exports/export_lora_best_inference
/content/drive/MyDrive/ielts_model_exports/ielts_lora_BEST_checkpoint_export
/content/drive/MyDrive/export_lora_best_inference
```

Nếu chỉ có file zip, notebook sẽ tự giải nén khi zip có tên gần giống `ielts_lora_BEST_checkpoint_export.zip`.

### Chuẩn bị dataset cho RAG

Pipeline retrieval cần một CSV train có các cột IELTS như:

```text
prompt, essay, evaluation, TR, CC, LR, GRA, band
```

Lấy dataset từ máy local trong repo:

```text
C:\Users\PC\IELTS-Writing-Evals\dataset
```

Cách dễ nhất:

1. Lấy file `dataset/augmented_dataset/ielts_train_aug_df.csv`.
2. Upload vào Google Drive root:

```text
MyDrive/ielts_train_aug_df.csv
```

Hoặc:

1. Lấy file `dataset/full_concat_ds/train.csv`.
2. Đổi tên thành `ielts_train_df2.csv`.
3. Upload vào Google Drive root:

```text
MyDrive/ielts_train_df2.csv
```

Notebook tự tìm retrieval CSV ở các vị trí:

```text
/content/ielts_train_df2.csv
/content/drive/MyDrive/ielts_train_df2.csv
/content/drive/MyDrive/ielts_train_aug_df.csv
/content/ielts_train_aug_df.csv
```

Nếu để file trong thư mục con của Drive, tên file vẫn nên có cả `ielts` và `train`, ví dụ `ielts_train_aug_df.csv`, vì notebook có bước search recursive trong `MyDrive`.

### Cài requirements trên Colab

Trong Colab, chọn:

```text
Runtime -> Change runtime type -> GPU -> L4
```

Sau đó chạy các cell install trong notebook. Nếu muốn cài thủ công trong một cell đầu, dùng:

```python
!pip install -U "bitsandbytes>=0.46.1" "transformers>=4.46.0" "peft>=0.12.0" "accelerate>=0.33.0"
!pip install -U "sentence-transformers" "scikit-learn" "gradio>=4.44.0" "langgraph" "pandas" "numpy"
```

Nếu Hugging Face yêu cầu token:

```python
from huggingface_hub import login
login()
```

Không paste token vào notebook public. Nên dùng Colab Secrets hoặc nhập token khi `login()` hỏi.

Sau khi cài `bitsandbytes`, nếu Colab yêu cầu restart runtime thì restart, rồi chạy lại notebook từ đầu.

### Chạy demo từng bước cho sinh viên năm nhất

1. Mở Colab và upload notebook `test_9_inference_QWEN3B_traitsStacked_1_compatible.ipynb`.
2. Chọn GPU L4.
3. Copy model export từ link Drive ở trên vào `MyDrive/ielts_model_exports/`.
4. Copy dataset RAG vào `MyDrive/ielts_train_aug_df.csv` hoặc `MyDrive/ielts_train_df2.csv`.
5. Chạy cell mount Drive:

```python
from google.colab import drive
drive.mount("/content/drive")
```

6. Chạy các cell install requirement.
7. Chạy các cell import và config. Cell config sẽ in ra `GPU_NAME`, `FAST_DEMO`, `MAX_AGENT_STEPS`.
8. Chạy cell build retrieval. Nếu đúng, bạn sẽ thấy dòng `Using TRAIN_CSV = ...`.
9. Chạy cell tìm model. Nếu đúng, bạn sẽ thấy dòng `Using MODEL_DIR = ...`.
10. Chạy các cell load Qwen và Mistral. Bước này lâu nhất.
11. Chạy cell sanity check. Kết quả đúng là:

```text
Sanity check passed. All required tool, helper, and LangGraph symbols are defined.
```

12. Chạy cell Gradio cuối cùng.
13. Mở link public mà Gradio in ra.
14. Paste đề bài IELTS vào ô `Essay Prompt`.
15. Paste bài viết vào ô `Essay`.
16. Bấm `Run Demo`.

### Output của demo

Gradio UI trả về các phần:

| Tab/khối | Ý nghĩa |
| --- | --- |
| Summary | Tóm tắt kết quả chạy agent. |
| Scores & Task Check | Điểm `TR`, `CC`, `LR`, `GRA`, `Overall` và kiểm tra lạc đề/task mismatch. |
| Feedback | Feedback chi tiết theo tiêu chí, có strengths, limitations và gợi ý sửa. |
| Retrieved Cases | Các bài tương tự được retrieve từ dataset RAG. |
| Trace | Log các tool mà LangGraph đã gọi. |
| Raw State | JSON state đầy đủ để debug. |

Điểm được model Qwen trả về theo band IELTS 0-9, làm tròn về nửa band. `Overall` không được train trực tiếp mà được tính từ trung bình 4 trait.

### Lỗi thường gặp

| Lỗi | Cách xử lý |
| --- | --- |
| `Không tìm thấy export model Qwen TraitStack` | Kiểm tra Drive có `inference_config.json`, `trait_stacks_and_features.pt`, `qwen_lora_adapter/`, `tokenizer/`. Đặt vào `MyDrive/ielts_model_exports/`. |
| `Không tìm thấy file train CSV để build retrieval database` | Upload `ielts_train_aug_df.csv` hoặc đổi `full_concat_ds/train.csv` thành `ielts_train_df2.csv`. |
| CUDA out of memory | Dùng Colab L4/A100, restart runtime, đóng notebook khác, giảm input essay quá dài. |
| Hugging Face 401/403 | Login Hugging Face và đảm bảo tài khoản có quyền tải model. |
| Gradio không hiện link | Chạy lại cell cuối, kiểm tra `share=True`, hoặc restart runtime rồi chạy lại từ đầu. |
| Feedback quá chậm | L4 chạy được nhưng chậm hơn A100. Đợi vài phút cho lần đầu vì model và embedding cần warm up. |

## Quy ước lưu kết quả

- Không ghi đè kết quả cũ nếu đang so sánh nhiều cấu hình.
- Tên file nên có model, prompt/essay set, split, seed và biến thể phương pháp.
- Với LCES/pairwise, giữ raw judgment, parse status và consistency flag để debug.
- Với supervised regression, lưu cả prediction chi tiết và bảng metric tổng hợp.
- Với IELTS demo, nếu xuất thêm log hoặc report, nên lưu riêng theo ngày chạy hoặc tên notebook để tránh nhầm với benchmark ASAP.
