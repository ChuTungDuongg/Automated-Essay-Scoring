## ASAP Benchmarking

Repository này tập trung vào các thí nghiệm chấm điểm bài viết tự động, đặc biệt là benchmark trên bộ dữ liệu **ASAP** và **ASAP++**. Phần quan trọng nhất của repo hiện tại là thư mục `benchmark_asap/`, nơi lưu các notebook chạy baseline supervised learning, zero-shot LLM, LCES và MTS.

Ngoài benchmark ASAP, repo còn có các notebook demo cho IELTS Writing Task 2: huấn luyện mô hình chấm điểm, inference điểm số, retrieval và sinh feedback.

## Điểm nhanh

- `benchmark_asap/`: notebook benchmark chính cho ASAP.
- `dataset/asap/`: dữ liệu ASAP đã tiền xử lý và chia train/val/test.
- `dataset/asap++/`: dữ liệu ASAP++ theo từng prompt.
- `benchmark_results/ASAP/`: kết quả benchmark đã xuất ra CSV.
- `CS338_DemoToolUse/`: pipeline IELTS Writing Task 2, gồm training, inference, baseline và full inference.
- `references/`: paper tham khảo cho các phương pháp được tái lập.

## Cấu trúc thư mục

```text
.
├── benchmark_asap/
│   ├── supervised_learning/
│   │   ├── TF-IDF-SVR-Ridge.ipynb
│   │   ├── BiLSTM-Attention.ipynb
│   │   ├── Longformer-Regression.ipynb
│   │   ├── deBerta-v3-large-Regression.ipynb
│   │   ├── End2End_Longformer_XHPDF_Colab.ipynb
│   │   └── XHPDF_Reproduction_Colab.ipynb
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
├── dataset/
│   ├── asap/
│   ├── asap++/
│   ├── hugging_face_dataset/
│   ├── augmented_dataset/
│   └── full_concat_ds/
├── CS338_DemoToolUse/
├── ASAP/
├── ASAP++/
└── references/
```

## Benchmark ASAP

Thư mục `benchmark_asap/` được chia thành hai nhóm thí nghiệm.

### Supervised learning

Các notebook trong `benchmark_asap/supervised_learning/` huấn luyện mô hình trên dữ liệu ASAP có nhãn và đánh giá bằng QWK, MAE, RMSE.

| Notebook | Vai trò |
| --- | --- |
| `TF-IDF-SVR-Ridge.ipynb` | Baseline truyền thống dùng TF-IDF với Ridge/SVR. |
| `BiLSTM-Attention.ipynb` | Baseline neural sequence model với BiLSTM và attention. |
| `Longformer-Regression.ipynb` | Fine-tune Longformer cho bài viết dài. |
| `deBerta-v3-large-Regression.ipynb` | Fine-tune DeBERTa-v3-large cho regression. |
| `End2End_Longformer_XHPDF_Colab.ipynb` | Pipeline Longformer end-to-end theo hướng XHPDF. |
| `XHPDF_Reproduction_Colab.ipynb` | Tái lập XHPDF-style với discourse/features/embedding. |

Kết quả tương ứng nằm trong:

```text
benchmark_results/ASAP/Supervised_learning/
```

Các file `*_results.csv` chứa metric tổng hợp theo prompt/essay set. Các file `*_predictions.csv` chứa dự đoán chi tiết.

### Zero-shot learning

Các notebook trong `benchmark_asap/zeroshot_learning/` đánh giá LLM không fine-tune trực tiếp trên nhãn ASAP.

| Nhóm | Vai trò |
| --- | --- |
| `vanilla_mistral_zero_shot_baseline_*` | Baseline zero-shot trực tiếp bằng Mistral cho prompt 1-8. |
| `MTS_Mistral7B_Lite_1turn.ipynb` | MTS phiên bản nhẹ, một lượt gọi model. |
| `MTS_Mistral7B_Full_2turn_paper_split.ipynb` | MTS đầy đủ, hai lượt, theo split của paper. |
| `LCES_Reproduction/` | Tái lập LCES theo official rubrics, chia prompt 1-4 và 5-8. |
| `LCES_Reproduction_UpgradePairwise/` | LCES transductive pairwise nâng cấp, mỗi notebook xử lý một prompt. |

Kết quả zero-shot nằm trong:

```text
benchmark_results/ASAP/ZeroShot/
├── lces/
└── mts/
```

## Dữ liệu ASAP

Các file ASAP chính nằm trong `dataset/asap/`:

| File | Mục đích |
| --- | --- |
| `training_set_rel3.tsv` / `training_set_rel3.xls` | Dữ liệu gốc ASAP. |
| `training_set_rel3_preprocessed.csv` | Bản đã tiền xử lý. |
| `asap_train.csv` | Split train. |
| `asap_val.csv` | Split validation. |
| `asap_test.csv` | Split test. |

Các notebook benchmark thường dùng các cột:

- `essay_id`: mã bài viết.
- `essay_set`: prompt/essay set ASAP, từ 1 đến 8.
- `essay`: nội dung bài viết.
- `domain1_score`: điểm gốc.
- `domain1_score_norm`: điểm đã chuẩn hóa về khoảng 0-1.

## Kết quả hiện có

Một số kết quả tổng hợp đã có sẵn trong `benchmark_results/ASAP/` để đọc lại mà không cần chạy toàn bộ notebook.

| Nhóm | File chính | Metric đáng chú ý |
| --- | --- | --- |
| TF-IDF + Ridge/SVR | `experiment1_tfidf_svr_ridge_results.csv` | Average test QWK khoảng 0.667-0.670. |
| BiLSTM-Attention | `experiment2_bilstm_attention_results.csv` | Average test QWK khoảng 0.670. |
| Longformer | `experiment3_longformer_results.csv` | Average test QWK khoảng 0.789. |
| DeBERTa-v3-large | `experiment4_deberta_v3_large_results.csv` | Average test QWK khoảng 0.676. |
| MTS Lite | `mts_lite_mistral7b_qwk_average_fixed_splits.csv` | Average QWK over prompts: 0.573. |
| MTS Full | `full_mts_mistral7b_qwk_average_paper_split.csv` | Average QWK over prompts: 0.530. |
| LCES | `ZeroShot/lces/p*_judgments.csv` | Lưu pairwise judgments cho từng prompt. |

Ghi chú: với các mô hình regression, một số notebook lưu cả `test_qwk_round` và `test_qwk`. Khi báo cáo kết quả, nên ghi rõ dùng cách làm tròn trực tiếp hay threshold optimization trên validation.

## Cách chạy benchmark

Khuyến nghị chạy trên Google Colab hoặc máy có GPU CUDA, đặc biệt với Longformer, DeBERTa và Mistral 7B.

1. Chuẩn bị dữ liệu ASAP:

```text
dataset/asap/asap_train.csv
dataset/asap/asap_val.csv
dataset/asap/asap_test.csv
```

2. Mở notebook cần chạy trong `benchmark_asap/`.

3. Kiểm tra lại các biến đường dẫn trong notebook. Một số notebook đang dùng đường dẫn kiểu Colab như:

```text
/content/asap_train.csv
/content/asap_val.csv
/content/asap_test.csv
```

Khi chạy local, cần đổi các đường dẫn này sang `dataset/asap/...`.

4. Chạy tuần tự các cell từ đầu notebook.

5. Lưu kết quả mới vào `benchmark_results/ASAP/` với tên file mới nếu muốn so sánh nhiều cấu hình.

## Môi trường gợi ý

Repo chủ yếu là notebook, nên không có một entrypoint duy nhất. Các thư viện thường gặp:

```text
python>=3.10
jupyterlab
pandas
numpy
scikit-learn
scipy
torch
transformers
datasets
sentence-transformers
accelerate
peft
tqdm
```

Với notebook zero-shot dùng Mistral, cần thêm quyền truy cập model trên Hugging Face nếu model yêu cầu xác thực.

## Luồng đọc repo đề xuất

Nếu mục tiêu là benchmark ASAP, hãy đọc theo thứ tự:

1. `dataset/asap/` để nắm format dữ liệu và split.
2. `benchmark_asap/supervised_learning/TF-IDF-SVR-Ridge.ipynb` để hiểu baseline đơn giản.
3. `benchmark_asap/supervised_learning/Longformer-Regression.ipynb` hoặc `deBerta-v3-large-Regression.ipynb` để xem fine-tuning transformer.
4. `benchmark_asap/zeroshot_learning/MTS_Mistral7B_Lite_1turn.ipynb` để xem zero-shot trait scoring.
5. `benchmark_asap/zeroshot_learning/LCES_Reproduction/` để xem pairwise ranking theo LCES.
6. `benchmark_results/ASAP/` để đối chiếu metric và prediction đã xuất.

## Phần IELTS demo

Ngoài benchmark ASAP, `CS338_DemoToolUse/` chứa các notebook cho bài toán IELTS Writing Task 2:

| Thư mục | Nội dung |
| --- | --- |
| `score_training/` | Huấn luyện mô hình chấm điểm. |
| `score_inference/` | Suy luận điểm số. |
| `full_inference/` | Pipeline đầy đủ gồm scoring, retrieval và feedback. |
| `baseline/` | Baseline backbone khác nhau. |

Notebook demo đáng chú ý:

- `CS338_DemoToolUse/score_training/qwen_3b_test_8.ipynb`
- `CS338_DemoToolUse/score_inference/test_7_inference.ipynb`
- `CS338_DemoToolUse/full_inference/test_7_inference_retriever_full_zero_mistral_tool_use_automatic_16_best_LANGGRAPH_A100.ipynb`

## Quy ước lưu kết quả

- Không ghi đè kết quả cũ nếu đang so sánh nhiều cấu hình.
- Tên file nên thể hiện model, prompt/essay set, split, seed và biến thể phương pháp.
- Với LCES/pairwise, nên giữ cả judgment raw text, parse status và consistency flag để debug.
- Với supervised regression, nên lưu cả prediction chi tiết và bảng metric tổng hợp.
